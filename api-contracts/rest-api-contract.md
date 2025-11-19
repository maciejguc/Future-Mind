# Kontrakt API REST - Integracja FMedia z InPost (Dostawa)

## Informacje ogólne

### Base URL

```
Production:  https://api.fmedia.pl/v1
Staging:     https://api-staging.fmedia.pl/v1
```

### Content-Type

```http
Content-Type: application/json
Accept: application/json
```

### Autentykacja

Wszystkie żądania wymagają tokena JWT w headerze:

```http
Authorization: Bearer {token}
```

---

## Endpointy - Paczkomaty InPost

### GET /inpost/parcel-lockers

Pobiera listę dostępnych Paczkomatów InPost na podstawie adresu dostawy.

#### Query Parameters

| Parametr | Typ | Wymagany | Opis |
|----------|-----|----------|------|
| `address` | string | Tak* | Pełny adres dostawy (np. "ul. Marszałkowska 1, 00-001 Warszawa") |
| `postal_code` | string | Tak* | Kod pocztowy (alternatywa dla address) |
| `city` | string | Tak* | Miasto (wymagane z postal_code) |
| `radius` | integer | Nie | Promień wyszukiwania w km (domyślnie: 5, max: 25) |
| `limit` | integer | Nie | Liczba wyników (domyślnie: 10, max: 50) |

*Wymagany `address` LUB (`postal_code` + `city`)

#### Przykładowe żądanie

```http
GET /v1/inpost/parcel-lockers?address=ul. Marszałkowska 1, 00-001 Warszawa&radius=5
Authorization: Bearer {token}
```

#### Response (200 OK)

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

#### Error Responses

**400 Bad Request** - nieprawidłowe parametry
```json
{
  "error": {
    "code": "INVALID_PARAMETERS",
    "message": "Missing required parameter: address or postal_code+city"
  }
}
```

**422 Unprocessable Entity** - błąd geocodingu
```json
{
  "error": {
    "code": "GEOCODING_FAILED",
    "message": "Unable to geocode address. Please check the address and try again."
  }
}
```

**503 Service Unavailable** - InPost API niedostępne
```json
{
  "error": {
    "code": "SERVICE_UNAVAILABLE",
    "message": "InPost service temporarily unavailable. Please try again later.",
    "retry_after": 60
  }
}
```

---

### GET /inpost/parcel-lockers/{lockerId}

Pobiera szczegółowe informacje o konkretnym Paczkomacie.

#### Response (200 OK)

```json
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
  "description": "Przy stacji BP",
  "available": true,
  "operating_hours": "24/7",
  "accessibility": {
    "wheelchair_accessible": true,
    "parking_nearby": true
  }
}
```

**404 Not Found** - Paczkomat nie istnieje
```json
{
  "error": {
    "code": "LOCKER_NOT_FOUND",
    "message": "Parcel locker with ID '{lockerId}' not found."
  }
}
```

---

## Endpointy - Zamówienia

### POST /orders

Tworzy nowe zamówienie z dostawą do Paczkomatu InPost.

#### Request Body (tylko istotne pola dla InPost)

```json
{
  "items": [
    {
      "product_id": "12345",
      "quantity": 1,
      "price": 2999.00
    }
  ],
  "customer": {
    "email": "customer@example.com",
    "first_name": "Jan",
    "last_name": "Kowalski",
    "phone": "+48500123456"
  },
  "shipping_address": {
    "street": "ul. Testowa 123",
    "city": "Warszawa",
    "postal_code": "00-123",
    "country": "PL"
  },
  "delivery_method": "INPOST_LOCKER",
  "parcel_locker": {
    "id": "WAW1234",
    "name": "Paczkomat WAW1234",
    "address": "ul. Biblioteczna 10, 00-123 Warszawa"
  },
  "payment_method": "CARD"
}
```

#### Response (201 Created)

```json
{
  "order_id": "ORD-2025-123456",
  "status": "pending_payment",
  "total_amount": 3008.99,
  "currency": "PLN",
  "payment_url": "https://payments.fmedia.pl/pay/abc123...",
  "created_at": "2025-11-16T14:30:00Z"
}
```

#### Error Responses

**422 Unprocessable Entity** - produkty za duże dla Paczkomatu
```json
{
  "error": {
    "code": "PRODUCTS_TOO_LARGE",
    "message": "Selected products are too large for the chosen parcel locker.",
    "details": {
      "max_dimensions": "41x38x64 cm",
      "suggestion": "Please choose courier delivery for these products."
    }
  }
}
```

**422 Unprocessable Entity** - Paczkomat niedostępny
```json
{
  "error": {
    "code": "LOCKER_UNAVAILABLE",
    "message": "Selected parcel locker is currently unavailable or full.",
    "details": {
      "locker_id": "WAW1234",
      "status": "full"
    }
  }
}
```

---

### GET /orders/{orderId}

Pobiera szczegóły zamówienia (w kontekście InPost - tracking).

#### Response (200 OK) - fragment

```json
{
  "order_id": "ORD-2025-123456",
  "status": "shipped",
  "delivery_method": "INPOST_LOCKER",
  "parcel_locker": {
    "id": "WAW1234",
    "name": "Paczkomat WAW1234",
    "address": "ul. Biblioteczna 10, 00-123 Warszawa"
  },
  "shipping": {
    "tracking_number": "INP123456789012345",
    "carrier": "InPost",
    "status": "in_transit",
    "estimated_delivery": "2025-11-18T18:00:00Z",
    "tracking_url": "https://tracking.inpost.pl/INP123456789012345"
  }
}
```

---

## Schematy danych

### Delivery Method Codes

| Kod | Opis | Koszt |
|-----|------|-------|
| `INPOST_LOCKER` | Paczkomat InPost 24/7 | 9,99 zł |
| `COURIER` | Wysyłka kurierem | 12,99 zł |
| `STORE_PICKUP` | Odbiór osobisty | 0,00 zł |

### Order Status Codes (InPost relevant)

| Kod | Opis |
|-----|------|
| `pending_payment` | Oczekuje na płatność |
| `paid` | Opłacone |
| `processing` | W realizacji (magazyn) |
| `shipped` | Wysłane do Paczkomatu |
| `delivered` | Dostarczone do Paczkomatu |

---

## Kody błędów

### HTTP Status Codes

| Kod | Znaczenie | Kiedy używać |
|-----|-----------|--------------|
| 200 | OK | Żądanie wykonane pomyślnie |
| 201 | Created | Zamówienie utworzone |
| 400 | Bad Request | Nieprawidłowe parametry |
| 401 | Unauthorized | Brak/nieprawidłowy token |
| 404 | Not Found | Zasób nie istnieje |
| 422 | Unprocessable Entity | Walidacja biznesowa (Paczkomat pełny, produkty za duże) |
| 503 | Service Unavailable | InPost API niedostępne |

### Error Codes (InPost specific)

| Kod | HTTP | Opis |
|-----|------|------|
| `INVALID_PARAMETERS` | 400 | Nieprawidłowe parametry (address/postal_code) |
| `GEOCODING_FAILED` | 422 | Nie można zgeokodować adresu |
| `LOCKER_NOT_FOUND` | 404 | Paczkomat nie istnieje |
| `PRODUCTS_TOO_LARGE` | 422 | Produkty za duże dla Paczkomatu |
| `LOCKER_UNAVAILABLE` | 422 | Paczkomat niedostępny/pełny |
| `SERVICE_UNAVAILABLE` | 503 | InPost API niedostępne |

---

## Endpointy - Zwroty

### POST /orders/{orderId}/returns

Inicjuje zwrot produktów z zamówienia.

#### Path Parameters

| Parametr | Typ | Wymagany | Opis |
|----------|-----|----------|------|
| `orderId` | string | Tak | ID zamówienia (np. "ORD-2025-123456") |

#### Request Body

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

#### Response (201 Created)

```json
{
  "return_id": "RET-2025-001234",
  "status": "pending",
  "items": [
    {
      "product_id": "12345",
      "product_name": "iPhone 15 Pro",
      "quantity": 1,
      "price": 299.99
    }
  ],
  "estimated_refund": 299.99,
  "parcel_locker": {
    "id": "WAW1234",
    "name": "Paczkomat WAW1234",
    "address": "ul. Biblioteczna 10, 00-123 Warszawa"
  },
  "created_at": "2025-11-16T10:30:00Z"
}
```

#### Error Responses

**400 Bad Request** - nieprawidłowe dane
```json
{
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Missing required field: items"
  }
}
```

**422 Unprocessable Entity** - okres zwrotu upłynął
```json
{
  "error": {
    "code": "RETURN_PERIOD_EXPIRED",
    "message": "Return period has expired. Returns are accepted within 14 days of delivery."
  }
}
```

**422 Unprocessable Entity** - produkt nie podlega zwrotowi
```json
{
  "error": {
    "code": "PRODUCT_NOT_RETURNABLE",
    "message": "One or more products in the request are not eligible for return.",
    "details": {
      "non_returnable_products": ["12346"]
    }
  }
}
```

---

### POST /returns/{returnId}/label

Generuje etykietę zwrotną InPost dla zwrotu.

#### Path Parameters

| Parametr | Typ | Wymagany | Opis |
|----------|-----|----------|------|
| `returnId` | string | Tak | ID zwrotu (np. "RET-2025-001234") |

#### Response (201 Created)

```json
{
  "return_id": "RET-2025-001234",
  "tracking_number": "INP123456789012345",
  "label_url": "https://inpost.pl/labels/INP123456789012345.pdf",
  "qr_code": "data:image/png;base64,iVBORw0KG...",
  "status": "label_generated"
}
```

#### Error Responses

**422 Unprocessable Entity** - Paczkomat niedostępny
```json
{
  "error": {
    "code": "LOCKER_UNAVAILABLE",
    "message": "Selected parcel locker is unavailable. Please choose another one.",
    "details": {
      "locker_id": "WAW1234",
      "status": "full"
    }
  }
}
```

**503 Service Unavailable** - InPost API niedostępne
```json
{
  "error": {
    "code": "SERVICE_UNAVAILABLE",
    "message": "InPost service temporarily unavailable. Please try again later.",
    "retry_after": 60
  }
}
```

---

### GET /returns/{returnId}

Pobiera szczegóły zwrotu wraz z historią statusów.

#### Path Parameters

| Parametr | Typ | Wymagany | Opis |
|----------|-----|----------|------|
| `returnId` | string | Tak | ID zwrotu (np. "RET-2025-001234") |

#### Response (200 OK)

```json
{
  "return_id": "RET-2025-001234",
  "order_id": "ORD-2025-123456",
  "status": "received_in_warehouse",
  "tracking_number": "INP123456789012345",
  "items": [
    {
      "product_id": "12345",
      "product_name": "iPhone 15 Pro",
      "quantity": 1,
      "price": 299.99
    }
  ],
  "reason": "Produkt niezgodny z opisem",
  "reason_details": "Kolor inny niż na zdjęciu",
  "estimated_refund": 299.99,
  "parcel_locker": {
    "id": "WAW1234",
    "name": "Paczkomat WAW1234",
    "address": "ul. Biblioteczna 10, 00-123 Warszawa"
  },
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
  ],
  "created_at": "2025-11-16T10:30:00Z",
  "updated_at": "2025-11-17T09:15:00Z"
}
```

**404 Not Found** - zwrot nie istnieje
```json
{
  "error": {
    "code": "RETURN_NOT_FOUND",
    "message": "Return with ID '{returnId}' not found."
  }
}
```

---

### GET /returns

Pobiera listę zwrotów użytkownika.

#### Query Parameters

| Parametr | Typ | Wymagany | Opis |
|----------|-----|----------|------|
| `customer_id` | string | Tak | ID użytkownika |
| `status` | string | Nie | Filtrowanie po statusie (pending, label_generated, shipped_to_warehouse, received_in_warehouse, approved, refunded) |
| `limit` | integer | Nie | Liczba wyników (domyślnie: 10, max: 50) |
| `offset` | integer | Nie | Offset dla paginacji (domyślnie: 0) |

#### Response (200 OK)

```json
{
  "data": [
    {
      "return_id": "RET-2025-001234",
      "order_id": "ORD-2025-123456",
      "status": "refunded",
      "tracking_number": "INP123456789012345",
      "estimated_refund": 299.99,
      "created_at": "2025-11-16T10:30:00Z",
      "updated_at": "2025-11-18T10:05:00Z"
    },
    {
      "return_id": "RET-2025-001200",
      "order_id": "ORD-2025-123400",
      "status": "shipped_to_warehouse",
      "tracking_number": "INP123456789012300",
      "estimated_refund": 599.00,
      "created_at": "2025-11-10T14:20:00Z",
      "updated_at": "2025-11-11T08:30:00Z"
    }
  ],
  "meta": {
    "total": 2,
    "returned": 2,
    "limit": 10,
    "offset": 0
  }
}
```

---

### POST /webhooks/inpost/tracking

Webhook od InPost informujący o zmianie statusu przesyłki zwrotnej.

**Uwaga:** Ten endpoint jest wywoływany przez InPost, nie przez aplikację mobilną.

#### Request Body (przykład z InPost)

```json
{
  "tracking_number": "INP123456789012345",
  "status": "delivered",
  "event_time": "2025-11-17T09:15:00Z",
  "signature": "sha256_signature_from_inpost"
}
```

#### Response (200 OK)

```json
{
  "received": true,
  "timestamp": "2025-11-17T09:15:05Z"
}
```

#### Error Responses

**400 Bad Request** - nieprawidłowe dane webhook
```json
{
  "error": {
    "code": "INVALID_WEBHOOK",
    "message": "Invalid webhook signature or payload."
  }
}
```

---

## Schematy danych

### Return Status Codes

| Kod | Opis biznesowy |
|-----|----------------|
| `pending` | Zwrot utworzony, oczekuje na wygenerowanie etykiety |
| `label_generated` | Etykieta zwrotna wygenerowana, oczekuje na nadanie |
| `shipped_to_warehouse` | Paczka nadana w Paczkomacie, w transporcie |
| `received_in_warehouse` | Paczka dostarczona do magazynu, trwa weryfikacja |
| `approved` | Zwrot zaakceptowany przez magazyn, oczekuje na zwrot środków |
| `refunded` | Środki zwrócone klientowi |
| `refund_failed` | Błąd podczas zwrotu środków (wymaga ręcznej interwencji) |
| `rejected` | Zwrot odrzucony (np. produkt uszkodzony przez klienta) |

### Return Reason Codes

| Kod | Opis |
|-----|------|
| `not_as_described` | Produkt niezgodny z opisem |
| `defective` | Produkt uszkodzony/wadliwy |
| `wrong_item` | Pomyłka przy zamówieniu |
| `better_price` | Znalazłem lepszą ofertę |
| `changed_mind` | Rezygnacja z zakupu |
| `other` | Inna przyczyna |

---

## Wymagania techniczne

### Performance

- **GET /inpost/parcel-lockers:** < 1s (p95) włącznie z geocodingiem
- **POST /orders:** < 2s (p95)
- **Cache TTL:** 1 godzina dla listy Paczkomatów

### Retry Logic

- **InPost API errors (5xx):** 3 próby z exponential backoff (1s, 2s, 4s)
- **Timeout:** 5 sekund dla żądań do InPost API
- **Circuit breaker:** Po 5 kolejnych błędach → zwrot błędu 503
- **Error response:** Standardowy komunikat "InPost service unavailable. Please try again."

### Security

- **HTTPS only** (TLS 1.2+)
- **JWT tokens:** Access token TTL 1h, Refresh token TTL 30 dni
- **Rate limiting:** 60 req/min dla `/inpost/parcel-lockers`
