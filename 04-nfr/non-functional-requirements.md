# Wymagania niefunkcjonalne (NFR) - Dostawa przez InPost


## 1. Wydajność

### 1.1 Response Time (czas odpowiedzi)

| Endpoint/Funkcja | Target (p95) | Uwagi |
|------------------|--------------|-------|
| GET /inpost/parcel-lockers | < 1s | Włączając geocoding + InPost API |
| GET /inpost/parcel-lockers (cache) | < 300ms | Cache Redis hit |
| POST /orders (z Paczkomatem) | < 2s | Bez generowania etykiety |
| iOS: Wybór Paczkomatu | < 3s | Od kliknięcia do wyświetlenia listy |

### 1.2 Cache Strategy

- **Lista Paczkomatów:**
  - TTL: 1 godzina
  - Cache key: hash(address) + radius
  - Error handling: Zwrot 503 gdy InPost API niedostępne

- **iOS App:**
  - Offline mode: brak (wymaga połączenia dla fresh data)

### 1.3 Throughput

- **API Backend:** Min 100 req/s na instancję dla `/inpost/parcel-lockers`
- **iOS:** Płynne przewijanie listy 100+ Paczkomatów (60 FPS)

---

## 2. Bezpieczeństwo

### 2.1 Transport Security

- **HTTPS only** - TLS 1.2+ (preferowany TLS 1.3)
- Wszystkie komunikacje z InPost API przez HTTPS
- Certificate validation obowiązkowy

### 2.2 Autentykacja

- **JWT Tokens:**
  - Access token TTL: 1 godzina
  - Refresh token TTL: 30 dni
  - Token storage (iOS): Keychain

- **InPost API Keys:**
  - Przechowywanie: Environment variables / Secrets Manager
  - Brak hardcoded secrets w kodzie
  - Rotacja: co 90 dni

### 2.3 Input Validation

- **Geocoding:**
  - Walidacja formatu adresu przed geocodingiem
  - Sanityzacja parametrów address/postal_code
  - Limit długości: address max 200 znaków

- **Rate Limiting:**
  - `/inpost/parcel-lockers`: 60 req/min na użytkownika
  - Odpowiedź 429 Too Many Requests z Retry-After header

---

## 3. Dostępność i Niezawodność

### 3.1 SLA

- **Backend API:** 99.5% uptime (monthly) ≈ 3.6h downtime/miesiąc
- **Planned maintenance:** Poza godzinami szczytu (02:00-06:00), komunikat 24h wcześniej

### 3.2 Fault Tolerance

- **InPost API niedostępne:**
  - Retry logic: 3 próby z exponential backoff (1s, 2s, 4s)
  - Zwrot błędu 503 Service Unavailable
  - Komunikat dla użytkownika: "Nie udało się pobrać listy Paczkomatów. Spróbuj ponownie."
  - Przycisk "Spróbuj ponownie"

- **Circuit Breaker:**
  - Trigger: 5 kolejnych błędów InPost API
  - Stan otwarty: 60 sekund
  - Half-open: 1 test request

- **Retry Logic:**
  - InPost API 5xx errors: 3 próby z exponential backoff (1s, 2s, 4s)
  - Timeout: 5 sekund per request

### 3.3 Graceful Degradation

- **InPost API down → błąd 503** z komunikatem + opcja retry
- **Geocoding failed → błąd 422** z sugestią sprawdzenia adresu
- **Paczkomat niedostępny → błąd 422** z sugestią wyboru innego

---

## 4. Integracja z InPost API

### 4.1 API Requirements

- **Endpoint:** `/v1/points` (lista Paczkomatów)
- **Expected uptime:** 99%+
- **Typical response time:** 200-500ms
- **Timeout:** 5 sekund

### 4.2 Error Handling

| Error Type | Action |
|------------|--------|
| 4xx (Client Error) | Log + zwróć błąd do klienta (walidacja) |
| 500 (Server Error) | Retry 3x z backoff, potem zwróć 503 |
| 503 (Unavailable) | Retry 3x z backoff, potem zwróć 503 |
| Timeout (>5s) | Retry 3x z backoff, potem zwróć 503 |

### 4.3 Data Consistency

- **Geocoding:**
  - Usługa: Google Maps Geocoding API / OpenStreetMap Nominatim
  - Walidacja country code: tylko "PL"
  - Zwrot pustej listy dla adresów poza Polską

- **Distance Calculation:**
  - Algorytm: Haversine (odległość w linii prostej)
  - Precision: metry (integer)
  - Sortowanie: rosnąco (najbliższe pierwsze)

### 4.4 Rate Limiting InPost

- **Limit:** Sprawdzić z InPost (typowo 1000 req/h)
- **Monitoring:** Track remaining quota w response headers
- **Action przy 429:** Respect Retry-After header, cache aggressively

---

## 5. Monitoring i Metryki

### 5.1 Kluczowe Metryki

| Metryka | Tool | Alert Threshold |
|---------|------|-----------------|
| InPost API Response Time | APM | > 1s (p95) |
| InPost API Error Rate | Logging | > 5% |
| InPost API Availability | Uptime Monitor | < 99% |
| Geocoding Failures | Logging | > 10% |
| Cache Hit Rate | Redis | < 70% |

### 5.2 Business Metrics

- **InPost Adoption Rate:** % zamówień z Paczkomatem vs. inne metody
- **Top 10 Paczkomatów:** Najpopularniejsze lokalizacje
- **Failed Selections:** % przypadków gdzie Paczkomat był niedostępny przy checkout

### 5.3 Logging

- **Poziomy:**
  - ERROR: InPost API down, geocoding failed
  - WARNING: Cache miss, slow response (>2s)
  - INFO: Parcel locker selected, order created

- **Context:**
  - Request ID (dla tracingu)
  - User ID
  - Geocoded coordinates (dla debugowania)
  - InPost API response time

---

## 6. Aplikacja iOS

### 6.1 Performance

- **Start time:** < 2s do first meaningful paint
- **Memory usage:** < 150MB podczas przeglądania Paczkomatów
- **Battery drain:** < 5% per godzinę aktywnego użytkowania

### 6.2 Accessibility

- **VoiceOver support:** Wszystkie elementy UI opisane
- **Dynamic Type:** Skalowanie czcionek
- **Contrast:** Min 4.5:1 (WCAG AA)
- **Touch targets:** Min 44x44 pt

### 6.3 Compatibility

- **iOS Version:** Min 15.0 (wspiera 95%+ urządzeń)
- **Devices:** iPhone SE (2020) → iPhone 15 Pro Max
- **Orientations:** Portrait (primary), Landscape (optional)

---

## 7. Skalowalność

### 7.1 Backend

- **Horizontal scaling:** Min 2, max 10 instancji (auto-scaling)
- **Trigger:** CPU > 75% przez 5 minut → +1 instancja
- **Stateless:** Session w Redis (umożliwia load balancing)

### 7.2 Database

- **Read replicas:** 2-3 dla odczytów (order tracking)
- **Indexy:**
  - `sales_order.parcel_locker_id` (dla raportowania)
  - `sales_order.delivery_method` + `created_at` (composite)

### 7.3 Cache

- **Redis Cluster:** 1 master + 1 replica (minimum)
- **Eviction policy:** allkeys-lru
- **Max memory:** 4GB
