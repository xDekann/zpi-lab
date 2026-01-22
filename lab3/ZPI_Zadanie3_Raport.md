## 1. Wstęp

### 1.1 Cel dokumentu
Celem niniejszego raportu jest przedstawienie:
- transformacji wymagań z SRS na Product Backlog (epiki → historyjki → zadania),
- roadmapy (oś czasu) obejmującej zakres **MVP** na semestr,
- szczegółowego planu **Sprintu 1** (2 tygodnie), skoncentrowanego na **PoC** kluczowej funkcjonalności.

### 1.2 Definicje i skróty
- **Epic** – duży obszar funkcjonalny / cel biznesowy.
- **User Story** – historyjka użytkownika w formacie „Jako…, chcę…, aby…”.  
- **PoC (Proof of Concept)** – minimalny prototyp do weryfikacji hipotezy wykonalności.
- **DoD (Definition of Done)** – wspólne kryteria ukończenia pracy.

---

## 2. Część A – Product Backlog i Roadmapa

### 2.1 Epiki (MVP)
| ID | Epic | Opis (1–2 zdania) | Priorytet |
|----|------|-------------------|:---------:|
| ZPI&#8209;5 | Core – Silnik generowania planu (WF-PLAN) | Epik obejmuje implementację kluczowego silnika aplikacji odpowiedzialnego za automatyczne generowanie harmonogramu zwiedzania na podstawie parametrów wejściowych użytkownika oraz danych z otwartych źródeł. Stanowi fundament systemu i jest realizowany w pierwszej kolejności jako Proof of Concept. | High |
| ZPI&#8209;13 | Wizualizacja planu i mapa (WF-INFO) | Epik koncentruje się na czytelnej prezentacji wygenerowanego planu podróży w formie listy chronologicznej oraz interaktywnej mapy, umożliwiającej podgląd atrakcji i ich lokalizacji. | Medium |
| ZPI&#8209;21 | Edycja planu i logistyka podróży (WF-MOD, WF-NOC) | Epik obejmuje podstawowe mechanizmy edycji wygenerowanego planu oraz obsługę logistyki dnia, takie jak usuwanie i przenoszenie atrakcji oraz start i koniec dnia z placeholderami noclegowymi. | Medium |

### Backlog produktu (Jira)

![Backlog produktu - epiki i historyjki](./Backlog-Jira.png)
*Rys. 1. Widok Product Backlog w narzędziu Jira z podziałem na epiki i historyjki użytkownika.*


### 2.2 Roadmapa (oś czasu – tylko epiki)

![Roadmap epiców](./Epic-Timeline.png)
*Rys. 2. Widok Timeline Epików w narzędziu Jira.*

## 3. Część B – Planowanie Sprintu 1 (2 tygodnie) na podstawie PoC

### 3.1 Opis PoC
### PoC 1 – Automatyczne pobranie atrakcji i ułożenie trasy (core algorytmu)

#### Hipoteza
Da się automatycznie:
- Pobrać atrakcje z OpenTripMap dla miasta,
- Ułożyć je w logicznej kolejności zwiedzania na podstawie odległości (routing),
- Zrobić z tego sensowny jednodniowy plan w formie od atrakcji do atrakcji, gdzie każda atrakcja zawiera opis

#### Co sprawdzamy technicznie
- Czy API OpenTripMap daje wystarczająco dobre dane (POI, kategorie, współrzędne),
- Czy da się szybko policzyć kolejność zwiedzania (np. najbliższy sąsiad / OSRM),
- Czy wynik jest użyteczny (czas, dystans, kolejność).

#### Minimalny prototyp (opis koncepcji)
Backend (np. Python/Java):
- Endpoint: `/v1/plan?city=Rome&tags=museum,park`

Odpowiedź endpointu (pojedynczy POI):
```json
{
  "name": "Colosseum",
  "description": "Ancient Roman amphitheater",
  "lat": 41.8902,
  "lon": 12.4922,
  "order": 1,
  "distance_to_next": 450
}
```

Kroki:
1. Zapytanie do OpenTripMap → lista POI
2. Filtrowanie po tagach
3. Wywołanie OSRM (Open Source Routing Machine) → macierz odległości między POI
4. Prosty algorytm kolejności (nearest neighbor) w oparciu o macierz odległości z wywołania OSRM
5. Zwrócenie JSON: lista punktów w kolejności + dystanse (odpowiedź API)

#### Eksperyment
- Wejście: Rzym, 1 dzień, preferencje „muzea”
- Mierzymy lub zapisujemy:
  1. Czas odpowiedzi
  2. Odpowiedź API (JSON)
  3. Całkowitą długość trasy

Dla każdego wywołania wyliczamy minimalną sumę odległości ze zwróconych punktów - wywołanie powtarzamy N razy (np. 1000). Przy okazji sprawdzamy, czy za każdym razem uzyskane są wszystkie atrybuty w odpowiedzi API.

#### Kryteria sukcesu
Dla każdego wywołania:
- Plan generuje się **< 5s**
- Gdy całkowita długość trasy nie przekracza 1,5-krotności minimalnej możliwej sumy odległości potrzebnej do połączenia wszystkich wybranych atrakcji w jedną spójną strukturę (minimalne drzewo rozpinające), przy założeniu, że każda para atrakcji ma znaną odległość między sobą.
- Każdy punkt ma **nazwę, opis i lokalizację**

#### Możliwe scenariusze i decyzje
- **Scenariusz pozytywny:** Plan generuje się szybko, trasa jest logiczna, wszystkie POI mają poprawne dane.  
  **Decyzja:** PoC udany → dalszy rozwój (np. UI, lepszy algorytm optymalizacji trasy).
- **Scenariusz częściowo udany:** Plan działa, ale czas jest zbyt długi lub trasa nie zawsze logiczna.  
  **Decyzja:** usprawnienia algorytmu, limit POI, lepsze filtrowanie, optymalizacja wywołań OSRM.
- **Scenariusz negatywny:** Plan nie generuje się poprawnie lub OTM/OSRM nie dostarcza danych.  
  **Decyzja:** zmiana źródła danych, modyfikacja podejścia do routingu, ewentualne zawieszenie projektu.

---

### 3.2 Cel Sprintu (SMART)
**Cel sprintu (SMART):**  
„W ciągu jednego sprintu opracować prototyp systemu automatycznego generowania jednodniowego planu zwiedzania dla dowolnego miasta, wykorzystującego OpenTripMap i OSRM, który pobiera atrakcje zgodnie z preferencjami użytkownika, filtruje je po kategoriach, ustala logiczną kolejność odwiedzania punktów na podstawie odległości i czasów przejścia, a następnie generuje plan w formacie JSON gotowym do prezentacji. Sukces mierzymy czasem generowania planu (<5s), spójnością trasy (łączna odległość ≤1,5× minimalnej możliwej), oraz kompletnością danych każdego POI (nazwa, opis, lokalizacja).”

### 3.3 Tablica sprintu

![Tablica Sprintu](./Sprint-Table.png)
*Rys. 3. Widok pierwszego Sprintu w narzędziu Jira.*

![Cel Sprintu](./Sprint-Goal.png)
*Rys. 4. Widok celu biznesowego Sprintu w narzędziu Jira.*

### 3.4 Definition of Done (DoD)

---

## 4. Pre-Mortem dla PoC (ryzyka + plan zapobiegawczy)

### 4.1 Założenie „porażki”
Zakładamy, że wdrożenie PoC zakończyło się niepowodzeniem. Poniżej spisujemy możliwe przyczyny (techniczne, organizacyjne, bezpieczeństwa) oraz plan zapobiegawczy.


## 5. Załączniki

### 5.1 Linki
- [**Jira projekt**](https://zpi-zad3.atlassian.net/jira/software/projects/ZPI/summary)
- [**Repozytorium**](https://github.com/xDekann/zpi-lab)
