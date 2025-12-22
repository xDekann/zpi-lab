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
### WF-NIGHT: Nocleg w planie podróży (placeholder + uzupełnienie + alternatywy)

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

- **WF-NIGHT-01: Placeholder noclegu w wygenerowanym planie (Scenariusz Główny)**
    - *Given:* Użytkownik podał miejsce wyjazdu i liczbę dni (oraz opcjonalnie budżet i preferencje).
    - *When:* Użytkownik wybierze opcję „Generuj plan”.
    - *Then:* System wyświetli plan podzielony na dni.
    - *And:* Każdy dzień zawiera element **„Start dnia”** oraz **„Koniec dnia”**.
    - *And:* W elementach „Start dnia” i „Koniec dnia” system wyświetli placeholder (np. „Wybierz nocleg” / „Brak wybranego noclegu”).
    - *And:* Placeholder zawiera akcję „Wybierz nocleg”, która przenosi użytkownika do listy noclegów.

- **WF-NIGHT-02: Uzupełnienie placeholdera noclegiem z listy (Scenariusz Główny)**
    - *Given:* Plan zawiera placeholder noclegu oraz dostępna jest lista noclegów.
    - *When:* Użytkownik wybierze nocleg z listy i wybierze akcję „Dodaj do planu”.
    - *Then:* System uzupełni placeholder w planie wybranym noclegiem.
    - *And:* Dla każdego dnia system wyświetli „Start dnia: wyjście z noclegu <Nazwa>” oraz „Koniec dnia: powrót do noclegu <Nazwa>”.
    - *And:* System wyświetli co najmniej nazwę noclegu, lokalizację oraz link „Zobacz ofertę” (otwierany zewnętrznie).

- **WF-NIGHT-03: Plan bez noclegu (Scenariusz Alternatywny)**
    - *Given:* Użytkownik ma wygenerowany plan zawierający placeholder noclegu.
    - *When:* Użytkownik wybierze opcję „Nie dodawaj noclegu”.
    - *Then:* System oznaczy elementy „Start dnia” i „Koniec dnia” jako **„Brak noclegu”**.
    - *And:* System nie wymaga wyboru noclegu do wyświetlenia i edycji planu.

- **WF-NIGHT-04: Nocleg w podróży (np. pociąg nocny) (Scenariusz Alternatywny)**
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

### WF-EDIT: Modyfikacja wygenerowanego planu (dodawanie, usuwanie, przenoszenie)
**Opis:** Użytkownik może edytować plan: usuwać elementy, dodawać nowe oraz przenosić atrakcje między dniami, zachowując kontrolę nad ostatecznym harmonogramem.

**Historyjka Użytkownika:**
- Jako użytkownik,
- chcę edytować plan po wygenerowaniu,
- abym mógł dopasować go do własnego stylu zwiedzania.

**Cel Biznesowy:** Zwiększenie satysfakcji użytkownika i dopasowania planu bez ponownego generowania od zera.

**Warunki Wstępne:** Plan został wygenerowany.

**Warunki Końcowe:** Plan zostaje zaktualizowany i zapisany w bieżącej sesji aplikacji.

**Kryteria Akceptacji:**
- **WF-EDIT-01: Usunięcie atrakcji z dnia (Scenariusz Główny)**
    - *Given:* Użytkownik ma plan z listą atrakcji w danym dniu.
    - *When:* Użytkownik wybierze opcję „Usuń” przy atrakcji.
    - *Then:* Atrakcja znika z harmonogramu danego dnia.
    - *And:* System nie usuwa innych elementów planu.

- **WF-EDIT-02: Przeniesienie atrakcji między dniami (Scenariusz Główny)**
    - *Given:* Użytkownik ma plan obejmujący co najmniej 2 dni.
    - *When:* Użytkownik przeniesie atrakcję do innego dnia (np. drag&drop lub „Przenieś do dnia…”).
    - *Then:* Atrakcja pojawi się w docelowym dniu.
    - *And:* Atrakcja zniknie z dnia źródłowego.

- **WF-EDIT-03: Dodanie atrakcji spoza planu (Scenariusz Alternatywny)**
    - *Given:* Użytkownik przegląda listę dodatkowych atrakcji dla lokalizacji.
    - *When:* Użytkownik kliknie „Dodaj do planu” i wybierze dzień.
    - *Then:* System doda atrakcję do wskazanego dnia planu.

- **WF-EDIT-04: Konflikt (ten sam punkt dodany wielokrotnie) (Scenariusz Wyjątkowy)**
    - *Given:* Atrakcja już znajduje się w planie.
    - *When:* Użytkownik spróbuje dodać tę samą atrakcję ponownie.
    - *Then:* System wyświetli ostrzeżenie „Ta atrakcja jest już w planie”.
    - *And:* System nie dubluje wpisu (chyba że użytkownik jawnie wybierze „Dodaj mimo to”).
- **WF-EDIT-05: Edycja godziny atrakcji / wypełnianie okienek (Scenariusz Alternatywny)**
    - *Given:* Użytkownik ma wygenerowany plan, w którym atrakcje w danym dniu mają przypisaną godzinę.
    - *When:* Użytkownik edytuje godzinę atrakcji **albo** usuwa atrakcję tworząc wolne okienko czasowe.
    - *Then:* System zaktualizuje harmonogram dnia zgodnie ze zmianą (nowa godzina / widoczne okienko).
    - *And:* Jeśli powstanie okienko, system umożliwi użytkownikowi dodanie nowej atrakcji do okienka z podaniem godziny.
    - *And:* Jeśli nowa godzina powoduje konflikt (nakładanie się atrakcji), system wyświetli ostrzeżenie i poprosi o korektę godziny.

---

### WF-OFFERS: Lista noclegów i atrakcji z linkiem zewnętrznym
**Opis:** Aplikacja prezentuje listę pasujących noclegów i atrakcji oraz umożliwia przejście do zewnętrznej strony (np. rezerwacyjnej) przez prosty link.

**Historyjka Użytkownika:**
- Jako użytkownik,
- chcę zobaczyć pasujące noclegi i propozycje atrakcji,
- aby móc szybko przejść do rezerwacji poza aplikacją.

**Cel Biznesowy:** Dostarczenie wartości użytkownikowi oraz możliwość monetyzacji (np. afiliacja) bez blokowania wyboru.

**Warunki Wstępne:** Użytkownik ma ustawioną lokalizację podróży (z planu lub ręcznie).

**Warunki Końcowe:** Użytkownik otwiera zewnętrzną stronę obiektu/oferty w przeglądarce.

**Kryteria Akceptacji:**
- **WF-OFFERS-01: Wyświetlenie listy podmiotów (atrakcje/noclegi) (Scenariusz Główny)**
    - *Given:* Użytkownik ma wybraną lokalizację podróży.
    - *When:* Użytkownik otworzy zakładkę odpowiadającą podmiotowi.
    - *Then:* System wyświetli listę propozycji.
    - *And:* Każda pozycja zawiera nazwę oraz przycisk/link „Zobacz ofertę”.

- **WF-OFFERS-02: Przekierowanie do oferty zewnętrznej (Scenariusz Główny)**
    - *Given:* Użytkownik widzi listę ofert.
    - *When:* Użytkownik kliknie „Zobacz ofertę”.
    - *Then:* System otworzy zewnętrzną stronę w przeglądarce (lub w WebView).

- **WF-OFFERS-03: Brak ofert (Scenariusz Alternatywny)**
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

- **WF-VIP-02: Użytkownik bez VIP (Scenariusz Wyjątkowy)**
    - *Given:* Użytkownik nie ma statusu VIP.
    - *When:* Użytkownik spróbuje użyć funkcji „Warianty tras”.
    - *Then:* System wyświetli ekran/komunikat informujący o dostępności funkcji w VIP.
    - *And:* System nie blokuje dostępu do standardowego planu.
