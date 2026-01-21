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

> **Screenshot:** tablica sprintu (To Do / In Progress / Done)  
> `![Tablica sprintu](./img/sprint-board.png)`

### 3.4 Definition of Done (DoD)

**Epik:** PoC Core - silnik generowania planu (WF-PLAN)

---

#### 1. Kryteria Funkcjonalne
- **Pobieranie i filtrowanie danych:** System poprawnie pobiera dane POI i aplikuje filtry po tagach.
- **Logika tworzenia trasy:** Algorytm wyznacza optymalną kolejność zwiedzania z uwzględnieniem geolokalizacji i okien czasowych (PoC: trasa ≤1,5× minimalnej).
- **Parametry trasy:** Każdy wygenerowany plan zawiera szacowany czas dotarcia i czas pobytu w POI.

#### 2. Warstwa API (v1/plan)
- **Endpoint:** Interfejs `/v1/plan` przyjmuje żądania i zwraca poprawną strukturę danych JSON zawierającą POI w odpowiedniej kolejności zwiedzania.
- **Obsługa błędów (ZPI-41):** System zwraca kody 4xx/5xx w przypadku braku danych, błędnych parametrów lub awarii silnika.
- **Dokumentacja:** Kontrakt API jest opisany w standardzie OpenAPI/Swagger.

#### 3. Walidacja Techniczna i Jakość
- **Integracja:** Testy integracyjne przechodzą pomyślnie w środowisku deweloperskim.
- **Jakość kodu:** Kod przeszedł procedurę Code Review i nie zawiera krytycznych błędów.
- **Testy:** Pokrycie testami jednostkowymi kluczowej logiki algorytmu wynosi min. 80%.

#### 4. Akceptacja Końcowa
- Kod został scalony do gałęzi głównej.
- Funkcjonalność została zweryfikowana i zaakceptowana przez osobę decyzyjną.

**Epik:** Prezentacja harmonogramu i interaktywna mapa (WF-INFO)

---

#### 1. Wygląd i Działanie (UI/UX)
- **Mapa:** Interaktywna mapa (OpenStreetMap) poprawnie wyświetla trasę zwiedzania i wszystkie punkty (POI).
- **Lista:** Harmonogram jest czytelny, podzielony na konkretne dni i ułożony chronologicznie (oś czasu).
- **Szczegóły:** Po kliknięciu w atrakcję pojawia się okno lub widok z opisem i zdjęciami miejsca.
- **Synchronizacja:** najechanie myszką na element listy podświetla odpowiadający mu punkt na mapie (i na odwrót).
- **Responsywność:** Widok mapy i listy dopasowuje się do ekranu telefonu i komputera.

#### 2. Obsługa Danych i Błędów
- **Błędne dane:** Jeśli system otrzyma niepełne dane (np. brak współrzędnych POI), wyświetla komunikat o błędzie zamiast "rozsypanego" widoku.
- **Weryfikacja wizualna:** Na mapie widać gołym okiem, że atrakcje są blisko siebie (zgodnie z logiką geograficzną).

#### 3. Jakość Techniczna
- **Kontrola kodu:** Przynajmniej dwie osoby z zespołu sprawdziły kod pod kątem błędów i czytelności.
- **Czystość zapisu:** Kod nie zawiera niepotrzebnych powtórzeń i jest zgodny z przyjętymi standardami zespołu.
- **Wydajność:** Mapa ładuje się płynnie, a przełączanie między dniami nie powoduje zawieszania aplikacji.

#### 4. Akceptacja Końcowa
- Funkcjonalność została przetestowana na różnych urządzeniach (telefon, laptop).
- Wszystkie zmiany zostały zapisane w głównym folderze projektu.
- Wynik prac został pokazany i zaakceptowany przez osobę decyzyjną.


**Epik:** Mechanizmy modyfikacji planu i zarządzanie logistyką (WF-MOD, WF-NOC)

---

#### 1. Funkcje edycji (WF-MOD)
- **Zmiana kolejności:** Użytkownik może swobodnie zmieniać kolejność atrakcji, a system ponownie tworzy trasę zwiedzania.
- **Usuwanie:** Użytkownik może usunąć niechcianą atrakcję z planu dnia.
- **Pełna kontrola:** Po edycji plan pozostaje spójny i użytkownik ma nad nim pełną władzę.

#### 2. Zarządzanie logistyką (WF-NOC)
- **Ramy dnia:** Użytkownik może zdefiniować godzinę startu i zakończenia zwiedzania każdego dnia.
- **Obsługa noclegów:** System pozwala na wskazanie miejsca noclegu (start/koniec dnia w konkretnym punkcie).
-  **Placeholder:** Interfejs pozwala zarezerwować czas na nieplanowane aktywności.

#### 3. Jakość techniczna i środowisko
- **Stabilność:** Edycja planu nie powoduje błędów w działaniu mapy ani harmonogramu.
- **Przegląd:** Kod przeszedł przegląd pod kątem logiki przeliczania trasy po zmianach użytkownika.

#### 4. Akceptacja Końcowa
- **Testy użytkownika:** Sprawdzono, czy przesuwanie i usuwanie elementów jest intuicyjne i szybkie.
- **Wdrożenie:** Wszystkie funkcje są scalone z głównym kodem projektu.
- **Zatwierdzenie:** Funkcje edycji i logistyki zostały zaakceptowane przez osobę decyzyjną.


## 4. Pre-Mortem dla PoC (ryzyka + plan zapobiegawczy)

### 4.1 Założenie „porażki”
Zakładamy, że wdrożenie PoC zakończyło się niepowodzeniem. Poniżej spisujemy możliwe przyczyny (techniczne, organizacyjne, bezpieczeństwa) oraz plan zapobiegawczy.


## 5. Załączniki

### 5.1 Linki
- Jira project: \<link\>
- Repozytorium: \<link\>