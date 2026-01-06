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

### WF-ACCOUNT: Konto użytkownika (rejestracja) oraz zakup VIP

**Opis:** System umożliwia użytkownikowi rejestrację i korzystanie z konta, aby zapisywać ustawienia (np. warianty planu) oraz wykupić dostęp VIP. Po zakupie VIP system nadaje użytkownikowi status VIP i udostępnia funkcje premium (np. dodatkowe warianty tras).

**Historyjka Użytkownika:**
- Jako użytkownik,
- chcę móc założyć konto,
- aby mieć dostęp do funkcji wymagających identyfikacji użytkownika oraz móc wykupić VIP.

**Cel Biznesowy:** Umożliwienie personalizacji i monetyzacji aplikacji (VIP).

**Warunki Wstępne:** Użytkownik ma dostęp do ekranu rejestracji/logowania i aplikacja ma dostęp do internetu.

**Warunki Końcowe:** Użytkownik ma utworzone konto (oraz opcjonalnie aktywny status VIP), który jest widoczny w aplikacji.

**Kryteria Akceptacji:**

- **WF-ACCOUNT-01: Rejestracja użytkownika (Scenariusz Główny)**
    - *Given:* Użytkownik nie jest zalogowany i widzi formularz rejestracji.
    - *When:* Użytkownik poda wymagane dane rejestracyjne (np. e-mail i hasło) i zatwierdzi rejestrację.
    - *Then:* System utworzy konto użytkownika.
    - *And:* System zaloguje użytkownika automatycznie lub umożliwi logowanie bez ponownego podawania danych.
    - *And:* System wyświetli potwierdzenie poprawnej rejestracji.

- **WF-ACCOUNT-02: Rejestracja z użyciem zajętego adresu e-mail (Scenariusz Wyjątkowy)**
    - *Given:* Użytkownik próbuje zarejestrować konto na adres e-mail, który już istnieje w systemie.
    - *When:* Użytkownik zatwierdzi rejestrację.
    - *Then:* System wyświetli komunikat o konflikcie (np. „Konto z tym e-mailem już istnieje”).
    - *And:* System zaproponuje przejście do logowania lub resetu hasła (jeśli przewidziane w MVP).

- **WF-ACCOUNT-03: Zakup VIP (Scenariusz Główny)**
    - *Given:* Użytkownik jest zalogowany i nie posiada statusu VIP.
    - *When:* Użytkownik wybierze opcję „Kup VIP” i zakończy proces płatności.
    - *Then:* System oznaczy konto użytkownika jako VIP.
    - *And:* System natychmiast odblokuje funkcje VIP (np. warianty tras) w aplikacji.
    - *And:* System wyświetli potwierdzenie aktywacji VIP.

- **WF-ACCOUNT-04: Nieudana płatność VIP (Scenariusz Wyjątkowy)**
    - *Given:* Użytkownik rozpoczął proces zakupu VIP.
    - *When:* Płatność zostanie odrzucona lub przerwana.
    - *Then:* System nie nada statusu VIP.
    - *And:* System wyświetli komunikat o niepowodzeniu i umożliwi ponowienie próby.

- **WF-ACCOUNT-05: Widoczny status VIP (Scenariusz Główny)**
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

- **WF-VIP-02: Użytkownik bez VIP (Scenariusz Wyjątkowy)**
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
| WF-EDIT | Edycja planu podróży (dodawanie, usuwanie, zmiana kolejności) | 13 | 8 | 8 | 5 | 1.62 |
| WF-OFFERS | Lista noclegów i atrakcji z linkami zewnętrznymi | 8 | 5 | 5 | 5 | 1.30 |
| WF-NIGHT | Uwzględnianie noclegów w planie podróży | 8 | 8 | 8 | 5 | 1.14 |
| WF-ACCOUNT | Rejestracja konta użytkownika i zakup wersji VIP | 5 | 5 | 13 | 8 | 0.48 |
| WF-VIP | Zaawansowane funkcje planowania dostępne dla użytkowników VIP | 3 | 3 | 13 | 8 | 0.29 |

### Uzasadnienie przyznanych priorytetów

**WF-PLAN – Generowanie planu podróży**  
Funkcjonalność stanowi podstawę działania systemu i realizuje jego główny cel. Bez niej aplikacja nie byłaby użyteczna dla użytkownika, co uzasadnia najwyższą ocenę zarówno korzyści, jak i kary. Pomimo relatywnie wysokiego kosztu i ryzyka implementacji, jej znaczenie dla systemu przekłada się na najwyższy priorytet.

**WF-INFO – Podgląd szczegółów atrakcji i nawigacja po planie**  
Funkcjonalność znacząco zwiększa czytelność i użyteczność wygenerowanego planu, umożliwiając użytkownikowi zapoznanie się ze szczegółami zaproponowanych atrakcji. Jej brak obniżyłby komfort korzystania z systemu, jednak nie uniemożliwiłby jego działania, co uzasadnia wysoki, lecz niższy niż w przypadku WF-PLAN priorytet.

**WF-EDIT – Edycja planu podróży**  
Możliwość modyfikowania wygenerowanego planu pozwala użytkownikowi dostosować go do własnych preferencji. Funkcjonalność ta jest istotna z punktu widzenia doświadczenia użytkownika, jednak jej implementacja wiąże się z większym kosztem oraz ryzykiem, wynikającym z konieczności zachowania spójności planu.

**WF-OFFERS – Lista ofert noclegów i atrakcji**  
Funkcjonalność uzupełniająca, umożliwiająca przejście do zewnętrznych serwisów rezerwacyjnych. Jej obecność zwiększa użyteczność systemu, jednak brak tej funkcji nie uniemożliwia realizacji podstawowego celu aplikacji.

**WF-NIGHT – Uwzględnianie noclegów w planie podróży**  
Funkcjonalność zwiększa kompletność planu podróży, szczególnie w przypadku wyjazdów wielodniowych. Ze względu na to, że nie jest niezbędna do podstawowego działania systemu, otrzymała umiarkowany priorytet.

**WF-ACCOUNT – Rejestracja konta użytkownika i zakup wersji VIP**  
Funkcjonalność istotna z perspektywy dalszego rozwoju i monetyzacji systemu, jednak niewymagana na etapie pierwszej wersji aplikacji. Wysoki koszt oraz ryzyko implementacji obniżają jej priorytet.

**WF-VIP – Zaawansowane funkcje planowania dla użytkowników VIP**  
Stanowi rozszerzenie funkcjonalności systemu skierowane do węższej grupy użytkowników. Niewielki wpływ na podstawową funkcjonalność aplikacji oraz relatywnie wysoki koszt wdrożenia uzasadniają najniższy priorytet.
