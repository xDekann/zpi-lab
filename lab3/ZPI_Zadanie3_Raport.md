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
|---:|------|-------------------|:---------:|
| EP-01 | \<nazwa\> | \<opis\> | High/Med/Low |
| EP-02 | \<nazwa\> | \<opis\> | High/Med/Low |
| EP-03 | \<nazwa\> | \<opis\> | High/Med/Low |
| EP-04 | \<nazwa\> | \<opis\> | High/Med/Low |

> **Screenshot:** backlog epików z Jira – wklej tutaj (lub link).  
> `![Backlog epików](./img/epics.png)`

### 2.2 Roadmapa (oś czasu – tylko epiki)
Wstaw wizualizację (Gantt) dla epików – np. eksport z Jira lub zrzut ekranu.

> **Screenshot:** roadmapa epików  
> `![Roadmapa](./img/roadmap.png)`

## 3. Część B – Planowanie Sprintu 1 (2 tygodnie) na podstawie PoC

### 3.1 Opis PoC
### PoC 1 – Automatyczne pobranie atrakcji i ułożenie trasy (core algorytmu)

#### Hipoteza
Da się automatycznie:
- Pobrać atrakcje z OpenTripMap dla miasta,
- Przefiltrować je po kategoriach,
- Ułożyć je w logicznej kolejności zwiedzania na podstawie odległości (routing),
- Zrobić z tego sensowny jednodniowy plan w formie POI -> POI.

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
- Mierzymy:
    1. Czas odpowiedzi
    2. Liczbę atrakcji
    3. Łączny czas przejść
    4. Suma odległości między POI
    5. Odległość w linii prostej między pierwszym POI a ostatnim POI

#### Kryteria sukcesu
- Plan generuje się **< 5s**
- Trasa zwiedzania jest uznana za niechaotyczną, jeśli jej całkowita długość nie przekracza **1,5× minimalnej możliwej sumy odległości** potrzebnej do połączenia wszystkich wybranych atrakcji w jedną spójną strukturę (przy założeniu znanych odległości między każdą parą atrakcji).
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

> **Screenshot:** tablica sprintu (To Do / In Progress / Done)  
> `![Tablica sprintu](./img/sprint-board.png)`

### 3.4 Definition of Done (DoD)

---

## 4. Pre-Mortem dla PoC (ryzyka + plan zapobiegawczy)

### 4.1 Założenie „porażki”
Zakładamy, że wdrożenie PoC zakończyło się niepowodzeniem. Poniżej spisujemy możliwe przyczyny (techniczne, organizacyjne, bezpieczeństwa) oraz plan zapobiegawczy.


## 5. Załączniki

### 5.1 Linki
- Jira project: \<link\>
- Repozytorium: \<link\>