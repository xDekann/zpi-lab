## 1. Wstęp

### 1.1 Cel

Niniejszy dokument stanowi specyfikację wymagań oprogramowania (SRS - Software Requirements Specification) dla **Systemu
Automatycznego Planowania Podróży w wersji 1.0**.

Głównym celem dokumentu jest precyzyjne i jednoznaczne zdefiniowanie wymagań funkcjonalnych oraz niefunkcjonalnych,
które system musi spełnić, aby zrealizować założenia biznesowe i potrzeby użytkowników końcowych. Dokument ten pełni
rolę kontraktu projektowego i jest przeznaczony dla:

- zespołu deweloperskiego: jako podstawa do projektowania architektury, implementacji kodu oraz integracji z
  zewnętrznymi API (OpenStreetMap, Wikivoyage),
- testerów: jako baza do tworzenia scenariuszy testowych i kryteriów akceptacji,
- interesariuszy i inwestorów: w celu monitorowania postępów prac i weryfikacji zgodności produktu z wizją biznesową,
- project managerów: do priorytetyzacji zadań i zarządzania zakresem projektu (MVP vs rozwój późniejszy).

Dokument powinien być używany jako główne źródło prawdy o zachowaniu systemu w całym cyklu życia projektu (SDLC).

### 1.2 Wizja, zakres i cele produktu:

#### Wizja

*Stworzyć inteligentnego, cyfrowego asystenta podróży, który w kilkanaście sekund przekształca mglisty pomysł na wyjazd
w kompletny, spersonalizowany i logistycznie spójny harmonogram. Naszym celem jest zniesienie barier w planowaniu
podróży – chcemy, aby każdy, niezależnie od budżetu czy doświadczenia, mógł odkrywać świat bez stresu związanego z
wielogodzinnym researchem, korzystając z potęgi otwartych danych.*

#### Zakres systemu

System jest aplikacją webową (z możliwością rozwoju do PWA), która automatyzuje proces tworzenia planów wycieczek. Do
głównych zadań systemu należy:

- agregacja i przetwarzanie danych: pobieranie informacji o atrakcjach turystycznych (POI) z otwartych źródeł (
  OpenTripMap, OpenStreetMap, Wikivoyage) oraz ich kategoryzacja,
- inteligentne generowanie harmonogramu: tworzenie dziennych planów zwiedzania na podstawie parametrów użytkownika (
  lokalizacja, data, budżet, preferencje: "muzea", "natura", "kulinaria"), z uwzględnieniem logicznej kolejności
  geograficznej,
- zarządzanie logistyką dnia: obsługa "startu" i "końca" dnia, sugerowanie lokalizacji noclegowych oraz obsługa
  scenariuszy "noclegu w podróży",
- interaktywna edycja: umożliwienie użytkownikowi modyfikacji wygenerowanego planu (usuwanie punktów, przenoszenie
  między dniami, dodawanie własnych),
- ekosystem afiliacyjny: prezentacja ofert komercyjnych (noclegi, bilety) dopasowanych do planu, z przekierowaniem do
  partnerów zewnętrznych,
- personalizacja i VIP: obsługa kont użytkowników, zapisywanie planów oraz udostępnianie wariantów tras (np. "
  spokojna", "intensywna") dla subskrybentów VIP.

#### Cele biznesowe i cele użytkowników (KPIs & Kryteria Akceptacji)

| Typ Celu        | Opis Celu                                                                                                   | Mierzalne Kryterium Akceptacji (KPI)                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Użytkownika** | **Szybkość planowania:** użytkownik chce drastycznie skrócić czas potrzebny na przygotowanie planu wyjazdu. | **KPI-USR-01:** System wygeneruje kompletny plan podróży (dla zakresu do 7 dni) w czasie poniżej 5 sekund od zatwierdzenia formularza.                               |
| **Użytkownika** | **Użyteczność:** plan musi być logiczny i wykonalny geograficznie.                                          | **KPI-USR-02:** 90% wygenerowanych atrakcji w ramach jednego dnia znajduje się w promieniu pozwalającym na dotarcie do nich (pieszo/komunikacją) w założonym czasie. |
| **Biznesowy**   | **Monetyzacja (afiliacja):** generowanie przychodu poprzez przekierowania do partnerów (booking, atrakcje). | **KPI-BIZ-01:** Minimum 5% użytkowników przeglądających plan kliknie w link zewnętrzny ("Zobacz ofertę" / "Zarezerwuj").                                             |
| **Biznesowy**   | **Konwersja na VIP:** zachęcenie użytkowników do zakupu subskrypcji dla lepszych funkcji.                   | **KPI-BIZ-02:** Osiągnięcie konwersji na poziomie 2% użytkowników darmowych przechodzących na płatny plan VIP w skali miesiąca.                                      |
| **Biznesowy**   | **Niski koszt utrzymania:** oparcie systemu o darmowe dane.                                                 | **KPI-BIZ-03:** 100% danych o atrakcjach turystycznych pochodzi z darmowych/otwartych źródeł (brak opłat licencyjnych za dane w MVP).                                |

#### Poza zakresem

Aby uniknąć rozszerzania zakresu (scope creep), definiuje się, że w wersji 1.0 system **NIE** będzie:

- Przetwarzał płatności za usługi turystyczne: system nie jest agentem turystycznym ani pośrednikiem finansowym i
  transakcje odbywają się na stronach partnerów. Płatności wewnątrz aplikacji dotyczą wyłącznie subskrypcji VIP.
- Działał jako nawigacja "zakręt po zakręcie": aplikacja wskazuje lokalizację na mapie, ale nie prowadzi użytkownika
  głosowo jak Google Maps czy Waze.
- Działał w trybie offline: generowanie planu i dostęp do szczegółów wymaga aktywnego połączenia z Internetem (API
  first).
- Obsługiwał rezerwacji biletów lotniczych/kolejowych: skupiamy się na planie pobytu i noclegach, a nie na transporcie "
  do" i "z" miejsca docelowego (poza opcją oznaczenia "noclegu w podróży").

### 1.3. Definicje, akronimy i skróty

Poniższy słowniczek zapewnia spójne rozumienie terminologii użytej w dokumencie SRS:

- Algorytm Planowania – logika biznesowa aplikacji odpowiedzialna za dobór atrakcji (na podstawie wag/preferencji) i ich
  sortowanie geograficzne (np. problem komiwojażera).
- Atrakcja / POI (Point of Interest) – punkt na mapie mający wartość turystyczną (muzeum, park, zabytek), pobierany z
  baz Open Data.
- Open Data – dane dostępne publicznie bez ograniczeń praw autorskich (lub na licencjach otwartych jak ODbL), np.
  OpenStreetMap.
- Afiliacja – model współpracy, w którym aplikacja otrzymuje prowizję za przekierowanie użytkownika na stronę partnera (
  np. serwisu rezerwacyjnego).
- Placeholder noclegu – element interfejsu w planie dnia, przypominający użytkownikowi o konieczności wyboru miejsca
  noclegowego, bez narzucania konkretnego hotelu.
- VIP – status konta użytkownika, który wykupił subskrypcję, uprawniający do korzystania z zaawansowanych funkcji (np.
  warianty tras "Rodzinna", "Intensywna").
- MVP (Minimum Viable Product) – wersja produktu posiadająca tylko funkcjonalności niezbędne do zaspokojenia
  podstawowych potrzeb użytkownika i zebrania feedbacku.
- RWD (Responsive Web Design) – technika projektowania strony www, tak aby jej układ dostosowywał się do rozmiaru okna
  urządzenia (desktop, tablet, smartfon).
- PWA (Progressive Web App) – aplikacja internetowa, która może być zainstalowana na urządzeniu użytkownika i działać
  podobnie do aplikacji natywnej (np. powiadomienia, ikona na pulpicie), ale oparta jest na technologiach webowych (
  HTML, CSS, JS).
- SDLC (Software Development Life Cycle) – cykl życia oprogramowania, proces stosowany przez zespoły programistyczne do
  projektowania, tworzenia i testowania wysokiej jakości oprogramowania.

### 1.4. Przegląd dokumentu

Dalsza część dokumentu jest zorganizowana w następujący sposób:

- Rozdział 1 (Wstęp): określa cel dokumentu, wizję produktu, zakres systemu, słownik pojęć oraz strukturę niniejszej
  specyfikacji.
- Rozdział 2 (Opis Ogólny): przedstawia szerszy kontekst produktu, charakterystykę użytkowników (Persony), środowisko
  operacyjne oraz główne ograniczenia projektowe i technologiczne.
- Rozdział 3 (Wymagania Dotyczące Interfejsów): opisuje interfejsy użytkownika (makiety, stylistyka) oraz interfejsy
  programowe (API zewnętrzne, integracje).
- Rozdział 4 (Wymagania Funkcjonalne): stanowi rdzeń specyfikacji. Zawiera szczegółowe scenariusze użycia (User Stories)
  oraz kryteria akceptacji (w formacie Given-When-Then) dla modułów: planowania, noclegów, edycji, ofert oraz konta
  użytkownika.
- Rozdział 5 (Atrybuty Jakościowe): definiuje wymagania niefunkcjonalne, takie jak wydajność, bezpieczeństwo danych,
  dostępność i skalowalność systemu.
- Rozdział 6 (Analiza Wymagań): zawiera analizę porównawczą z konkurencją oraz uzasadnienie przyjętych rozwiązań.

### 3.1. Interfejsy Użytkownika (UI)

#### 3.1.1. Ogólny styl i koncepcja
Interfejs aplikacji zaprojektowano z wykorzystaniem jasnej palety barw (dominujący błękit oraz biel), co zapewnia czytelność i nowoczesny wygląd. Zastosowano spójną typografię bezszeryfową oraz wyraźne odstępy, aby uniknąć przytłoczenia użytkownika informacjami.

Kluczowe założenia UX:
*   **Hierarchia informacji:** najważniejsze elementy (mapa, przyciski akcji) są wizualnie wyróżnione.
*   **Intuicyjne sterowanie:** wykorzystanie znanych wzorców, takich jak suwaki zakresu czy ikony edycji/usuwania.
*   **Dostępność:** kontrastowe przyciski akcji takie jak "Generuj plan" lub "Nawiguj".

#### 3.1.2. Opis ekranów (makiety)

Poniższe makiety prezentują realizację głównego scenariusza użycia systemu: konfiguracji, przez przegląd planu, po szczegóły atrakcji.

**Ekran 1: Konfigurator podróży**

![Wireframe 1](./Wireframe%20-%201.png)

Ekran startowy służący do zebrania danych wejściowych (zgodnie z **WF-PLAN**).
*   **Lokalizacja:** pole tekstowe do wprowadzenia miejsca docelowego.
*   **Budżet:** suwak zakresowy pozwalający określić widełki cenowe (np. 30-1000 zł).
*   **Preferencje:** system tagów, umożliwiający szybki wybór kategorii (Muzea, Jedzenie, Natura, Architektura).
*   **Termin:** kalendarz pozwalający zaznaczyć przedział dat wyjazdu.
*   **Akcja:** przycisk "Generuj plan" umieszczony na dole ekranu (łatwo dostępny na urządzeniach mobilnych).

**Ekran 2: Widok planu i mapy**

![Wireframe 2](./Wireframe%20-%202.png)

Główny widok, realizujący wymagania **WF-PLAN**, **WF-MOD** oraz **WF-AFFIL**.
*   **Mapa:** interaktywna mapa (OpenStreetMap) z naniesioną trasą i ponumerowanymi punktami (markerami), co pozwala na szybką orientację przestrzenną.
*   **Sekcja wyboru noclegu:** dedykowany moduł prezentujący oferty partnerów, dopasowane do lokalizacji, realizujący funkcję monetyzacji i wsparcia logistycznego (**WF-AFFIL**).
*   **Lista atrakcji:** chronologiczna lista punktów planu. Każda karta zawiera:
    *   Przedział czasowy wizyty.
    *   Nazwę i tagi kategorii.
    *   **Narzędzia edycji (WF-MOD):** ikony pozwalające na podgląd szczegółów (ikona "i"), edycję godziny (ołówek) oraz usunięcie punktu z planu (kosz).

**Ekran 3: Szczegóły atrakcji**

![Wireframe 3](./Wireframe%20-%203.png)

Widok szczegółowy, dostępny po kliknięciu w atrakcję lub ikonę informacji (**WF-INFO**, **WF-OFERTA**).
*   **Multimedia:** galeria zdjęć (główne zdjęcie oraz miniatury).
*   **Opis merytoryczny:** sekcja tekstowa zawierająca opis miejsca, adres oraz godziny otwarcia.
*   **Pasek akcji:** pasek u dołu ekranu z kluczowymi przyciskami:
    1.  **"Nawiguj":** uruchamia zewnętrzną aplikację mapową.
    2.  **"Kup bilet":** przycisk afiliacyjny kierujący do zewnętrznego systemu rezerwacji (realizacja **WF-OFERTA**).

### 3.2. Interfejsy programowe (API)

| Nazwa Interfejsu | Opis i cel integracji | Wykorzystanie na makietach |
| :--- | :--- | :--- |
| **OpenTripMap/Wikidata** | Pobieranie nazw, opisów, godzin otwarcia i kategorii atrakcji. | Widoczne jako dane na kartach (ekran 2) i opisy (ekran 3). |
| **OpenStreetMap** | Wyświetlanie podkładu mapowego. | Widoczna mapa Amsterdamu (ekran 2). |
| **Routing API (np. OSRM)** | Wyznaczanie kolejności punktów i rysowanie linii trasy na mapie. | Widoczna przerywana linia trasy łącząca punkty 1-2-3-4 na mapie. |
| **API partnerów afiliacyjnych** | Pobieranie ofert promowanych (hotele, bilety). | Sekcja wyboru noclegu (ekran 2) oraz przycisk "Kup bilet" (ekran 3). |
