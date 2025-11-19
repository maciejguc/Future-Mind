# Dodatkowe funkcje mobilne - Integracja z InPost

## Grupa 1: Tracking i powiadomienia

### 1.1 Śledzenie przesyłki w czasie rzeczywistym

**Opis:**
Użytkownik może śledzić lokalizację swojej paczki InPost w czasie rzeczywistym na mapie w aplikacji. Widok pokazuje aktualny status (w transporcie, w Paczkomacie, odebrana) oraz przewidywany czas dostawy.

**Korzyść dla użytkownika:**
- Pełna transparentność - użytkownik wie, gdzie jest jego paczka
- Redukcja niepokoju związanego z oczekiwaniem na przesyłkę
- Możliwość zaplanowania odbioru (wiem, że paczka będzie w Paczkomacie za 2h)

**Korzyść dla biznesu:**
- Redukcja zapytań do obsługi klienta "Gdzie jest moja paczka?"
- Wzrost satysfakcji klienta (NPS score)
- Competitive advantage (nie wszystkie sklepy oferują live tracking)

**Złożoność:** Średnia
- Integracja z InPost Tracking API (webhooks)
- Mapowanie statusów InPost → statusy czytelne dla użytkownika
- UI/UX: Timeline statusów

---

### 1.2 Powiadomienia push o statusie przesyłki

**Opis:**
Użytkownik otrzymuje powiadomienia push na kluczowych etapach dostawy:
- "Paczka nadana - tracking: INP123..."
- "Paczka w transporcie - dostawa jutro"
- "Paczka w Paczkomacie WAW1234 - kod: 1234-5678"
- "Przypomnienie: odbierz paczkę do jutra (30h pozostało)"

**Korzyść dla użytkownika:**
- Nie musi sprawdzać aplikacji - dostanie powiadomienie
- Kod odbioru od razu w notyfikacji (nie trzeba szukać SMS)
- Przypomnienie o odbiorze (uniknie zwrotu paczki)

**Korzyść dla biznesu:**
- Redukcja zwrotów z powodu braku odbioru
- Re-engagement użytkowników (otwierają aplikację częściej)
- Możliwość cross-sell w powiadomieniu ("Twoja paczka gotowa + 10% rabat na kolejne zakupy")

**Złożoność:** Niska
- APNs (Apple Push Notification service) - już zaimplementowane
- Backend: trigger push przy zmianie statusu (webhook InPost)

---

## Grupa 2: Zarządzanie Paczkomatami

### 2.2 Mapa Paczkomatów z filtrami

**Opis:**
Zamiast listy, użytkownik może przeglądać Paczkomaty na mapie. Filtry:
- Dostępne 24h
- Z terminalem płatniczym
- Paczkomaty wielkogabarytowe
- Tylko z wolnymi skrytkami

**Korzyść dla użytkownika:**
- Wizualne szukanie Paczkomatu (łatwiej niż lista)
- Filtrowanie po funkcjach (np. tylko z płatnością, jeśli chce zapłacić gotówką)

**Korzyść dla biznesu:**
- Lepsze UX = wyższa konwersja
- Użytkownicy znajdują Paczkomaty, które faktycznie spełniają ich potrzeby

**Złożoność:** Średnia
- MapKit clustering (wiele pinezek na mapie)
- Filtry + API call z parametrami
- UI/UX: mapa + panel filtrów

---

### 2.3 Rating i recenzje Paczkomatów

**Opis:**
Użytkownicy mogą ocenić Paczkomat po odbiorze (1-5 gwiazdek) i dodać komentarz (np. "Trudno znaleźć wejście", "Zawsze czysty", "Brak parkingu"). Oceny są widoczne dla innych użytkowników przy wyborze Paczkomatu.

**Korzyść dla użytkownika:**
- Wie, którego Paczkomatu unikać (np. zepsutego, w niebezpiecznej lokalizacji)
- Może wybrać najwyżej oceniony Paczkomat w okolicy

**Korzyść dla biznesu:**
- User-generated content (zwiększa engagement)
- Możliwość przekazania negatywnych opinii do InPost (poprawa jakości)
- Lepsza satysfakcja klienta (unika złych Paczkomatów)

**Złożoność:** Średnia
- Backend: tabela ratings (user_id, locker_id, rating, comment)
- Moderacja komentarzy (spam, inappropriate content)
- UI: gwiazdki, komentarze, sortowanie po rating

---

## Grupa 3: Odbiór i zwroty

### 3.5 Opcje dodatkowe przy wysyłce

**Opis:**
Użytkownik podczas składania zamówienia może wybrać dodatkowe opcje dla przesyłki InPost, takie jak:
- Ubezpieczenie przesyłki (np. dla wartościowych produktów RTV/AGD)
- Priorytetowa dostawa (24h zamiast 48h)

**Korzyść dla użytkownika:**
- Większa elastyczność i bezpieczeństwo zakupów
- Spokój przy zamówieniu drogich produktów (np. laptop za 5000 zł)
- Możliwość dostosowania dostawy do swoich potrzeb

**Korzyść dla biznesu:**
- Dodatkowy revenue stream (opłaty za ubezpieczenie, priorytet)
- Redukcja ryzyka reklamacji (ubezpieczenie)
- Competitive advantage (pełna personalizacja dostawy)

**Złożoność:** Średnia
- InPost API: dostępność opcji dodatkowych (insurance, priority)
- Backend: obliczanie dodatkowych kosztów
- UI: dodatkowe checkboxy/switche w checkout flow
- Integracja z płatnościami (dodatkowe opłaty)

---

## Grupa 4: Personalizacja i gamification

### 4.2 Statystyki ekologiczne (CO2 zaoszczędzone)

**Opis:**
Aplikacja pokazuje, ile kg CO2 użytkownik zaoszczędził, korzystając z Paczkomatów zamiast dostawy kurierskiej (każda dostawa kurierem = X kg CO2, Paczkomat = Y kg CO2). Gamification: badge za "Eco Warrior" po 10 dostawach przez Paczkomat.

**Korzyść dla użytkownika:**
- Poczucie, że robi coś dobrego dla środowiska
- Gamification = fun factor

**Korzyść dla biznesu:**
- ESG storytelling (FMedia dba o środowisko)
- Marketing: kampanie "Razem zaoszczędziliśmy X ton CO2"
- Zwiększenie wyboru Paczkomatów (bardziej ekologiczne)

**Złożoność:** Niska
- Algorytm obliczania CO2 (stałe wartości per typ dostawy)
- UI: dashboard z statystykami, badges
- Backend: tracking liczby dostaw per metoda

---

## Grupa 5: Płatności

### 5.1 InPost Pay w aplikacji

**Opis:**
Integracja z InPost Pay jako dodatkowa metoda płatności. Użytkownik może zapłacić za zamówienie używając InPost Pay (portfel cyfrowy InPost).

**Korzyść dla użytkownika:**
- Dodatkowa opcja płatności (może mieć środki w InPost Pay)
- Czasem promocje (cashback od InPost)

**Korzyść dla biznesu:**
- Dodatkowa metoda płatności = wyższa konwersja
- Możliwy revenue share z InPost

**Złożoność:** Średnia
- Integracja InPost Pay API (payment gateway)
- PCI compliance (delegowane do InPost)
- UI: opcja płatności w checkout
