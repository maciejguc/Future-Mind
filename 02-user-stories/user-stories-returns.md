# User Stories - Zwroty przez Paczkomaty InPost


## Spis treści

1. [US-RET-01] Przeglądanie zamówień z możliwością zwrotu (iOS)
2. [US-RET-02] Inicjowanie zwrotu produktu (iOS)
3. [US-RET-03] Wybór przyczyny zwrotu (iOS)
4. [US-RET-04] Wybór Paczkomatu dla zwrotu (iOS)
5. [US-RET-05] Generowanie i pobieranie etykiety zwrotnej (iOS)
6. [US-RET-06] Wyświetlanie instrukcji zwrotu (iOS)
7. [US-RET-07] Śledzenie statusu zwrotu (iOS)
8. [US-RET-08] Backend - Endpoint inicjowania zwrotu
9. [US-RET-09] Backend - Generowanie etykiety zwrotnej InPost
10. [US-RET-10] Backend - Aktualizacja statusu zwrotu
11. [US-RET-11] Backend - Obsługa zwrotu płatności
12. [US-RET-12] Backend - Powiadomienia o statusie zwrotu

---

## User Stories - iOS Mobile App

### [US-RET-01] Przeglądanie zamówień z możliwością zwrotu

**Jako** klient FMedia
**Chcę** móc przeglądać historię moich zamówień i widzieć, które można zwrócić
**Aby** szybko zainicjować zwrot produktu, jeśli jest taka potrzeba

#### Kryteria akceptacji

**Zakładając, że** jestem zalogowany w aplikacji mobilnej
**Kiedy** przechodzę do sekcji "Moje zamówienia"
**Wtedy** widzę listę wszystkich moich zamówień z informacją o statusie

**Zakładając, że** przeglądam listę zamówień
**Kiedy** zamówienie zostało dostarczone i jest w okresie zwrotu (np. 14 dni)
**Wtedy** widzę przycisk "Zwróć produkty" przy danym zamówieniu

**Zakładając, że** zamówienie jest poza okresem zwrotu lub już zostało zwrócone
**Kiedy** przeglądam szczegóły zamówienia
**Wtedy** przycisk "Zwróć produkty" jest nieaktywny lub ukryty

**Zakładając, że** kliknąłem na zamówienie
**Kiedy** wyświetlają się szczegóły zamówienia
**Wtedy** widzę:
- Listę produktów w zamówieniu
- Status dostawy
- Informację o okresie zwrotu (np. "Możesz zwrócić do: 2025-11-30")
- Przycisk "Zwróć produkty"

#### Definition of Done

- [ ] Lista zamówień wyświetla wszystkie zamówienia użytkownika
- [ ] Przycisk "Zwróć produkty" jest widoczny tylko dla zamówień w okresie zwrotu
- [ ] Zamówienia poza okresem zwrotu mają nieaktywny przycisk lub brak przycisku
- [ ] Szczegóły zamówienia zawierają wszystkie wymagane informacje
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe pokrywają >= 80% kodu
- [ ] Testy UI dla różnych stanów zamówień (zwrotne, niezwrotne, zwrócone)
- [ ] Accessibility: VoiceOver poprawnie czyta elementy
- [ ] Analytics: order_history_viewed, return_eligible_order_viewed

---

### [US-RET-02] Inicjowanie zwrotu produktu

**Jako** klient FMedia
**Chcę** móc zainicjować zwrot wybranych produktów z zamówienia
**Aby** rozpocząć proces zwrotu

#### Kryteria akceptacji

**Zakładając, że** jestem w szczegółach zamówienia, które można zwrócić
**Kiedy** klikam przycisk "Zwróć produkty"
**Wtedy** widzę listę produktów z zamówienia z możliwością wyboru (checkboxy)

**Zakładając, że** widzę listę produktów do zwrotu
**Kiedy** zaznaczam produkty, które chcę zwrócić
**Wtedy** widzę podsumowanie: liczbę zwracanych produktów i potencjalną kwotę zwrotu

**Zakładając, że** zamówienie zawiera produkty, które nie mogą być zwrócone (np. produkty personalizowane)
**Kiedy** przeglądam listę produktów
**Wtedy** takie produkty są oznaczone jako "Nie podlega zwrotowi" i nie można ich zaznaczyć

**Zakładając, że** zaznaczyłem przynajmniej jeden produkt
**Kiedy** klikam przycisk "Dalej"
**Wtedy** przechodzę do ekranu wyboru przyczyny zwrotu

**Zakładając, że** nie zaznaczyłem żadnego produktu
**Kiedy** klikam przycisk "Dalej"
**Wtedy** widzę komunikat walidacyjny "Wybierz przynajmniej jeden produkt do zwrotu"

#### Definition of Done

- [ ] Lista produktów wyświetla wszystkie produkty z zamówienia
- [ ] Checkboxy umożliwiają wybór wielu produktów
- [ ] Produkty niepodlegające zwrotowi są odpowiednio oznaczone
- [ ] Podsumowanie (liczba produktów, kwota) aktualizuje się dynamicznie
- [ ] Walidacja wymaga wyboru min. 1 produktu
- [ ] Przycisk "Dalej" przekierowuje do wyboru przyczyny zwrotu
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki wyboru produktów
- [ ] Testy UI dla różnych scenariuszy (1 produkt, wiele produktów, wszystkie)
- [ ] Analytics: return_initiated, products_selected_for_return

---

### [US-RET-03] Wybór przyczyny zwrotu

**Jako** klient FMedia
**Chcę** móc wybrać przyczynę zwrotu produktu
**Aby** FMedia mogło lepiej zrozumieć powody zwrotów i poprawić jakość usług

#### Kryteria akceptacji

**Zakładając, że** wybrałem produkty do zwrotu
**Kiedy** przechodzę do ekranu wyboru przyczyny
**Wtedy** widzę listę możliwych przyczyn zwrotu:
- "Produkt niezgodny z opisem"
- "Produkt uszkodzony/wadliwy"
- "Pomyłka przy zamówieniu"
- "Znalazłem lepszą ofertę"
- "Rezygnacja z zakupu"
- "Inna przyczyna"

**Zakładając, że** wybrałem opcję "Inna przyczyna"
**Kiedy** zaznaczam tę opcję
**Wtedy** pojawia się pole tekstowe, gdzie mogę wpisać szczegóły (opcjonalne)

**Zakładając, że** wybrałem przyczynę zwrotu
**Kiedy** klikam przycisk "Dalej"
**Wtedy** przechodzę do ekranu wyboru Paczkomatu dla zwrotu

**Zakładając, że** nie wybrałem żadnej przyczyny
**Kiedy** klikam przycisk "Dalej"
**Wtedy** widzę komunikat walidacyjny "Wybierz przyczynę zwrotu"

#### Definition of Done

- [ ] Lista przyczyn zwrotu jest wyświetlana (radio buttons)
- [ ] Opcja "Inna przyczyna" wyświetla pole tekstowe
- [ ] Walidacja wymaga wyboru przyczyny
- [ ] Przycisk "Dalej" przekierowuje do wyboru Paczkomatu
- [ ] Wybrana przyczyna jest zapisywana w stanie aplikacji
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki wyboru przyczyny
- [ ] Testy UI dla różnych przyczyn
- [ ] Analytics: return_reason_selected

---

### [US-RET-04] Wybór Paczkomatu dla zwrotu

**Jako** klient FMedia
**Chcę** móc wybrać Paczkomat, do którego nadaję zwrot
**Aby** wybrać najbardziej dogodną lokalizację

#### Kryteria akceptacji

**Zakładając, że** wybrałem przyczynę zwrotu
**Kiedy** przechodzę do ekranu wyboru Paczkomatu
**Wtedy** widzę listę dostępnych Paczkomatów (podobnie jak przy dostawie)

**Zakładając, że** widzę listę Paczkomatów
**Kiedy** lista się ładuje
**Wtedy** Paczkomaty są posortowane według odległości od mojej lokalizacji

**Zakładając, że** przeglądam listę Paczkomatów
**Kiedy** wybieram konkretny Paczkomat
**Wtedy** widzę zaznaczenie (radio button) przy wybranym Paczkomacie

**Zakładając, że** wybrałem Paczkomat
**Kiedy** klikam przycisk "Dalej"
**Wtedy** przechodzę do ekranu generowania etykiety zwrotnej

**Zakładając, że** nie wybrałem Paczkomatu
**Kiedy** klikam przycisk "Dalej"
**Wtedy** widzę komunikat walidacyjny "Wybierz Paczkomat, aby kontynuować"

#### Kryteria akceptacji - funkcjonalności zaawansowane

**Zakładając, że** zwracam produkt, który został dostarczony do Paczkomatu
**Kiedy** wchodzę na ekran wyboru Paczkomatu dla zwrotu
**Wtedy** Paczkomat, z którego odebrałem paczkę, jest domyślnie zaznaczony lub sugerowany na górze listy

**Zakładając, że** zwracam produkty o dużych gabarytach
**Kiedy** przeglądam listę Paczkomatów
**Wtedy** widzę tylko Paczkomaty z odpowiednio dużymi skrytkami (filtrowanie po wymiarach)

#### Definition of Done

- [ ] Lista Paczkomatów jest wyświetlana z odległościami
- [ ] Paczkomaty są sortowane po odległości
- [ ] Wybór Paczkomatu działa (radio button, single selection)
- [ ] Paczkomat oryginalnej dostawy jest sugerowany (jeśli dotyczy)
- [ ] Filtrowanie Paczkomatów po wymiarach skrytek (dla dużych produktów)
- [ ] Walidacja wymaga wyboru Paczkomatu
- [ ] Przycisk "Dalej" przekierowuje do generowania etykiety
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki wyboru i filtrowania
- [ ] Testy UI dla różnych scenariuszy (małe/duże produkty)
- [ ] Wykorzystanie komponentu z US-DEL-02 (reusable component)
- [ ] Analytics: return_parcel_locker_selected

---

### [US-RET-05] Generowanie i pobieranie etykiety zwrotnej

**Jako** klient FMedia
**Chcę** móc wygenerować i pobrać etykietę zwrotną
**Aby** nadać paczkę w wybranym Paczkomacie

#### Kryteria akceptacji

**Zakładając, że** wybrałem Paczkomat dla zwrotu
**Kiedy** klikam przycisk "Generuj etykietę"
**Wtedy** backend generuje etykietę zwrotną i aplikacja wyświetla potwierdzenie

**Zakładając, że** etykieta została wygenerowana
**Kiedy** widzę ekran potwierdzenia
**Wtedy** widzę:
- Numer zwrotu (np. "RET-2025-001234")
- Kod QR do nadania paczki
- Link do pobrania etykiety w formacie PDF
- Przycisk "Pobierz etykietę PDF"
- Przycisk "Zapisz kod QR do zdjęć"

**Zakładając, że** kliknąłem "Pobierz etykietę PDF"
**Kiedy** aplikacja przetwarza żądanie
**Wtedy** plik PDF z etykietą jest pobierany i zapisywany w aplikacji Files

**Zakładając, że** kliknąłem "Zapisz kod QR do zdjęć"
**Kiedy** aplikacja przetwarza żądanie
**Wtedy** kod QR jest zapisywany w galerii zdjęć (po uzyskaniu uprawnień do Photos)

**Zakładając, że** generowanie etykiety się nie powiodło (błąd API InPost)
**Kiedy** backend zwraca błąd
**Wtedy** widzę komunikat "Nie udało się wygenerować etykiety. Spróbuj ponownie." z przyciskiem "Spróbuj ponownie"

#### Definition of Done

- [ ] Generowanie etykiety wywołuje endpoint backendu
- [ ] Kod QR jest wyświetlany na ekranie
- [ ] Numer zwrotu jest wyświetlany
- [ ] Pobieranie PDF działa (zapisuje do Files)
- [ ] Zapisywanie QR do Photos działa (z obsługą uprawnień)
- [ ] Error handling dla błędów generowania
- [ ] Loading state podczas generowania
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki generowania i pobierania
- [ ] Testy UI dla różnych stanów (loading, success, error)
- [ ] Sprawdzone uprawnienia iOS (Photos access)
- [ ] Analytics: return_label_generated, return_label_downloaded, qr_code_saved

---

### [US-RET-06] Wyświetlanie instrukcji zwrotu

**Jako** klient FMedia
**Chcę** zobaczyć jasne instrukcje, jak nadać zwrot przez Paczkomat
**Aby** poprawnie wysłać paczkę

#### Kryteria akceptacji

**Zakładając, że** wygenerowałem etykietę zwrotną
**Kiedy** przeglądam ekran potwierdzenia
**Wtedy** widzę instrukcje krok po kroku:
1. "Zapakuj produkt w oryginalne opakowanie lub pudełko"
2. "Naklej etykietę zwrotną na paczce (lub użyj kodu QR)"
3. "Udaj się do wybranego Paczkomatu: [nazwa i adres]"
4. "Wybierz opcję 'Nadaj paczkę' na ekranie Paczkomatu"
5. "Zeskanuj kod QR lub wpisz numer nadania"
6. "Włóż paczkę do otwartej skrytki"
7. "Poczekaj na potwierdzenie nadania (SMS/email)"

**Zakładając, że** przeglądam instrukcje
**Kiedy** klikam na adres Paczkomatu
**Wtedy** aplikacja otwiera mapę z lokalizacją Paczkomatu (Apple Maps / Google Maps)

**Zakładając, że** przeczytałem instrukcje
**Kiedy** klikam przycisk "Zakończ"
**Wtedy** wracam do ekranu "Moje zamówienia" z widocznym zwrotem w trakcie realizacji

#### Definition of Done

- [ ] Instrukcje zwrotu są wyświetlane krok po kroku
- [ ] Link do mapy Paczkomatu działa
- [ ] Przycisk "Zakończ" wraca do listy zamówień
- [ ] Instrukcje są czytelne i zrozumiałe
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy UI dla wyświetlania instrukcji
- [ ] Analytics: return_instructions_viewed, parcel_locker_location_opened

---

### [US-RET-07] Śledzenie statusu zwrotu

**Jako** klient FMedia
**Chcę** móc śledzić status mojego zwrotu
**Aby** wiedzieć, na jakim etapie jest proces

#### Kryteria akceptacji

**Zakładając, że** zainicjowałem zwrot
**Kiedy** wchodzę do sekcji "Moje zwroty" lub szczegółów zamówienia
**Wtedy** widzę aktualny status zwrotu:
- "Etykieta wygenerowana - oczekuje na nadanie"
- "Paczka nadana w Paczkomacie"
- "Paczka w transporcie"
- "Paczka dostarczona do magazynu FMedia"
- "Zwrot zaakceptowany - oczekuje na zwrot środków"
- "Zwrot zrealizowany - środki zwrócone"

**Zakładając, że** przeglądam szczegóły zwrotu
**Kiedy** klikam na zwrot
**Wtedy** widzę:
- Numer zwrotu
- Listę zwracanych produktów
- Status zwrotu
- Historia zmian statusu z datami
- Szacowany czas zwrotu środków

**Zakładając, że** status zwrotu się zmienił
**Kiedy** backend aktualizuje status
**Wtedy** otrzymuję powiadomienie push (jeśli włączone) informujące o zmianie

**Zakładając, że** zwrot został zrealizowany i środki zwrócone
**Kiedy** przeglądam szczegóły zwrotu
**Wtedy** widzę potwierdzenie: "Środki w kwocie XXX zł zostały zwrócone [data]"

#### Definition of Done

- [ ] Lista zwrotów wyświetla wszystkie zwroty użytkownika
- [ ] Statusy zwrotu są poprawnie wyświetlane
- [ ] Historia zmian statusu jest dostępna
- [ ] Szczegóły zwrotu zawierają wszystkie informacje
- [ ] Powiadomienia push o zmianach statusu działają
- [ ] Synchronizacja statusu z backendem co 30s lub webhook
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki statusów
- [ ] Testy UI dla różnych stanów zwrotu
- [ ] Testy integracyjne z backendem (webhook handling)
- [ ] Analytics: return_status_viewed, return_status_updated

---

## User Stories - Backend (Magento 2 API)

### [US-RET-08] Backend - Endpoint inicjowania zwrotu

**Jako** aplikacja mobilna iOS
**Chcę** móc zainicjować zwrot produktów z zamówienia
**Aby** rozpocząć proces zwrotu dla użytkownika

#### Kryteria akceptacji

**Zakładając, że** aplikacja mobilna wysyła żądanie POST do `/api/v1/orders/{orderId}/returns`
**Kiedy** podane są parametry:
```json
{
  "items": [
    {
      "product_id": "12345",
      "quantity": 1
    }
  ],
  "reason": "Produkt niezgodny z opisem",
  "reason_details": "Kolor inny niż na zdjęciu",
  "parcel_locker_id": "WAW1234"
}
```
**Wtedy** backend tworzy zwrot w Magento i zwraca:
```json
{
  "return_id": "RET-2025-001234",
  "status": "pending",
  "items": [...],
  "estimated_refund": 299.99,
  "parcel_locker": {
    "id": "WAW1234",
    "name": "Paczkomat WAW1234",
    "address": "ul. Biblioteczna 10, 00-123 Warszawa"
  },
  "created_at": "2025-11-16T10:30:00Z"
}
```

**Zakładając, że** zamówienie jest poza okresem zwrotu
**Kiedy** backend przetwarza żądanie
**Wtedy** zwraca błąd 422 Unprocessable Entity z komunikatem "Return period expired"

**Zakładając, że** produkt nie podlega zwrotowi (np. produkt personalizowany)
**Kiedy** backend przetwarza żądanie
**Wtedy** zwraca błąd 422 z komunikatem "Product is not returnable"

**Zakładając, że** żądanie zawiera nieprawidłowe dane (brak items, nieprawidłowy order_id)
**Kiedy** backend przetwarza żądanie
**Wtedy** zwraca błąd 400 Bad Request z opisem błędów walidacji

#### Definition of Done

- [ ] Endpoint `/api/v1/orders/{orderId}/returns` jest zaimplementowany
- [ ] Walidacja okresu zwrotu (domyślnie 14 dni od dostawy)
- [ ] Walidacja produktów (czy podlegają zwrotowi)
- [ ] Tworzenie zwrotu w Magento (RMA - Return Merchandise Authorization)
- [ ] Obliczanie szacowanej kwoty zwrotu
- [ ] Odpowiedź JSON zgodna ze schematem
- [ ] Kody błędów HTTP są prawidłowe (400, 422, 201)
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe pokrywają >= 80% kodu
- [ ] Testy integracyjne z Magento RMA
- [ ] Dokumentacja API (Swagger/OpenAPI) zaktualizowana
- [ ] Logi zawierają request ID i user ID
- [ ] Monitoring dla success/error rate

---

### [US-RET-09] Backend - Generowanie etykiety zwrotnej InPost

**Jako** backend FMedia
**Chcę** móc wygenerować etykietę zwrotną InPost
**Aby** klient mógł nadać zwrot przez Paczkomat

#### Kryteria akceptacji

**Zakładając, że** zwrot został zainicjowany
**Kiedy** backend wywołuje endpoint POST `/api/v1/returns/{returnId}/label`
**Wtedy** backend komunikuje się z InPost API, aby wygenerować etykietę zwrotną

**Zakładając, że** InPost API zwraca etykietę
**Kiedy** backend przetwarza odpowiedź
**Wtedy** zapisuje:
- Numer listu przewozowego (tracking number)
- URL do etykiety PDF
- Kod QR (base64 lub URL)
- Status: "label_generated"

**Zakładając, że** backend ma etykietę
**Kiedy** zwraca odpowiedź do aplikacji mobilnej
**Wtedy** odpowiedź zawiera:
```json
{
  "return_id": "RET-2025-001234",
  "tracking_number": "INP123456789012345",
  "label_url": "https://inpost.pl/labels/INP123456789012345.pdf",
  "qr_code": "data:image/png;base64,iVBORw0KG...",
  "status": "label_generated"
}
```

**Zakładając, że** InPost API nie jest dostępne
**Kiedy** backend próbuje wygenerować etykietę
**Wtedy** zwraca błąd 503 Service Unavailable z komunikatem "InPost service temporarily unavailable"

**Zakładając, że** Paczkomat wybrany przez klienta jest pełny/nieaktywny
**Kiedy** backend próbuje wygenerować etykietę
**Wtedy** zwraca błąd 422 z komunikatem "Selected Parcel Locker is unavailable. Please choose another one."

#### Definition of Done

- [ ] Endpoint `/api/v1/returns/{returnId}/label` jest zaimplementowany
- [ ] Integracja z InPost API do generowania etykiet zwrotnych
- [ ] Walidacja dostępności Paczkomatu przed generowaniem
- [ ] Zapisywanie tracking number i URL etykiety w bazie danych
- [ ] Generowanie lub pobieranie kodu QR
- [ ] Obsługa błędów InPost API (retry, circuit breaker)
- [ ] Timeout dla InPost API: max 10 sekund
- [ ] Response time endpointu < 2 sekundy (p95)
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe z mockami InPost API
- [ ] Testy integracyjne w środowisku sandbox InPost
- [ ] Dokumentacja API zaktualizowana
- [ ] Monitoring wywołań InPost API (latency, errors)
- [ ] Logi zawierają return_id i tracking_number

---

### [US-RET-10] Backend - Aktualizacja statusu zwrotu

**Jako** backend FMedia
**Chcę** móc aktualizować status zwrotu w oparciu o informacje z InPost
**Aby** klient widział aktualny status w aplikacji

#### Kryteria akceptacji

**Zakładając, że** InPost wysyła webhook o zmianie statusu przesyłki
**Kiedy** backend otrzymuje webhook (np. "paczka nadana", "paczka dostarczona do magazynu")
**Wtedy** aktualizuje status zwrotu w Magento zgodnie z mapowaniem:
- InPost: "dispatched" → FMedia: "shipped_to_warehouse"
- InPost: "delivered" → FMedia: "received_in_warehouse"
- InPost: "delivered" + weryfikacja w magazynie → FMedia: "refund_pending"

**Zakładając, że** status zwrotu się zmienił
**Kiedy** backend aktualizuje status w bazie danych
**Wtedy** wysyła powiadomienie do aplikacji mobilnej (push notification via FCM/APNs)

**Zakładając, że** aplikacja mobilna wysyła żądanie GET `/api/v1/returns/{returnId}`
**Kiedy** backend przetwarza żądanie
**Wtedy** zwraca aktualny status zwrotu z historią zmian:
```json
{
  "return_id": "RET-2025-001234",
  "status": "received_in_warehouse",
  "tracking_number": "INP123456789012345",
  "history": [
    {
      "status": "pending",
      "timestamp": "2025-11-16T10:30:00Z"
    },
    {
      "status": "label_generated",
      "timestamp": "2025-11-16T10:35:00Z"
    },
    {
      "status": "shipped_to_warehouse",
      "timestamp": "2025-11-16T14:20:00Z"
    },
    {
      "status": "received_in_warehouse",
      "timestamp": "2025-11-17T09:15:00Z"
    }
  ]
}
```

**Zakładając, że** webhook z InPost zawiera nieprawidłowe dane
**Kiedy** backend przetwarza webhook
**Wtedy** loguje błąd walidacji i odpowiada 400 Bad Request (aby InPost ponowił próbę)

#### Definition of Done

- [ ] Endpoint webhook `/webhooks/inpost/tracking` jest zaimplementowany
- [ ] Webhook signature verification (bezpieczeństwo)
- [ ] Mapowanie statusów InPost → FMedia
- [ ] Aktualizacja statusu w bazie danych
- [ ] Historia zmian statusu jest zapisywana
- [ ] Wysyłanie powiadomień push do aplikacji mobilnej
- [ ] Endpoint GET `/api/v1/returns/{returnId}` zwraca status z historią
- [ ] Walidacja webhooków (signature, payload)
- [ ] Idempotency (obsługa duplikatów webhooków)
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki aktualizacji statusu
- [ ] Testy integracyjne z symulacją webhooków InPost
- [ ] Dokumentacja webhooków (payload, security)
- [ ] Monitoring webhooków (rate, failures)
- [ ] Alerty dla wysokiego error rate webhooków

---

### [US-RET-11] Backend - Obsługa zwrotu płatności

**Jako** backend FMedia
**Chcę** móc automatycznie zwrócić środki klientowi po zaakceptowaniu zwrotu
**Aby** klient otrzymał zwrot pieniędzy szybko i bezproblemowo

#### Kryteria akceptacji

**Zakładając, że** zwrot został dostarczony do magazynu i zweryfikowany pozytywnie
**Kiedy** magazyn akceptuje zwrot (zmiana statusu na "approved")
**Wtedy** backend automatycznie inicjuje zwrot płatności przez bramkę płatności

**Zakładając, że** zwrot płatności jest przetwarzany
**Kiedy** bramka płatności potwierdza zwrot
**Wtedy** backend aktualizuje status zwrotu na "refunded" i zapisuje:
- Kwotę zwrotu
- Datę zwrotu
- ID transakcji zwrotu

**Zakładając, że** płatność została zwrócona
**Kiedy** backend aktualizuje status
**Wtedy** wysyła email do klienta z potwierdzeniem zwrotu środków

**Zakładając, że** zwrot płatności się nie powiódł (błąd bramki płatności)
**Kiedy** backend otrzymuje błąd
**Wtedy** zmienia status na "refund_failed" i wysyła alert do zespołu finansowego

**Zakładając, że** klient płacił za pobraniem
**Kiedy** zwrot jest akceptowany
**Wtedy** backend oznacza zwrot jako "approved_cash_on_delivery" i czeka na ręczną obsługę przez dział finansowy

**Zakładając, że** zamówienie zawierało darmową dostawę (promocja)
**Kiedy** zwrot jest przetwarzany
**Wtedy** klient otrzymuje zwrot tylko za produkty (bez kosztu dostawy = 0 zł)

**Zakładając, że** klient zwrócił tylko część zamówienia
**Kiedy** backend oblicza kwotę zwrotu
**Wtedy** zwraca tylko wartość zwróconych produktów (proporcjonalnie)

#### Definition of Done

- [ ] Automatyczna inicjalizacja zwrotu płatności po zaakceptowaniu zwrotu
- [ ] Integracja z bramkami płatności (Przelewy24, PayU, etc.) dla refundów
- [ ] Obliczanie kwoty zwrotu (produkty + ewentualnie dostawa)
- [ ] Obsługa różnych metod płatności (karta, BLIK, przelew, za pobraniem)
- [ ] Aktualizacja statusu zwrotu po pomyślnym refundzie
- [ ] Wysyłanie email z potwierdzeniem zwrotu środków
- [ ] Error handling dla błędów bramki płatności
- [ ] Alerty dla zespołu w przypadku błędów refund
- [ ] Ręczna obsługa dla płatności za pobraniem
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki zwrotu płatności
- [ ] Testy integracyjne z mockami bramek płatności
- [ ] Dokumentacja procesu zwrotu płatności
- [ ] Monitoring refundów (success rate, amounts)
- [ ] Raportowanie zwrotów dla działu finansowego

---

### [US-RET-12] Backend - Powiadomienia o statusie zwrotu

**Jako** backend FMedia
**Chcę** móc wysyłać powiadomienia do klienta o zmianach statusu zwrotu
**Aby** klient był na bieżąco informowany

#### Kryteria akceptacji

**Zakładając, że** status zwrotu się zmienił
**Kiedy** backend aktualizuje status w bazie danych
**Wtedy** wysyła powiadomienia zgodnie z następującym mapowaniem:

| Status zwrotu | Email | Push Notification | SMS |
|---------------|-------|-------------------|-----|
| label_generated | ✅ | ✅ | ❌ |
| shipped_to_warehouse | ❌ | ✅ | ❌ |
| received_in_warehouse | ✅ | ✅ | ❌ |
| refund_pending | ❌ | ❌ | ❌ |
| refunded | ✅ | ✅ | ✅ |
| refund_failed | ✅ (admin) | ❌ | ❌ |

**Zakładając, że** klient ma wyłączone powiadomienia push w ustawieniach aplikacji
**Kiedy** backend próbuje wysłać push notification
**Wtedy** pomija push notification, ale nadal wysyła email (jeśli zaplanowany)

**Zakładając, że** wysyłane jest powiadomienie push
**Kiedy** backend przygotowuje wiadomość
**Wtedy** treść powiadomienia jest jasna i konkretna:
- "label_generated": "Etykieta zwrotna została wygenerowana. Nadaj paczkę w Paczkomacie."
- "received_in_warehouse": "Twój zwrot został dostarczony do naszego magazynu. Trwa weryfikacja."
- "refunded": "Zwrot środków w wysokości XXX zł został zrealizowany."

**Zakładając, że** wysyłane jest powiadomienie email
**Kiedy** backend przygotowuje email
**Wtedy** email zawiera:
- Numer zwrotu
- Status zwrotu
- Listę zwróconych produktów
- Kwotę zwrotu (jeśli dotyczy)
- Link do śledzenia zwrotu w aplikacji/na stronie

#### Definition of Done

- [ ] Wysyłanie powiadomień push (APNs dla iOS) przy zmianach statusu
- [ ] Wysyłanie emaili zgodnie z tabelą mapowania
- [ ] Opcjonalne wysyłanie SMS dla statusu "refunded"
- [ ] Szablony wiadomości (email templates) dla różnych statusów
- [ ] Personalizacja treści powiadomień (imię klienta, numer zwrotu, kwota)
- [ ] Obsługa preferencji użytkownika (opt-out z powiadomień)
- [ ] Retry logic dla nieudanych wysyłek (3 próby)
- [ ] Logowanie wszystkich wysłanych powiadomień (audit trail)
- [ ] Code review przeprowadzony i zatwierdzony
- [ ] Testy jednostkowe dla logiki powiadomień
- [ ] Testy integracyjne z serwisami APNs, email (SendGrid/SMTP)
- [ ] Dokumentacja szablonów powiadomień
- [ ] Monitoring wysyłki powiadomień (delivery rate, failures)



