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
| **Użytkownika** | **Szybkość planowania:** użytkownik chce znacząco skrócić czas potrzebny na przygotowanie planu wyjazdu.    | **KPI-USR-01:** System wygeneruje kompletny plan podróży (dla zakresu do 7 dni) w czasie poniżej 5 sekund od zatwierdzenia formularza.                               |
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

## 2. Opis Ogólny

System Automatycznego Planowania Podróży jest aplikacją webową, której architektura funkcjonalna opiera się na czterech
głównych modułach. Poniższe zestawienie stanowi ogólny zarys funkcjonalności, które zostały szczegółowo zdefiniowane w 
Rozdziale 4 (Wymagania Funkcjonalne).

### 2.1. Główne Funkcje Produktu

* **Moduł Inteligentnego Planowania:**
    - Agregacja danych o atrakcjach (POI) z otwartych źródeł (OpenTripMap, OpenStreetMap, Wikivoyage).
    - Automatyczne generowanie harmonogramu na podstawie parametrów: lokalizacja, data, budżet i preferencje (np. muzea, natura).
    - Algorytmiczne układanie logicznej kolejności zwiedzania (rozwiązywanie problemu komiwojażera w kontekście turystycznym).
* **Moduł Zarządzania Logistyką i Edycją:**
    - Obsługa ram czasowych dnia: "Start dnia" i "Koniec dnia" powiązane z lokalizacją noclegu.
    - Interaktywna edycja planu: usuwanie punktów, przenoszenie atrakcji między dniami (Drag & Drop), dodawanie własnych punktów.
    - Obsługa scenariuszy specjalnych, takich jak "nocleg w podróży" (np. pociąg nocny).
* **Moduł Komercyjny i Afiliacyjny:**
    - Prezentacja ofert noclegowych i biletowych dopasowanych do wygenerowanej trasy.
    - Mechanizm Deep Linking: przekierowanie użytkownika bezpośrednio do zewnętrznych partnerów (Booking, systemy rezerwacyjne) w celu finalizacji transakcji.
    - Sekcje "Polecane" wyróżniające partnerów afiliacyjnych.
* **Moduł Konta i Personalizacji (VIP):**
    - Rejestracja i logowanie użytkowników w celu zapisywania planów.
    - Obsługa subskrypcji VIP, odblokowującej zaawansowane warianty tras (np. "trasa intensywna", "trasa spokojna") oraz rozszerzone bazy POI.

### 2.2. Klasy Użytkowników

W systemie zidentyfikowano trzy główne klasy użytkowników końcowych, różniące się poziomem uprawnień oraz dostępem do funkcji premium.
1. Niezarejestrowany użytkownik standardowy (gość)**:
    - Osoba szukająca szybkiego, darmowego planu podróży. Często jest to "niezależny turysta" (FIT - Free Independent Traveler). Chce sprawdzić nową aplikację bez rejestracji.
    - Może wygenerować podstawowy plan, edytować go, korzystać z linków afiliacyjnych.
    - Nie może zapisywać planów ani korzystać z wariantów tras, traci dostęp do planów po zamknięciu sesji.
    - Brak dostępu do alternatywnych wariantów tras i zaawansowanych filtrów.
    - Limit generowanych planów na sesję, maksymlanie 1 plan dziennie.
2. **Zarejestrowany użytkownik standardowy**:
    - Użytkownik, który założył konto w aplikacji, aby zapisywać i zarządzać swoimi planami podróży.
    - Ma dostęp do wszystkich funkcji darmowych, w tym zapisywania planów i historii.
    - Może korzystać z podstawowych funkcji edycji i personalizacji planów.
    - Nie ma dostępu do funkcji premium (warianty tras, rozszerzone bazy POI).
3. **Użytkownik VIP**:
   - Użytkownik wymagający, ceniący wysoką personalizację lub specyficzny styl podróżowania (np. rodziny z dziećmi, "slow travel").
   - Wszystkie funkcje użytkownika standardowego plus dostęp do wariantów tras, brak reklam oraz priorytetowe rekomendacje.

**Persony Użytkowników**

Persona A: Tomasz "Backpacker" (Użytkownik Standardowy):
- Wiek: 24 lata
- Status: student, podróżuje budżetowo.
- Cel: chce zwiedzić Berlin w 3 dni, wydając jak najmniej. Nie chce tracić czasu na czytanie przewodników.
- Potrzeba: szybkie wygenerowanie logicznej trasy, aby zobaczyć "topowe" atrakcje i znaleźć tani hostel.
- Cytat: "Mam bilet na jutro, potrzebuję gotowego planu w kilka minut, żeby nie błądzić z plecakiem."

Persona B: Anna "Komfort i Rodzina" (Użytkownik VIP)
- Wiek: 38 lat
- Status: pracująca mama dwójki dzieci.
- Cel: planuje rodzinny wyjazd do Rzymu.
- Potrzeba: unikanie tłumów i pośpiechu. Potrzebuje wariantu trasy "Spokojna/Rodzinna", który uwzględnia wolniejsze tempo i atrakcje przyjazne dzieciom. Jest gotowa zapłacić za VIP, by zaoszczędzić stresu.
- Cytat: "Nie chcę biegać od muzeum do muzeum. Chcę planu, który pozwoli nam spokojnie zjeść lody i zobaczyć miasto bez marudzenia dzieci. Jestem gotowa zapłacić, żeby mieć pewność, że będzie to plan dostosowany do naszych potrzeb."

### 2.3. Ograniczenia Projektowe i Implementacyjne

#### Ograniczenia Technologiczne
- Architektura: brak budżetu na serwery wymusza korzystanie z darmowych planów chmurowych (free tier) o restrykcyjnych limitach sprzętowych (niska pamięć RAM, współdzielone vCPU).
  Wymusza to pełną konteneryzację (Docker) i optymalizację aplikacji, aby działała płynnie na minimalnych zasobach i była łatwa do przeniesienia w przypadku wyczerpania limitów danego dostawcy.
- Bezpieczeństwo: wymuszenie protokołu HTTPS (TLS 1.3) oraz polityki Content Security Policy (CSP) dla ochrony danych przesyłanych między frontendem a backendem.
- Zależność od API: system jest całkowicie zależny od dostępności zewnętrznych, otwartych źródeł danych (OpenTripMap, OSM). Awaria tych serwisów wymaga działania mechanizmów cache.
- API First: aplikacja wymaga stałego połączenia z internetem do generowania planów i pobierania szczegółów (brak trybu offline dla generowania).

#### Ograniczenia Organizacyjne
- Budżet: projekt realizowany jest jako MVP (Minimum Viable Product) przy minimalnym budżecie, co wymusza korzystanie wyłącznie z darmowych danych (Open Data) bez opłat licencyjnych.
- Zasoby ludzkie: zespół deweloperski funkcjonuje jako "Small Agile Team" (4-7 osób). Ze względu na sztywny termin wejścia na rynek, zakres prac został ograniczony do kluczowych funkcjonalności. Złożone moduły zostały wykluczone z zakresu MVP na rzecz integracji z gotowymi rozwiązaniami zewnętrznymi.
- Metodyka pracy: prace prowadzone są w metodyce Scrum z dwutygodniowymi sprintami, co narzuca konieczność dostarczania przyrostu funkcjonalnego w krótkich odstępach czasu i priorytetyzację zadań według wartości biznesowej

#### Ograniczenia Prawne i Środowiskowe
- Licencje danych: konieczność przestrzegania licencji ODbL (Open Database License) przy wykorzystaniu danych z OpenStreetMap (wymagane atrybucje).
- RODO (GDPR): ochrona danych osobowych użytkowników (e-mail, hasła) podczas rejestracji konta i zakupu subskrypcji VIP.
- Regulaminy Partnerów: implementacja linków afiliacyjnych musi być zgodna z warunkami świadczenia usług partnerów zewnętrznych (np. zakaz "cloakingu" linków).

### 2.4. Założenia Projektowe
- Płatności zewnętrzne: zakłada się, że wszelkie transakcje związane z rezerwacją noclegów i biletów odbywają się poza systemem. Aplikacja nie przetwarza danych kart kredytowych dla usług turystycznych.
- Dokładność danych Open Data: zakłada się, że dane pobierane z OpenTripMap/Wikivoyage są wystarczająco dokładne i aktualne dla celów turystycznych MVP. Ewentualne błędy w danych (np. nieaktualne godziny otwarcia) są niezależne od systemu.
- Dostępność użytkownika: zakłada się, że użytkownik posiada urządzenie mobilne lub desktopowe z nowoczesną przeglądarką internetową obsługującą HTML5 i geolokalizację.
- Model przychodowy: zakłada się, że konwersja na linki afiliacyjne (min. 5%) oraz subskrypcje VIP (2%) będą wystarczające do utrzymania serwerów w fazie produkcyjnej.

## 3. Wymagania Dotyczące Interfejsów Zewnętrznych

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


## 4. Wymagania Funkcjonalne

### WF-PLAN: Generowanie planu podróży na podstawie parametrów użytkownika
**Opis:** System automatycznie tworzy wielodniowy plan podróży na podstawie miejsca wyjazdu, liczby dni, budżetu i preferencji (np. „muzea”, „natura”, „jedzenie”, „punkty widokowe”), wykorzystując otwarte źródła danych (np. OpenStreetMap, OpenTripMap, Wikivoyage).

**Historyjka Użytkownika:**
- Jako użytkownik,
- chcę podać podstawowe parametry wyjazdu,
- aby otrzymać gotowy, logiczny plan zwiedzania na każdy dzień.

**Cel Biznesowy:** Skrócenie czasu planowania i zwiększenie wartości aplikacji poprzez szybkie dostarczenie użytecznego harmonogramu.

**Warunki Wstępne:** Użytkownik ma dostęp do formularza planowania (aplikacja uruchomiona).

**Warunki Końcowe:** Plan zostaje wygenerowany i wyświetlony jako harmonogram dzienny.

**Kryteria Akceptacji:**
- **WF-PLAN-01: Generowanie planu (Scenariusz Główny)**
    - *Given:* Użytkownik podał miejsce wyjazdu, liczbę dni, budżet i co najmniej 1 preferencję.
    - *When:* Użytkownik wybierze opcję „Generuj plan”.
    - *Then:* System wyświetli plan podzielony na dni.
    - *And:* Każdy dzień zawiera listę atrakcji w logicznej kolejności zwiedzania.
    - *And:* Dla każdej atrakcji system pokaże nazwę, krótki opis i lokalizację (np. adres / współrzędne / punkt na mapie).

- **WF-PLAN-02: Brak wyników dla preferencji (Scenariusz Alternatywny)**
    - *Given:* Użytkownik wybrał preferencje, dla których w danej lokalizacji nie ma wystarczającej liczby atrakcji.
    - *When:* Użytkownik uruchomi generowanie planu.
    - *Then:* System wygeneruje plan z częściowym pokryciem preferencji (system pokryje część z grupy podanych preferencji)
    - *And:* System wyświetli komunikat, że część preferencji nie mogła zostać spełniona i zaproponuje rozszerzenie kategorii (np. „ogólne atrakcje”).

- **WF-PLAN-03: Niedostępne źródło danych (Scenariusz Wyjątkowy)**
    - *Given:* Jedno ze źródeł danych (np. API atrakcji) jest chwilowo niedostępne.
    - *When:* Użytkownik uruchomi generowanie planu.
    - *Then:* System spróbuje wygenerować plan w oparciu o pozostałe źródła.
    - *And:* System poinformuje użytkownika, że plan może być niepełny.

- **WF-PLAN-04: Brak preferencji (Scenariusz Alternatywny)**
    - *Given:* Użytkownik podał miejsce wyjazdu, liczbę dni i budżet, ale nie wybrał żadnej preferencji.
    - *When:* Użytkownik wybierze opcję „Generuj plan”.
    - *Then:* System wygeneruje plan na podstawie ogólnych/popularnych atrakcji dla danej lokalizacji.
    - *And:* System wyświetli plan podzielony na dni.
    - *And:* Każdy dzień zawiera listę atrakcji w logicznej kolejności zwiedzania.
    - *And:* System wyświetli informację, że plan został wygenerowany bez preferencji (np. „Nie wybrano preferencji — pokazano popularne atrakcje.”).

---
### WF-NOC: Nocleg w planie podróży (placeholder + uzupełnienie + alternatywy)

**Opis:** System umożliwia wyświetlenie finalnego planu podróży łącznie z noclegiem w układzie dziennym, tj. każdy dzień zawiera elementy startu i końca dnia powiązane z noclegiem. Podczas generowania planu system dodaje placeholder noclegu, który użytkownik może uzupełnić wybierając nocleg z listy. Dodatkowo użytkownik może świadomie zrezygnować z noclegu lub wskazać „nocleg w podróży” (np. pociąg nocny).

**Historyjka Użytkownika:**
- Jako użytkownik,
- chcę widzieć plan podróży łącznie z noclegiem (start i koniec dnia),
- aby mieć kompletny, łatwy do realizacji harmonogram,
- oraz mieć możliwość wskazania, że noclegu nie potrzebuję (np. podróż nocą).

**Cel Biznesowy:** Zapewnienie „finalnego” planu (atrakcje + logistyka dnia) oraz elastyczności dla różnych stylów podróżowania (w tym podróże nocne).

**Warunki Wstępne:** Użytkownik podał parametry podróży i wygenerował plan.

**Warunki Końcowe:** Plan jest wyświetlany z informacją o noclegu (lub świadomym brakiem noclegu / noclegiem w podróży).

**Kryteria Akceptacji:**

- **WF-NOC-01: Placeholder noclegu w wygenerowanym planie (Scenariusz Główny)**
    - *Given:* Użytkownik podał miejsce wyjazdu i liczbę dni (oraz opcjonalnie budżet i preferencje).
    - *When:* Użytkownik wybierze opcję „Generuj plan”.
    - *Then:* System wyświetli plan podzielony na dni.
    - *And:* Każdy dzień zawiera element **„Start dnia”** oraz **„Koniec dnia”**.
    - *And:* W elementach „Start dnia” i „Koniec dnia” system wyświetli placeholder (np. „Wybierz nocleg” / „Brak wybranego noclegu”).
    - *And:* Placeholder zawiera akcję „Wybierz nocleg”, która przenosi użytkownika do listy noclegów.

- **WF-NOC-02: Uzupełnienie placeholdera noclegiem z listy (Scenariusz Główny)**
    - *Given:* Plan zawiera placeholder noclegu oraz dostępna jest lista noclegów.
    - *When:* Użytkownik wybierze nocleg z listy i wybierze akcję „Dodaj do planu”.
    - *Then:* System uzupełni placeholder w planie wybranym noclegiem.
    - *And:* Dla każdego dnia system wyświetli „Start dnia: wyjście z noclegu <Nazwa>” oraz „Koniec dnia: powrót do noclegu <Nazwa>”.
    - *And:* System wyświetli co najmniej nazwę noclegu, lokalizację oraz link „Zobacz ofertę” (otwierany zewnętrznie).

- **WF-NOC-03: Plan bez noclegu (Scenariusz Alternatywny)**
    - *Given:* Użytkownik ma wygenerowany plan zawierający placeholder noclegu.
    - *When:* Użytkownik wybierze opcję „Nie dodawaj noclegu”.
    - *Then:* System oznaczy elementy „Start dnia” i „Koniec dnia” jako **„Brak noclegu”**.
    - *And:* System nie wymaga wyboru noclegu do wyświetlenia i edycji planu.

- **WF-NOC-04: Nocleg w podróży (np. pociąg nocny) (Scenariusz Alternatywny)**
    - *Given:* Użytkownik ma plan obejmujący co najmniej 2 dni.
    - *When:* Użytkownik wybierze opcję „Nocleg w podróży” i wskaże środek transportu oraz relację (np. „pociąg”, A → B).
    - *Then:* System doda w planie na końcu odpowiedniego dnia element „Podróż nocna: A → B (pociąg)”.
    - *And:* System oznaczy kolejny dzień jako rozpoczęty w lokalizacji B, bez klasycznego noclegu.

---

### WF-INFO: Podgląd szczegółów atrakcji i nawigacja po planie
**Opis:** Użytkownik może przeglądać szczegóły atrakcji w planie (opis, kategoria, lokalizacja) oraz nawigować po dniach i elementach planu.

**Historyjka Użytkownika:**
- Jako użytkownik,
- chcę kliknąć atrakcję w planie,
- aby zobaczyć jej szczegóły i lepiej zdecydować, czy mi pasuje.

**Cel Biznesowy:** Zwiększenie przejrzystości planu i zaufania do rekomendacji.

**Warunki Wstępne:** Plan został wygenerowany.

**Warunki Końcowe:** Użytkownik widzi szczegóły wybranej atrakcji i może wrócić do planu.

**Kryteria Akceptacji:**
- **WF-INFO-01: Podgląd atrakcji (Scenariusz Główny)**
    - *Given:* Użytkownik ma wygenerowany plan.
    - *When:* Użytkownik kliknie w atrakcję.
    - *Then:* System wyświetli panel/ekran szczegółów atrakcji.
    - *And:* Panel zawiera co najmniej nazwę, krótki opis i lokalizację.

- **WF-INFO-02: Brak opisu w danych (Scenariusz Alternatywny)**
    - *Given:* Dla atrakcji brakuje opisu w źródle danych.
    - *When:* Użytkownik otworzy szczegóły atrakcji.
    - *Then:* System wyświetli komunikat „Brak opisu” oraz nadal pokaże nazwę i lokalizację.

---

### WF-MOD: Modyfikacja wygenerowanego planu (dodawanie, usuwanie, przenoszenie)
**Opis:** Użytkownik może edytować plan: usuwać elementy, dodawać nowe oraz przenosić atrakcje między dniami, zachowując kontrolę nad ostatecznym harmonogramem.

**Historyjka Użytkownika:**
- Jako użytkownik,
- chcę edytować plan po wygenerowaniu,
- abym mógł dopasować go do własnego stylu zwiedzania.

**Cel Biznesowy:** Zwiększenie satysfakcji użytkownika i dopasowania planu bez ponownego generowania od zera.

**Warunki Wstępne:** Plan został wygenerowany.

**Warunki Końcowe:** Plan zostaje zaktualizowany i zapisany w bieżącej sesji aplikacji.

**Kryteria Akceptacji:**
- **WF-MOD-01: Usunięcie atrakcji z dnia (Scenariusz Główny)**
    - *Given:* Użytkownik ma plan z listą atrakcji w danym dniu.
    - *When:* Użytkownik wybierze opcję „Usuń” przy atrakcji.
    - *Then:* Atrakcja znika z harmonogramu danego dnia.
    - *And:* System nie usuwa innych elementów planu.

- **WF-MOD-02: Przeniesienie atrakcji między dniami (Scenariusz Główny)**
    - *Given:* Użytkownik ma plan obejmujący co najmniej 2 dni.
    - *When:* Użytkownik przeniesie atrakcję do innego dnia (np. drag&drop lub „Przenieś do dnia…”).
    - *Then:* Atrakcja pojawi się w docelowym dniu.
    - *And:* Atrakcja zniknie z dnia źródłowego.

- **WF-MOD-03: Dodanie atrakcji spoza planu (Scenariusz Alternatywny)**
    - *Given:* Użytkownik przegląda listę dodatkowych atrakcji dla lokalizacji.
    - *When:* Użytkownik kliknie „Dodaj do planu” i wybierze dzień.
    - *Then:* System doda atrakcję do wskazanego dnia planu.

- **WF-MOD-04: Konflikt (ten sam punkt dodany wielokrotnie) (Scenariusz Wyjątkowy)**
    - *Given:* Atrakcja już znajduje się w planie.
    - *When:* Użytkownik spróbuje dodać tę samą atrakcję ponownie.
    - *Then:* System wyświetli ostrzeżenie „Ta atrakcja jest już w planie”.
    - *And:* System nie dubluje wpisu (chyba że użytkownik jawnie wybierze „Dodaj mimo to”).
- **WF-MOD-05: Edycja godziny atrakcji / wypełnianie okienek (Scenariusz Alternatywny)**
    - *Given:* Użytkownik ma wygenerowany plan, w którym atrakcje w danym dniu mają przypisaną godzinę.
    - *When:* Użytkownik edytuje godzinę atrakcji **albo** usuwa atrakcję tworząc wolne okienko czasowe.
    - *Then:* System zaktualizuje harmonogram dnia zgodnie ze zmianą (nowa godzina / widoczne okienko).
    - *And:* Jeśli powstanie okienko, system umożliwi użytkownikowi dodanie nowej atrakcji do okienka z podaniem godziny.
    - *And:* Jeśli nowa godzina powoduje konflikt (nakładanie się atrakcji), system wyświetli ostrzeżenie i poprosi o korektę godziny.

---

### WF-OFERTA: Lista noclegów i atrakcji z linkiem zewnętrznym
**Opis:** Aplikacja prezentuje listę pasujących noclegów i atrakcji oraz umożliwia przejście do zewnętrznej strony (np. rezerwacyjnej) przez prosty link.

**Historyjka Użytkownika:**
- Jako użytkownik,
- chcę zobaczyć pasujące noclegi i propozycje atrakcji,
- aby móc szybko przejść do rezerwacji poza aplikacją.

**Cel Biznesowy:** Dostarczenie wartości użytkownikowi oraz możliwość monetyzacji (np. afiliacja) bez blokowania wyboru.

**Warunki Wstępne:** Użytkownik ma ustawioną lokalizację podróży (z planu lub ręcznie).

**Warunki Końcowe:** Użytkownik otwiera zewnętrzną stronę obiektu/oferty w przeglądarce.

**Kryteria Akceptacji:**
- **WF-OFERTA-01: Wyświetlenie listy podmiotów (atrakcje/noclegi) (Scenariusz Główny)**
    - *Given:* Użytkownik ma wybraną lokalizację podróży.
    - *When:* Użytkownik otworzy zakładkę odpowiadającą podmiotowi.
    - *Then:* System wyświetli listę propozycji.
    - *And:* Każda pozycja zawiera nazwę oraz przycisk/link „Zobacz ofertę”.

- **WF-OFERTA-02: Przekierowanie do oferty zewnętrznej (Scenariusz Główny)**
    - *Given:* Użytkownik widzi listę ofert.
    - *When:* Użytkownik kliknie „Zobacz ofertę”.
    - *Then:* System otworzy zewnętrzną stronę w przeglądarce (lub w WebView).

- **WF-OFERTA-03: Brak ofert (Scenariusz Alternatywny)**
    - *Given:* Dla lokalizacji nie zwrócono żadnych ofert.
    - *When:* Użytkownik otworzy sekcję „Noclegi” lub „Oferty”.
    - *Then:* System wyświetli komunikat „Brak pasujących ofert” i sugestię zmiany filtrów (np. budżetu).

---

### WF-AFFIL: Oznaczanie i ekspozycja partnerów afiliacyjnych (Polecane/Popularne)
**Opis:** System może wyróżniać obiekty partnerów (np. noclegi/atrakcje) w sekcjach „Polecane” lub „Popularne”, bez ukrywania pozostałych wyników.

**Historyjka Użytkownika:**
- Jako użytkownik,
- chcę widzieć przejrzyście, które miejsca są wyróżnione,
- aby zachować kontrolę i zaufanie do rekomendacji.

**Cel Biznesowy:** Umożliwienie monetyzacji afiliacyjnej przy zachowaniu transparentności.

**Warunki Wstępne:** Istnieje lista ofert dla lokalizacji.

**Warunki Końcowe:** Użytkownik widzi wyróżnione oferty z jasnym oznaczeniem.

**Kryteria Akceptacji:**
- **WF-AFFIL-01: Sekcja „Polecane” (Scenariusz Główny)**
    - *Given:* System posiada oferty od partnerów afiliacyjnych dla lokalizacji.
    - *When:* Użytkownik otworzy listę ofert.
    - *Then:* System wyświetli sekcję „Polecane” nad wynikami standardowymi.
    - *And:* Oferty w „Polecane” są oznaczone etykietą (np. „Polecane/Partner”).

- **WF-AFFIL-02: Brak partnerów (Scenariusz Alternatywny)**
    - *Given:* Brak ofert partnerskich dla lokalizacji.
    - *When:* Użytkownik otworzy listę ofert.
    - *Then:* System nie wyświetla sekcji „Polecane” i pokazuje standardową listę.

---

### WF-KONTO: Konto użytkownika (rejestracja) oraz zakup VIP

**Opis:** System umożliwia użytkownikowi rejestrację i korzystanie z konta, aby zapisywać ustawienia (np. warianty planu) oraz wykupić dostęp VIP. Po zakupie VIP system nadaje użytkownikowi status VIP i udostępnia funkcje premium (np. dodatkowe warianty tras).

**Historyjka Użytkownika:**
- Jako użytkownik,
- chcę móc założyć konto,
- aby mieć dostęp do funkcji wymagających identyfikacji użytkownika oraz móc wykupić VIP.

**Cel Biznesowy:** Umożliwienie personalizacji i monetyzacji aplikacji (VIP).

**Warunki Wstępne:** Użytkownik ma dostęp do ekranu rejestracji/logowania i aplikacja ma dostęp do internetu.

**Warunki Końcowe:** Użytkownik ma utworzone konto (oraz opcjonalnie aktywny status VIP), który jest widoczny w aplikacji.

**Kryteria Akceptacji:**

- **WF-KONTO-01: Rejestracja użytkownika (Scenariusz Główny)**
    - *Given:* Użytkownik nie jest zalogowany i widzi formularz rejestracji.
    - *When:* Użytkownik poda wymagane dane rejestracyjne (np. e-mail i hasło) i zatwierdzi rejestrację.
    - *Then:* System utworzy konto użytkownika.
    - *And:* System zaloguje użytkownika automatycznie lub umożliwi logowanie bez ponownego podawania danych.
    - *And:* System wyświetli potwierdzenie poprawnej rejestracji.

- **WF-KONTO-02: Rejestracja z użyciem zajętego adresu e-mail (Scenariusz Wyjątkowy)**
    - *Given:* Użytkownik próbuje zarejestrować konto na adres e-mail, który już istnieje w systemie.
    - *When:* Użytkownik zatwierdzi rejestrację.
    - *Then:* System wyświetli komunikat o konflikcie (np. „Konto z tym e-mailem już istnieje”).
    - *And:* System zaproponuje przejście do logowania lub resetu hasła (jeśli przewidziane w MVP).

- **WF-KONTO-03: Zakup VIP (Scenariusz Główny)**
    - *Given:* Użytkownik jest zalogowany i nie posiada statusu VIP.
    - *When:* Użytkownik wybierze opcję „Kup VIP” i zakończy proces płatności.
    - *Then:* System oznaczy konto użytkownika jako VIP.
    - *And:* System natychmiast odblokuje funkcje VIP (np. warianty tras) w aplikacji.
    - *And:* System wyświetli potwierdzenie aktywacji VIP.

- **WF-KONTO-04: Nieudana płatność VIP (Scenariusz Wyjątkowy)**
    - *Given:* Użytkownik rozpoczął proces zakupu VIP.
    - *When:* Płatność zostanie odrzucona lub przerwana.
    - *Then:* System nie nada statusu VIP.
    - *And:* System wyświetli komunikat o niepowodzeniu i umożliwi ponowienie próby.

- **WF-KONTO-05: Widoczny status VIP (Scenariusz Główny)**
    - *Given:* Użytkownik jest zalogowany.
    - *When:* Użytkownik otworzy ekran profilu/ustawień (lub inny obszar aplikacji, gdzie status jest prezentowany).
    - *Then:* System pokaże aktualny status konta (VIP / standard).

---

### WF-VIP: Funkcje wersji VIP (warianty tras i rozszerzone rekomendacje)
**Opis:** Wersja VIP udostępnia dodatkowe warianty planu (np. „intensywny”, „spokojny”, „rodzinny”) oraz rozszerzone propozycje atrakcji/miejsc w ramach tej samej logiki działania.

**Historyjka Użytkownika:**
- Jako użytkownik VIP,
- chcę otrzymać dodatkowe warianty trasy,
- aby dopasować plan do tempa i stylu podróżowania.

**Cel Biznesowy:** Monetyzacja przez rozszerzenie funkcji bez komplikowania podstawowego doświadczenia.

**Warunki Wstępne:** Użytkownik ma aktywny status VIP.

**Warunki Końcowe:** Użytkownik widzi i może zastosować alternatywny wariant planu.

**Kryteria Akceptacji:**
- **WF-VIP-01: Wybór wariantu planu (Scenariusz Główny)**
    - *Given:* Użytkownik ma status VIP i wygenerowany plan.
    - *When:* Użytkownik wybierze wariant (np. „Spokojny”).
    - *Then:* System wygeneruje i wyświetli alternatywny plan dla tych samych parametrów.

- **WF-VIP-02: Użytkownik bez VIP (Scenariusz Alternatywny)**
    - *Given:* Użytkownik nie ma statusu VIP.
    - *When:* Użytkownik spróbuje użyć funkcji „Warianty tras”.
    - *Then:* System wyświetli ekran/komunikat informujący o dostępności funkcji w VIP.
    - *And:* System nie blokuje dostępu do standardowego planu.
### 4.1 Priorytetyzacja wymagań

W celu ustalenia kolejności realizacji wymagań funkcjonalnych zastosowano metodę priorytetyzacji opartą na porównaniu wartości biznesowej do kosztu i ryzyka implementacyjnego

Każde wymaganie zostało ocenione w czterech kategoriach:

- *Korzyść* – wartość funkcjonalności dla użytkownika końcowego
- *Kara* – konsekwencje braku funkcjonalności
- *Koszt* – nakład pracy potrzebny na implementację
- *Ryzyko* – niepewność techniczna lub projektowa

Oceny przyznano w skali Fibonacciego: 1, 2, 3, 5, 8, 13, 21. 

> Priorytet wymagania obliczany jest jako stosunek sumy korzyści i kary
> do sumy kosztu i ryzyka implementacyjnego.
>
> **Priorytet = (Korzyść + Kara) / (Koszt + Ryzyko)**

| ID | Wymaganie funkcjonalne | Korzyść | Kara | Koszt | Ryzyko | Priorytet |
|----|------------------------|---------:|------:|-------:|--------:|----------:|
| WF-PLAN | Generowanie planu podróży na podstawie parametrów użytkownika | 21 | 21 | 8 | 3 | 3.50 |
| WF-INFO | Podgląd szczegółów atrakcji i nawigacja po planie | 13 | 13 | 5 | 3 | 3.25 |
| WF-MOD | Edycja planu podróży (dodawanie, usuwanie, zmiana kolejności) | 13 | 8 | 8 | 5 | 1.62 |
| WF-OFERTA | Lista noclegów i atrakcji z linkami zewnętrznymi | 8 | 5 | 5 | 5 | 1.30 |
| WF-NOC | Uwzględnianie noclegów w planie podróży | 8 | 8 | 8 | 5 | 1.14 |
| WF-AFFIL | Oznaczanie i ekspozycja partnerów afiliacyjnych | 5 | 3 | 5 | 5 | 0.80 |
| WF-KONTO | Rejestracja konta użytkownika i zakup wersji VIP | 5 | 5 | 13 | 8 | 0.48 |
| WF-VIP | Zaawansowane funkcje planowania dostępne dla użytkowników VIP | 3 | 3 | 13 | 8 | 0.29 |

### Uzasadnienie przyznanych priorytetów

**WF-PLAN – Generowanie planu podróży**  
Funkcjonalność stanowi podstawę działania systemu i realizuje jego główny cel. Bez niej aplikacja nie byłaby użyteczna dla użytkownika, co uzasadnia najwyższą ocenę zarówno korzyści, jak i kary. Pomimo relatywnie wysokiego kosztu i ryzyka implementacji, jej znaczenie dla systemu przekłada się na najwyższy priorytet.

**WF-INFO – Podgląd szczegółów atrakcji i nawigacja po planie**  
Funkcjonalność znacząco zwiększa czytelność i użyteczność wygenerowanego planu, umożliwiając użytkownikowi zapoznanie się ze szczegółami zaproponowanych atrakcji. Jej brak obniżyłby komfort korzystania z systemu, jednak nie uniemożliwiłby jego działania, co uzasadnia wysoki, lecz niższy niż w przypadku WF-PLAN priorytet.

**WF-MOD – Edycja planu podróży**  
Możliwość modyfikowania wygenerowanego planu pozwala użytkownikowi dostosować go do własnych preferencji. Funkcjonalność ta jest istotna z punktu widzenia doświadczenia użytkownika, jednak jej implementacja wiąże się z większym kosztem oraz ryzykiem, wynikającym z konieczności zachowania spójności planu.

**WF-OFERTA – Lista ofert noclegów i atrakcji**  
Funkcjonalność uzupełniająca, umożliwiająca przejście do zewnętrznych serwisów rezerwacyjnych. Jej obecność zwiększa użyteczność systemu, jednak brak tej funkcji nie uniemożliwia realizacji podstawowego celu aplikacji.

**WF-NOC – Uwzględnianie noclegów w planie podróży**  
Funkcjonalność zwiększa kompletność planu podróży, szczególnie w przypadku wyjazdów wielodniowych. Ze względu na to, że nie jest niezbędna do podstawowego działania systemu, otrzymała umiarkowany priorytet.

**WF-AFFIL – Oznaczanie i ekspozycja partnerów afiliacyjnych**  
Funkcjonalność ma charakter biznesowy i wspiera potencjalną monetyzację systemu poprzez wyróżnianie partnerów afiliacyjnych. Jej brak nie wpływa bezpośrednio na możliwość wygenerowania i edycji planu podróży, dlatego uzyskała niższy priorytet niż funkcje związane z planowaniem. Jednocześnie koszt i ryzyko implementacji są umiarkowane, ponieważ funkcjonalność ogranicza się do warstwy prezentacyjnej i oznaczeń.

**WF-KONTO – Rejestracja konta użytkownika i zakup wersji VIP**  
Funkcjonalność istotna z perspektywy dalszego rozwoju i monetyzacji systemu, jednak niewymagana na etapie pierwszej wersji aplikacji. Wysoki koszt oraz ryzyko implementacji obniżają jej priorytet.

**WF-VIP – Zaawansowane funkcje planowania dla użytkowników VIP**  
Stanowi rozszerzenie funkcjonalności systemu skierowane do węższej grupy użytkowników. Niewielki wpływ na podstawową funkcjonalność aplikacji oraz relatywnie wysoki koszt wdrożenia uzasadniają najniższy priorytet.

## 5. Atrybuty Jakościowe

Poniżej zdefioniowano atrybuty jakościowe, które opisują, w jaki sposób system ma docelowo działać. Poniższe cele są mierzalne, co ułatwia dalsze tworzenie aplikacji i umożliwi w przyszłości określenie postępów w tworzeniu i rozwoju aplikacji.

* **Wydajność (Performance):**
    * **WNF-WYD-01:** Aplikacja musi stać się w pełni interaktywna (użytkownik może kliknąć i przewijać) w czasie poniżej 5 sekund od rozpoczęcia ładowania, nawet przy złożonych (np. tygodniowych) planach.
* **Dostępność (Availability):**
    * **WNF-NIEZ-01:** System musi posiadać mechanizm pobierania danych z pamięci podręcznej (cache), umożliwiający wyświetlenie wcześniej wygenerowanych planów, nawet gdy zewnętrzne API (np. OpenTripMap) jest nieosiągalne.
* **Bezpieczeństwo (Security):**
    * **WNF-BEZ-01:** Połączenie między przeglądarką użytkownika a serwerem musi być wymuszone przez protokół HTTPS z certyfikatem TLS 1.3.
    * **WNF-BEZ-02:** Wszystkie dane wrażliwe użytkowników, takie jak tokeny sesyjne, adresy e-mail, czy hasła przesyłane między frontendem a backendem muszą być chronione przez politykę Content Security Policy (CSP), która uniemożliwia wykonywanie nieautoryzowanych skryptów (ataki XSS).
* **Skalowalność (Scalability):**
    * **WNF-SKAL-01:** System musi być w stanie automatycznie uruchomić dodatkowe instancje kontenerów w czasie poniżej 30 sekund w odpowiedzi na gwałtowny wzrost liczby użytkowników obecnie korzystających z aplikacji.

#### Jakość projektu

* **Modyfikowalność (Modifiability):**
    * **WNF-MOD-01:** System musi umożliwiać dodanie nowej wersji językowej interfejsu bez ingerencji w kod źródłowy, jedynie poprzez dołączenie nowego pliku z tłumaczeniami w formacie JSON lub YAML.
* **Przenośność (Portability):**
    * **WNF-PRZEN-01:** Całość infrastruktury aplikacji (backend, frontend, baza danych, cache) musi być zdefiniowana jako kod (Infrastructure as Code) w pliku `docker-compose.yml`, umożliwiając uruchomienie identycznego środowiska na systemach Windows, macOS oraz Linux.


### Kluczowe atrybuty niefunkcjonalne

Poniżej wyróżniono trzy najważniejsze atrybuty jakościowe dla naszego projektu:

#### 1. Modyfikowalność
System polega na zewnętrznych źródłach danych, więc ich struktura może często ulegać zmianie. Wysoka modyfikowalność pozwala na błyskawiczną adaptację do nowych wersji API lub łatwe podpięcie nowych partnerów afiliacyjnych bez odczuwalnych przerw w działaniu aplikacji.

#### 2. Wydajność
Użytkownik korzystający z aplikacji oczekuje responsywnej aplikacji, która będzie realizować polecenia użytkownika tuż po zleceniu działania. Optymalizacja szybkości generowania tras i responsywności całej aplikacji bezpośrednio wpływa na ilość wykorzystanych linków afiliacyjnych. Im szybciej działa aplikacja, tym mniejsza szansa na rezygnację użytkownika z korzystania z generowanego planu.

#### 3. Dostępność
Podróżujący często sprawdzają plany w miejscach z ograniczonym zasięgiem. Stabilność systemu i mechanizmy cache gwarantują, że użytkownik zawsze ma dostęp do swojego planu, co daje przewagę nad innymi rozwiązaniami oraz umożliwia częste korzystanie z aplikacji.

## 6. Analiza Porównawcza

W ramach analizy porównawczej zestawiono nasza aplikację razem z bezpośrednią konkurencją, czyli popularną aplikacją do planowania podróży Wanderlog oraz z konkurencją pośrednią, czyli Google Maps. Poniżej pokakano tabelę, w której porównano Funkcjonalność, UX, model biznesowy oraz wspracie zewnętrznych rozwiązań w wymienionych aplikacjach.

| **Kryterium Oceny** | **Nasza aplikacja** | **Wanderlog** | **Mapy Google** |
| :--- | :--- | :--- | :--- |
| **Planowanie trasy** | System sam układa plan dnia i trasę na bazie preferencji i budżetu. | Aplikacja pozwala na zapisywanie rezerwacji, notatek i ręczne tworzenie listy miejsc do odwiedzenia. | Aplikacja pozwala na lokalizację wybranych przez użytkownika miejsc i szukaniu drogi pomiędzy poszczególnymi punktami trasy. |
| **User Experience** | Minimalistyczny interfejs, natychmiastowy wynik bez nadmiarowej konfiguracji. | Rozbudowany interfejs z wieloma panelami, wymagający czasu na opanowanie. | Wygodne do nawigacji, ale nie najlepsze przy tworzeniu szczegółowego planu podróży. |
| **Optymalizacja trasy** | System rozwiązuje problem kolejności zwiedzania, tworząc efektywną trasę. | Użytkownik sam musi decydować, w jakiej kolejności odwiedzi wybrane miejsca. | Brak wsparcia w planowaniu chronologii całego dnia zwiedzania. |
| **Działanie aplikacji w trudnych warunkach** | Dostęp do wygenerowanego planu i map bez połączenia z siecią w darmowej wersji. | Możliwość przeglądania planów bez dostępu do internetu dostępna tylko w płatnym abonamencie. | Możliwość pobrania wybranego fragment mapy, w trybie offline niewygodny dostęp do zaplanowanych tras. |
| **Model Biznesowy** | Darmowy dostęp do planera, prowizje od rezerwacji oraz płatne warianty tras (VIP). | Wiele funkcji, takich jak plan podróży offline czy automatyczny import rezerwacji z GMaila, ukrytych za płatnym abonamentem. | Zarabianie na widoczności lokalnych firm i danych użytkowników. |
| **Kontrola Budżetu** | Użytkownik może określić swój budżet już na początkowym etapie planowania podróży i plan zwiedzania jest układany z uwzględnieniem podanej kwoty. |  Można podać swój budżet, niemniej podana kwota nie ma wpływu na plan podróży zapisany w aplikacji. | Ograniczone narzędzia do planowania kosztów podróży. Przy wyszukiwaniu pojedynczych miejsc jest możliwość podania budżetu. |
| **Wsparcie i wykorzystanie zewnętrznych rozwiązań** |  Oparcie na OpenStreetMap i OpenTripMap, odsyłanie do zewnętrznych portali rezerwacyjnych dzieki linkom afiliacyjnym. | Integracja z Gmail i komercyjnymi API dostawców map. | Pełna integracja z usługami Google, ogromna baza danych gromadzonych przez Google. |

**1. Co konkurencja robi dobrze?**

* Wanderlog dobrze sobie radzi z automatycznym importowaniem rezerwacji z poczty e-mail, dzięki czemu jest wygodnym miejscem do przechowywania informacji o wyjeździe w jednym miejscu.
* Mapy Google dostarczają najwięcej informacji odnośnie danych obiektów na mapie, potrafią również w czasie rzeczywistym optymalizować drogę z początku do końca trasy, np. dzięki śledzeniu aktualnego natężenia ruchu. 

**2. Jakie są słabe punkty konkurencji?**

* Największą słabością konkurecji jest konieczność samodzielnego wyboru kolejnych etapów podróży. Konkurencja udostępnia narzędzia do zaplanowania podróży (np. mapy, listy atrakcji), ale nie dostarcza gotowej trasy do zwiedzania.
* W Wanderlog tryb offline jest dodatkowo płatny, co potencjalnie zniechęca do korzystania w przypadku, gdy użytkownicy będą się znajdować w miejscach o ograniczonym dostępie do internetu. Dodatkowo budzęt jest tylko liczbą wpisaną przez użytkownika, aplikacja nie potrafi automatycznie odrzucić zbyt drogich atrakcji lub zaproponować tańszych alternatyw, by zmieścić się w ustalonym limicie.
* W mapach Google brak wsparcia dla tras pieszych w trybie offline znacząco obniża wartość aplikacji dla turystów poruszających się po mieście pieszo, czy rowerem.
