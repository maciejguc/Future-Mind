# User Stories - Dostawa przez Paczkomaty InPost


## Spis treści

1. [US-DEL-01] Wybór opcji dostawy "Paczkomat InPost" (iOS)
2. [US-DEL-02] Wyświetlanie listy dostępnych Paczkomatów (iOS)
3. [US-DEL-03] Wyświetlanie adresu dostawy przy wyborze Paczkomatu (iOS)
4. [US-DEL-04] Sortowanie Paczkomatów po odległości (iOS)
5. [US-DEL-05] Wybór konkretnego Paczkomatu (iOS)
6. [US-DEL-06] Zapisanie wybranego Paczkomatu w zamówieniu (iOS)
7. [US-DEL-07] Backend - Endpoint zwracający listę Paczkomatów (z geocodingiem adresu)
8. [US-DEL-08] Backend - Integracja z InPost API
9. [US-DEL-09] Backend - Obliczanie odległości do Paczkomatów
10. [US-DEL-10] Backend - Walidacja dostępności Paczkomatu
11. [US-DEL-11] Backend - Zapisanie Paczkomatu w zamówieniu

---

### [US-DEL-01] Wybór opcji dostawy "Paczkomat InPost"

**Jako** klient FMedia korzystający z aplikacji mobilnej iOS
**Chcę** móc wybrać opcję dostawy "Odbiór w punkcie - Paczkomaty InPost 24/7"
**Aby** odebrać zamówienie w dogodnym dla mnie miejscu i czasie

#### Kryteria akceptacji

**Zakładając, że** jestem na ekranie checkout i wypełniłem dane odbiorcy (imię, nazwisko, pełny adres dostawy: ulica, numer, kod pocztowy, miasto, kraj)
**Kiedy** klikam przycisk "Wybierz dostawę"
**Wtedy** widzę modal z opcjami dostawy, w tym "Odbiór w punkcie - Paczkomaty InPost 24/7" z ceną 9,99 zł

**Zakładając, że** nie wypełniłem jeszcze danych odbiorcy lub adres dostawy jest niekompletny
**Kiedy** próbuję przejść do sekcji "Dostawa"
**Wtedy** przycisk "Wybierz dostawę" jest nieaktywny lub widzę komunikat "Uzupełnij dane odbiorcy, aby wybrać metodę dostawy"

**Zakładając, że** adres dostawy jest poza Polską
**Kiedy** klikam przycisk "Wybierz dostawę"
**Wtedy** opcja "Paczkomaty InPost" jest ukryta (dostępne tylko metody dostawy międzynarodowej)

**Zakładając, że** lista opcji dostawy jest wyświetlona i adres dostawy jest w Polsce
**Kiedy** wybieram opcję "Odbiór w punkcie - Paczkomaty InPost 24/7"
**Wtedy** aplikacja przekierowuje mnie do ekranu wyboru Paczkomatu

**Zakładając, że** produkty w moim koszyku przekraczają wymiary lub wagę dopuszczalną dla Paczkomatu
**Kiedy** klikam przycisk "Wybierz dostawę"
**Wtedy** opcja "Paczkomaty InPost" jest nieaktywna lub ukryta z informacją o przyczynie

#### Definition of Done

- [ ] Opcja "Paczkomaty InPost" jest widoczna TYLKO gdy dane odbiorcy są kompletne i adres w Polsce
- [ ] Cena dostawy (9,99 zł) jest poprawnie wyświetlana
- [ ] Walidacja danych odbiorcy: przycisk "Wybierz dostawę" nieaktywny gdy dane niekompletne
- [ ] Walidacja kraju: opcja "Paczkomaty InPost" ukryta dla adresów poza Polską
- [ ] Kliknięcie opcji przekierowuje do ekranu wyboru Paczkomatu
- [ ] Opcja jest niedostępna dla produktów wielkogabarytowych
- [ ] UI zgodne z makietą "Checkout - Dostawy"
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe pokrywają >= 80% kodu (walidacja adresu, kraju, gabarytów)
- [ ] Testy UI zautomatyzowane dla happy path + edge cases (brak adresu, adres zagraniczny)
- [ ] Funkcjonalność przetestowana manualnie na iOS 15+ i iOS 16+
- [ ] Brak crashów i memory leaks (sprawdzone w Instruments)
- [ ] Analytics events zaimplementowane (delivery_method_selected, delivery_address_incomplete, delivery_country_outside_pl)

---

### [US-DEL-02] Wyświetlanie listy dostępnych Paczkomatów

**Jako** klient FMedia
**Chcę** zobaczyć listę dostępnych Paczkomatów InPost w okolicy adresu dostawy
**Aby** wybrać najbardziej dogodną lokalizację odbioru

#### Kryteria akceptacji

**Zakładając, że** wybrałem opcję dostawy "Paczkomaty InPost" i podałem wcześniej pełny adres dostawy
**Kiedy** ekran "Wybierz paczkomat" się ładuje
**Wtedy** widzę listę Paczkomatów w okolicy adresu dostawy (promień 5 km) z następującymi informacjami:
- Nazwa Paczkomatu (np. "Paczkomat WAW1234")
- Adres (ulica, kod pocztowy, miasto)
- Dodatkowy opis lokalizacji (np. "Przy stacji BP")
- Odległość od adresu dostawy (np. "200 m", "1 km")

**Zakładając, że** lista Paczkomatów jest wyświetlana
**Kiedy** lista zawiera więcej niż 10 Paczkomatów
**Wtedy** mogę przewijać listę w dół, aby zobaczyć więcej wyników

**Zakładając, że** żaden Paczkomat nie jest dostępny w mojej okolicy (błąd API, brak wyników)
**Kiedy** ekran się ładuje
**Wtedy** widzę komunikat "Brak dostępnych Paczkomatów w Twojej okolicy. Spróbuj wyszukać po adresie."

**Zakładając, że** trwa ładowanie listy Paczkomatów
**Kiedy** czekam na odpowiedź z API
**Wtedy** widzę wskaźnik ładowania (loading spinner)

#### Definition of Done

- [ ] Lista Paczkomatów wyświetla wszystkie wymagane informacje (nazwa, adres, odległość)
- [ ] Lista jest przewijalna (scrollable)
- [ ] Loading state jest poprawnie wyświetlany
- [ ] Empty state (brak wyników) jest poprawnie obsłużony
- [ ] Error state (błąd API) jest poprawnie obsłużony z możliwością retry
- [ ] UI zgodne z makietą "Paczkomaty"
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe pokrywają >= 80% kodu
- [ ] Testy snapshot dla różnych stanów (loading, loaded, empty, error)
- [ ] Funkcjonalność przetestowana na różnych rozmiarach ekranów iOS
- [ ] Accessibility: VoiceOver poprawnie czyta elementy listy
- [ ] Analytics events: parcel_locker_list_viewed

---

### [US-DEL-03] Wyświetlanie adresu dostawy przy wyborze Paczkomatu

**Jako** klient FMedia
**Chcę** widzieć adres dostawy, na podstawie którego są pokazywane Paczkomaty
**Aby** upewnić się, że wybieram Paczkomat w odpowiedniej okolicy

#### Kryteria akceptacji

**Zakładając, że** jestem na ekranie "Wybierz paczkomat"
**Kiedy** ekran się ładuje
**Wtedy** widzę u góry ekranu adres dostawy z danych odbiorcy (np. "ul. Marszałkowska 1, 00-001 Warszawa")

**Zakładając, że** widzę wyświetlony adres dostawy
**Kiedy** zauważam, że adres jest niepoprawny
**Wtedy** widzę przycisk "Zmień adres" lub link "Edytuj", który przenosi mnie z powrotem do edycji danych odbiorcy

**Zakładając, że** kliknąłem "Zmień adres" i zmieniłem adres dostawy
**Kiedy** wracam do ekranu wyboru Paczkomatu
**Wtedy** widzę zaktualizowany adres i zaktualizowaną listę Paczkomatów (odfiltrowaną dla nowego adresu)

**Zakładając, że** adres dostawy został zaktualizowany po wyborze Paczkomatu
**Kiedy** wracam do ekranu wyboru Paczkomatu
**Wtedy** poprzednio wybrany Paczkomat pozostaje zaznaczony (nawet jeśli nie jest już w najbliższej okolicy - walidacja w kolejnym kroku)

#### Definition of Done

- [ ] Adres dostawy jest widoczny na ekranie wyboru Paczkomatu
- [ ] Przycisk/link "Zmień adres" jest dostępny i działa
- [ ] Zmiana adresu dostawy powoduje odświeżenie listy Paczkomatów
- [ ] Poprzedni wybór Paczkomatu jest zachowany po zmianie adresu (nie resetujemy automatycznie)
- [ ] UI zgodne z makietą
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki odświeżania listy przy zmianie adresu
- [ ] Testy UI dla flow edycji adresu
- [ ] Analytics: parcel_locker_address_displayed, parcel_locker_address_changed

---

### [US-DEL-04] Sortowanie Paczkomatów po odległości

**Jako** klient FMedia
**Chcę** aby Paczkomaty były posortowane od najbliższego do najdalszego (względem adresu dostawy)
**Aby** szybko znaleźć najbardziej dogodną lokalizację

#### Kryteria akceptacji

**Zakładając, że** lista Paczkomatów jest wyświetlona
**Kiedy** przeglądam listę
**Wtedy** Paczkomaty są posortowane rosnąco według odległości od adresu dostawy (200m, 600m, 1km, 2.3km, etc.)

**Zakładając, że** zmieniłem adres dostawy (wróciłem do edycji danych odbiorcy)
**Kiedy** wracam do ekranu wyboru Paczkomatu z nowym adresem
**Wtedy** lista Paczkomatów jest ponownie sortowana według odległości od nowego adresu dostawy

**Zakładając, że** lista Paczkomatów jest wyświetlona
**Kiedy** przeglądam odległości
**Wtedy** odległości są wyświetlane w metrach (< 1000m) lub kilometrach (>= 1000m) z odpowiednią precyzją

#### Definition of Done

- [ ] Paczkomaty są domyślnie sortowane po odległości od adresu dostawy (rosnąco)
- [ ] Sortowanie aktualizuje się przy zmianie adresu dostawy
- [ ] Odległości są wyświetlane w metrach (< 1000m) lub kilometrach (>= 1000m)
- [ ] Precyzja odległości: metry (bez miejsc dziesiętnych), kilometry (1 miejsce dziesiętne)
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla algorytmu sortowania (distance sorting)
- [ ] Testy jednostkowe dla formatowania odległości (meters/kilometers)
- [ ] Sprawdzone z różnymi zestawami danych (bliskie i dalekie lokalizacje od adresu dostawy)
- [ ] Analytics: parcel_locker_list_sorted_by_distance

---

### [US-DEL-05] Wybór konkretnego Paczkomatu

**Jako** klient FMedia
**Chcę** móc wybrać konkretny Paczkomat z listy
**Aby** wyznaczyć miejsce odbioru mojego zamówienia

#### Kryteria akceptacji

**Zakładając, że** widzę listę Paczkomatów
**Kiedy** klikam na wybrany Paczkomat
**Wtedy** widzę zaznaczenie (radio button) przy wybranym Paczkomacie

**Zakładając, że** wybrałem Paczkomat
**Kiedy** klikam na inny Paczkomat
**Wtedy** poprzednie zaznaczenie znika, a nowy Paczkomat zostaje zaznaczony (single selection)

**Zakładając, że** wybrałem Paczkomat
**Kiedy** klikam przycisk rozwinięcia (chevron down) przy Paczkomacie
**Wtedy** widzę rozszerzone informacje o Paczkomacie:
- Pełny adres
- Godziny dostępności (24/7)
- Wymiary dostępnych skrytek (opcjonalnie)
- Status dostępności

#### Definition of Done

- [ ] Kliknięcie na Paczkomat powoduje jego zaznaczenie (radio button)
- [ ] Tylko jeden Paczkomat może być zaznaczony jednocześnie
- [ ] Wizualne potwierdzenie wyboru jest wyraźne
- [ ] Rozwinięcie szczegółów Paczkomatu działa poprawnie
- [ ] UI zgodne z makietą (radio button, chevron)
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki wyboru
- [ ] Testy UI dla interakcji z listą
- [ ] Accessibility: VoiceOver poprawnie informuje o zaznaczeniu
- [ ] Analytics: parcel_locker_selected, parcel_locker_details_expanded

---

### [US-DEL-06] Zapisanie wybranego Paczkomatu w zamówieniu

**Jako** klient FMedia
**Chcę** aby wybrany Paczkomat został zapisany jako miejsce dostawy
**Aby** moje zamówienie zostało dostarczone we właściwe miejsce

#### Kryteria akceptacji

**Zakładając, że** wybrałem Paczkomat z listy
**Kiedy** klikam przycisk "Potwierdź" / przycisk nawigacji wstecz
**Wtedy** aplikacja wraca do ekranu checkout z wyświetloną informacją o wybranym Paczkomacie

**Zakładając, że** wróciłem do ekranu checkout po wyborze Paczkomatu
**Kiedy** przeglądam sekcję "Dostawa"
**Wtedy** widzę:
- "Odbiór w punkcie - Paczkomaty InPost 24/7"
- Nazwę wybranego Paczkomatu (np. "Paczkomat WAW1234")
- Adres Paczkomatu
- Cenę dostawy (9,99 zł)

**Zakładając, że** wybrałem Paczkomat
**Kiedy** wracam do ekranu wyboru Paczkomatu (np. chcę zmienić wybór)
**Wtedy** widzę poprzednio wybrany Paczkomat zaznaczony na liście

**Zakładając, że** nie wybrałem żadnego Paczkomatu
**Kiedy** próbuję wrócić do ekranu checkout (klikam wstecz)
**Wtedy** widzę komunikat walidacyjny "Wybierz Paczkomat, aby kontynuować"

#### Definition of Done

- [ ] Wybrany Paczkomat jest zapisywany w stanie aplikacji
- [ ] Informacje o Paczkomacie są wyświetlane na ekranie checkout
- [ ] Powrót do ekranu wyboru zachowuje poprzedni wybór
- [ ] Walidacja wymaga wybrania Paczkomatu przed kontynuacją
- [ ] Dane Paczkomatu są przesyłane do backendu przy składaniu zamówienia
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla state management (wybór, zapis, odczyt)
- [ ] Testy integracyjne dla flow checkout → wybór → powrót
- [ ] Sprawdzona synchronizacja z backendem
- [ ] Analytics: parcel_locker_confirmed, checkout_delivery_updated

---

## User Stories - Backend (Magento 2 API)

### [US-DEL-07] Backend - Endpoint zwracający listę Paczkomatów

**Jako** aplikacja mobilna iOS
**Chcę** móc pobrać listę dostępnych Paczkomatów InPost na podstawie adresu dostawy
**Aby** wyświetlić użytkownikowi opcje do wyboru

#### Kryteria akceptacji

**Zakładając, że** aplikacja mobilna wysyła żądanie GET do `/api/v1/inpost/parcel-lockers`
**Kiedy** podane są parametry: `address` (pełny adres dostawy) LUB `postal_code` + `city`, oraz opcjonalnie `radius` (w km, domyślnie 5 km)
**Wtedy** backend geocoduje adres, a następnie zwraca listę Paczkomatów w formacie JSON z następującymi danymi:
```json
{
  "data": [
    {
      "id": "WAW1234",
      "name": "Paczkomat WAW1234",
      "address": {
        "street": "ul. Biblioteczna 10",
        "city": "Warszawa",
        "postal_code": "00-123",
        "country": "PL"
      },
      "location": {
        "latitude": 52.2297,
        "longitude": 21.0122
      },
      "distance": 200,
      "description": "Przy stacji BP",
      "available": true,
      "operating_hours": "24/7"
    }
  ],
  "meta": {
    "total": 25,
    "returned": 10,
    "radius_km": 5
  }
}
```

**Zakładając, że** żądanie zawiera nieprawidłowe parametry (brak address/postal_code, nieprawidłowy format)
**Kiedy** backend przetwarza żądanie
**Wtedy** zwraca błąd 400 Bad Request z opisem błędu walidacji (np. "Missing required parameter: address or postal_code")

**Zakładając, że** adres nie może być zgeokodowany (nieprawidłowy adres, nieznana lokalizacja)
**Kiedy** backend próbuje geocodować adres
**Wtedy** zwraca błąd 422 Unprocessable Entity z komunikatem "Unable to geocode address. Please check the address and try again."

**Zakładając, że** adres jest poza Polską (geocodowanie zwraca kraj inny niż PL)
**Kiedy** backend przetwarza żądanie
**Wtedy** zwraca 200 OK z pustą tablicą `data: []` i komunikatem `meta.message: "Parcel lockers available only in Poland"`

**Zakładając, że** InPost API nie odpowiada lub zwraca błąd
**Kiedy** backend próbuje pobrać dane
**Wtedy** zwraca błąd 503 Service Unavailable z komunikatem "InPost service temporarily unavailable"

**Zakładając, że** żądanie dotyczy lokalizacji, gdzie nie ma Paczkomatów (np. środek lasu, mała wieś)
**Kiedy** backend przetwarza żądanie
**Wtedy** zwraca 200 OK z pustą tablicą `data: []` i `meta.total: 0`

#### Definition of Done

- [ ] Endpoint `/api/v1/inpost/parcel-lockers` jest zaimplementowany
- [ ] Parametry wejściowe są walidowane (address/postal_code+city, radius)
- [ ] Geocoding adresu jest zaimplementowany i testowany
- [ ] Walidacja kraju (tylko Polska) działa poprawnie
- [ ] Odpowiedź JSON jest zgodna ze zdefiniowanym schematem
- [ ] Błędy geocodingu są prawidłowo obsługiwane (422 dla nieprawidłowego adresu)
- [ ] Błędy API InPost są prawidłowo obsługiwane (retry logic, circuit breaker)
- [ ] Timeout dla żądania do InPost API wynosi max 5 sekund
- [ ] Response time endpointu < 1 sekunda (p95) włącznie z geocodingiem
- [ ] Kod błędów HTTP jest prawidłowy (400, 422, 503, 200)
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe pokrywają >= 80% kodu (geocoding, walidacja kraju)
- [ ] Testy integracyjne z mockiem InPost API i geocoding service
- [ ] Dokumentacja API (Swagger/OpenAPI) zaktualizowana (nowe parametry address/postal_code)
- [ ] Logi zawierają request ID oraz geocoded coordinates dla debugowania
- [ ] Monitoring i alerty dla dostępności endpointu skonfigurowane

---

### [US-DEL-08] Backend - Integracja z InPost API

**Jako** backend FMedia
**Chcę** móc komunikować się z API InPost
**Aby** pobierać aktualne dane o Paczkomatach

#### Kryteria akceptacji

**Zakładając, że** backend potrzebuje pobrać listę Paczkomatów
**Kiedy** wywołuje InPost API endpoint (np. `/v1/points`)
**Wtedy** poprawnie uwierzytelnia się (API key, OAuth) i otrzymuje dane

**Zakładając, że** InPost API wymaga paginacji dla dużej liczby wyników
**Kiedy** backend pobiera dane
**Wtedy** prawidłowo obsługuje paginację i zwraca wszystkie wyniki w określonym promieniu

**Zakładając, że** dane z InPost API mogą się zmieniać (nowe Paczkomaty, zmiany adresów)
**Kiedy** backend pobiera dane
**Wtedy** cache jest ważny przez max 1 godzinę, po czym dane są odświeżane

**Zakładając, że** InPost API jest niedostępne
**Kiedy** backend próbuje pobrać dane
**Wtedy** wykonuje retry logic (3 próby z exponential backoff), a jeśli wszystkie próby failują → zwraca błąd 503 Service Unavailable

**Zakładając, że** InPost API zwraca błąd 429 (rate limit exceeded)
**Kiedy** backend przetwarza odpowiedź
**Wtedy** stosuje backoff zgodnie z header `Retry-After` i loguje ostrzeżenie

#### Definition of Done

- [ ] Integracja z InPost API jest w pełni funkcjonalna
- [ ] Uwierzytelnianie działa poprawnie (API keys są bezpiecznie przechowywane)
- [ ] Paginacja InPost API jest obsługiwana
- [ ] Cache zaimplementowany (Redis/Memcached) z TTL 1 godzina
- [ ] Retry logic zaimplementowany (3 próby, exponential backoff)
- [ ] Circuit breaker pattern zaimplementowany (fail fast po 5 kolejnych błędach)
- [ ] Rate limiting jest obsługiwany (429 errors)
- [ ] Error handling: zwrot 503 gdy InPost API niedostępne po wszystkich retry
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe z mockami InPost API
- [ ] Testy integracyjne w środowisku sandbox InPost
- [ ] Dokumentacja techniczna opisuje flow integracji
- [ ] Monitoring InPost API calls (success rate, latency, errors)
- [ ] Alerty dla wysokiego error rate (> 5%)

---

### [US-DEL-09] Backend - Obliczanie odległości do Paczkomatów

**Jako** backend FMedia
**Chcę** móc obliczać odległość między adresem dostawy a Paczkomatami
**Aby** posortować wyniki od najbliższego do najdalszego

#### Kryteria akceptacji

**Zakładając, że** mam współrzędne geocodowane z adresu dostawy (lat, lng) i współrzędne Paczkomatu
**Kiedy** obliczam odległość
**Wtedy** używam formuły Haversine do obliczenia odległości w linii prostej (w metrach)

**Zakładając, że** obliczona odległość wynosi np. 1500 metrów
**Kiedy** zwracam dane do aplikacji mobilnej
**Wtedy** odległość jest zwrócona w metrach jako liczba całkowita (1500)

**Zakładając, że** lista Paczkomatów została pobrana
**Kiedy** backend przygotowuje odpowiedź
**Wtedy** Paczkomaty są posortowane rosnąco według odległości od adresu dostawy

**Zakładając, że** geocoding zwrócił nieprawidłowe współrzędne (poza zakresem)
**Kiedy** backend próbuje obliczyć odległość
**Wtedy** zwraca błąd walidacji 422 Unprocessable Entity

#### Definition of Done

- [ ] Algorytm Haversine jest poprawnie zaimplementowany
- [ ] Odległość zwracana w metrach (integer)
- [ ] Sortowanie po odległości działa poprawnie
- [ ] Walidacja współrzędnych (latitude: -90 to 90, longitude: -180 to 180)
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla algorytmu Haversine
- [ ] Testy jednostkowe dla sortowania
- [ ] Testy edge cases (współrzędne na biegunach, antypody)
- [ ] Performance test: obliczanie dla 1000 Paczkomatów < 100ms

---

### [US-DEL-10] Backend - Walidacja dostępności Paczkomatu

**Jako** backend FMedia
**Chcę** móc sprawdzić, czy wybrany Paczkomat jest dostępny i może przyjąć paczkę
**Aby** uniknąć sytuacji, gdzie zamówienie nie może być dostarczone

#### Kryteria akceptacji

**Zakładając, że** użytkownik wybrał Paczkomat
**Kiedy** składa zamówienie
**Wtedy** backend weryfikuje w API InPost, czy Paczkomat jest aktywny i ma wolne skrytki

**Zakładając, że** Paczkomat jest pełny lub nieaktywny
**Kiedy** backend sprawdza dostępność
**Wtedy** zwraca błąd 422 Unprocessable Entity z komunikatem "Wybrany Paczkomat jest obecnie niedostępny. Wybierz inny."

**Zakładając, że** produkty w zamówieniu przekraczają dozwolone wymiary Paczkomatu
**Kiedy** backend waliduje zamówienie
**Wtedy** zwraca błąd 422 z komunikatem "Produkty w zamówieniu są zbyt duże dla wybranego Paczkomatu."

**Zakładając, że** wszystko jest w porządku (Paczkomat dostępny, wymiary OK)
**Kiedy** backend waliduje zamówienie
**Wtedy** walidacja przechodzi i zamówienie może być złożone

#### Definition of Done

- [ ] Walidacja dostępności Paczkomatu jest zaimplementowana
- [ ] Sprawdzanie wymiarów produktów vs. wymiary skrytek Paczkomatu
- [ ] Błędy walidacji zwracają kod 422 z opisowymi komunikatami
- [ ] Integracja z InPost API do sprawdzania statusu Paczkomatu
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki walidacji
- [ ] Testy integracyjne z różnymi scenariuszami (pełny, nieaktywny, za małe skrytki)
- [ ] Dokumentacja opisuje możliwe błędy walidacji

---

### [US-DEL-11] Backend - Zapisanie Paczkomatu w zamówieniu

**Jako** backend FMedia
**Chcę** móc zapisać informacje o wybranym Paczkomacie w zamówieniu
**Aby** magazyn wiedział, dokąd wysłać paczkę

#### Kryteria akceptacji

**Zakładając, że** użytkownik składa zamówienie z dostawą do Paczkomatu
**Kiedy** backend przetwarza zamówienie
**Wtedy** zapisuje w bazie danych:
- ID Paczkomatu (np. "WAW1234")
- Nazwę Paczkomatu
- Pełny adres Paczkomatu
- Metodę dostawy: "INPOST_LOCKER"

**Zakładając, że** zamówienie zostało zapisane
**Kiedy** administrator przegląda zamówienie w panelu Magento
**Wtedy** widzi pełne informacje o Paczkomacie w sekcji "Shipping Information"

**Zakładając, że** zamówienie zostało złożone
**Kiedy** system wysyła email potwierdzający do klienta
**Wtedy** email zawiera informacje o wybranym Paczkomacie (nazwa, adres)

**Zakładając, że** zamówienie jest gotowe do wysyłki
**Kiedy** magazyn generuje etykietę InPost
**Wtedy** etykieta zawiera poprawny ID Paczkomatu docelowego

#### Definition of Done

- [ ] Informacje o Paczkomacie są zapisywane w tabeli zamówień Magento
- [ ] Model danych obsługuje wszystkie wymagane pola (ID, nazwa, adres)
- [ ] Panel administracyjny Magento wyświetla informacje o Paczkomacie
- [ ] Email potwierdzający zawiera dane o Paczkomacie
- [ ] Integracja z systemem etykiet InPost jest funkcjonalna
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki zapisu zamówienia
- [ ] Testy integracyjne end-to-end (zamówienie → zapis → wyświetlanie)
- [ ] Migracja bazy danych (jeśli wymagane nowe kolumny)
- [ ] Dokumentacja dla administratorów o nowej funkcjonalności


