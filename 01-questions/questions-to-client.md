# Pytania i uwagi do klienta - Integracja FMedia z InPost


## 1. Integracja z InPost - Aspekty techniczne

### 1.1 API i dokumentacja

- 1.1.1 Czy FMedia posiada już podpisaną umowę z InPost?

### 1.2 Zakres usług InPost

- 1.2.1 Czy integracja ma obejmować tylko odbiór paczek, czy również nadawanie zwrotów przez Paczkomaty?
- 1.2.2 Czy planowana jest integracja z mapą Paczkomatów InPost (Geowidget)?

### 1.3 Limitacje i ograniczenia

- 1.3.1 Czy wszystkie produkty w ofercie FMedia mogą być dostarczane przez Paczkomaty, czy są kategorie wykluczone (np. AGD wielkogabarytowe)?
- 1.3.2 Czy istnieją ograniczenia geograficzne (tylko Polska, czy również inne kraje)?



## 2. Integracja z Magento 2

### 2.1 Obecna konfiguracja platformy

- **2.1.1** Jaka dokładnie wersja Magento 2 jest obecnie używana (np. 2.4.6, 2.4.7)?
- **2.1.2** Czy platforma działa w wersji Open Source czy Commerce (Adobe Commerce)?
- **2.1.3** Jakie moduły/rozszerzenia związane z dostawą są obecnie zainstalowane?
- **2.1.4** Czy integracja powinna zostać przeprowadzona z wykorzystaniem gotowej wtyczki InPost do Magento czy w pełni custom? 

### 2.2 Architektura i infrastruktura

- **2.2.1** Czy Magento jest hostowane on-premise, czy w chmurze (AWS, Azure, Google Cloud)?
- **2.2.2** Jaka jest architektura środowiska (liczba serwerów aplikacyjnych, load balancing)?
- **2.2.3** Czy używany jest Varnish Cache lub inny mechanizm cache'owania?
- **2.2.4** Czy lista paczkomatów powinna być cached czy pobierana za każdym razem przy wyborze metody dostawy?
- **2.2.5** Jakie środowiska są dostępne (DEV, STAGING, PRODUCTION)?

### 2.3 Ograniczenia techniczne Magento

- **2.3.1** Czy są jakieś znane ograniczenia wydajnościowe obecnej instalacji Magento?
- **2.3.2** Jaki jest średni czas odpowiedzi API Magento obecnie?
- **2.3.3** Czy Magento jest zintegrowane z zewnętrznymi systemami (ERP, WMS, CRM)? Jeśli tak, z jakimi?
- **2.3.4** Czy istnieją customizacje w module checkout, które mogą wpłynąć na integrację?

### 2.4 Proces wdrożenia

- **2.4.1** Czy planowana jest aktualizacja Magento w najbliższym czasie?
- **2.4.2** Jaki jest preferowany sposób wdrożenia zmian (deployment pipeline, CI/CD)?



## 3. Wymagania biznesowe

### 3.1 Polityka cenowa dostaw

- **3.1.1** Jaki będzie koszt dostawy do Paczkomatu InPost dla klienta?
  - Stała cena (9,99 zł jak na makiecie)?
  - Zależna od wartości koszyka?
  - Zależna od rozmiaru produktu?
  - Darmowa dostawa powyżej określonej kwoty?
- **3.1.2** Na jakiej zasadzie system określa rozmiar paczki w przypadku zamówienia wielu produktów?
- **3.1.3** Czy cena dostawy będzie różna dla różnych kategorii produktów?
- **3.1.4** Jak często mogą zmieniać się ceny dostaw (np. promocje sezonowe)?
- **3.1.5** Czy koszty dostaw będą zarządzane przez panel administracyjny Magento? Jeżeli tak to w jakim zakresie (np. globalnie, dla kategorii produktu)?

### 3.2 Polityka zwrotów

- **3.2.1** Czy w pierwszej fazie projektu mają zostać obsłużone również zwroty przez InPost?
- **3.2.2** W jakim zakresie zwroty powinny być zintegrowane ze sklepem? Jakie możliwości i informacje klient powinien mieć dostępne z poziomu aplikacji sklepu?
- **3.2.3** Jak wygląda obecny proces zwrotu produktu?
- **3.2.4** Jaki jest aktualnie okres na zwrot produktów (14 dni, 30 dni)? Od czego to zależy?
- **3.2.5** Czy koszt zwrotu ponosi klient, czy firma? Od czego to zależy? Jakie scenariusze są możliwe?
- **3.2.6** Czy wszystkie produkty mogą być zwracane przez Paczkomaty (ograniczenia gabarytowe)?

### 3.3 Obsługa klienta

- **3.3.1** Czy planowane jest call center/helpdesk do obsługi problemów z dostawą?
- **3.3.2** Jak będą obsługiwane sytuacje, gdy paczka nie zostanie odebrana z Paczkomatu w terminie?

### 3.4 Metryki sukcesu projektu

- **3.4.1** Jakie KPI będą mierzone po wdrożeniu (np. % zamówień przez Paczkomaty, satysfakcja klientów)?
- **3.4.2** Jaki jest oczekiwany wzrost konwersji po dodaniu opcji Paczkomatów?
- **3.4.3** Czy prowadzone będą testy A/B nowej funkcjonalności?



## 4. Wymagania techniczne

### 4.1 Wydajność i skalowalność

- **4.1.1** Jaki jest obecny wolumen zamówień dziennie/miesięcznie?
- **4.1.2** Jaki wzrost ruchu jest przewidywany po wdrożeniu InPost?
- **4.1.3** Jakie są wymagania dotyczące czasu odpowiedzi API (np. <500ms)?
- **4.1.4** Jaki wolumen zamówień jest przewidziany przy szczytach zakupowych (Black Friday, święta)?

### 4.2 Monitoring i logowanie

- **4.2.1** Jakie narzędzia monitoringu są obecnie używane (np. New Relic, Datadog, ELK)?
- **4.2.2** Czy integracja z InPost powinna być monitorowana osobno?
- **4.2.3** Czy istnieją szczególne wymagania dot. logowania?
  - Dostęp do logów
  - Kategoryzacja
  - Zapisywanie i okres przechowywania

### 4.3 Disaster recovery i backup

- **4.3.1** Jaki jest plan awaryjny w przypadku niedostępności API InPost?
- **4.2.2** Czy system powinien obsługiwać fallback do innych form dostawy (np. kurier)?
- **4.3.3** Jak często wykonywane są backupy bazy danych Magento?



## 5. Płatności

### 5.1 Integracja z bramkami płatności

- **5.1.1** Jakie bramki płatności są obecnie zintegrowane z Magento (Przelewy24, PayU, Stripe)?
- **5.1.2** Czy płatność za pobraniem będzie dostępna dla dostaw do Paczkomatów?
- **5.1.3** Czy InPost Pay będzie dostępne jako opcja płatności?
- **5.1.4** Czy istnieją ograniczenia płatności dla określonych metod dostawy?

### 5.2 Zwroty płatności

- **5.2.1** Jak będzie wyglądać proces zwrotu pieniędzy (automatyczny zwrot na kartę, przelew)?
- **5.2.2** W jakim czasie klient powinien otrzymać zwrot środków?
- **5.2.3** Czy zwrot obejmuje również koszt dostawy?



## 6. Zwroty przez Paczkomaty

### 6.1 Proces zwrotu

- **6.1.1** Gdzie klient będzie inicjował zwrot (aplikacja mobilna, email)?
- **6.1.2** Czy etykieta będzie wysyłana mailem, czy dostępna do pobrania w aplikacji?
- **6.1.3** Czy klient będzie mógł wybrać dowolny Paczkomat do zwrotu, czy tylko ten, z którego odebrał paczkę?

### 6.2 Koszty i logistyka zwrotów

- **6.2.1** Kto pokrywa koszt wysyłki zwrotnej przez Paczkomat (klient/FMedia)?
- **6.2.2** Jak będzie wyglądać proces odbioru zwrotów z Paczkomatów przez FMedia?
- **6.2.3** Czy istnieje integracja z systemem WMS dla obsługi zwrotów?
- **6.2.4** Jak szybko zwrócony produkt musi być zweryfikowany i zwrócony do magazynu?

### 6.3 Status zwrotu

- **6.3.1** Czy klient będzie mógł śledzić status zwrotu (nadana, w transporcie, odebrana przez magazyn)?
- **6.3.3** Czy klient otrzyma powiadomienie o każdej zmianie statusu zwrotu? Czy powiadomienia mają być wysyłane przez sklep czy InPost?



## 7. Aplikacja mobilna iOS

### 7.1 Wymagania techniczne iOS

- **7.1.1** Jaka jest architektura obecnej aplikacji (UIKit, SwiftUI, hybrid)?
- **7.1.2** Czy aplikacja mobilna komunikuje się bezpośrednio z Magento API, czy przez warstwę BFF (Backend for Frontend)?

### 7.2 Design system i UX

- **7.2.1** Czy dostarczone makiety są finalne, czy mogą ulec zmianom?
- **7.2.2** Czy interfejs musi być zgodny z Apple Human Interface Guidelines?
- **7.2.3** Czy planowane są testy UX/UI z rzeczywistymi użytkownikami?

### 7.3 Analytics i monitoring

- **7.3.1** Czy aplikacja mobilna jest zintegrowana z narzędziami analytics (Firebase, Mixpanel, App Center)?
- **7.3.2** Jakie wydarzenia (events) powinny być trackowane w kontekście wyboru Paczkomatu?
- **7.3.3** Czy wymagane jest śledzenie performance aplikacji (crash reporting, APM)?

### 7.4 Testy i wdrożenie

- **7.4.1** Czy planowane są testy beta z grupą użytkowników?
- **7.4.2** Czy wymagane są testy automatyczne (unit tests, UI tests)?



## 8. Harmonogram i budżet

### 8.1 Timeline projektu

- **8.1.1** Jaki jest oczekiwany termin wdrożenia integracji?
- **8.1.2** Czy istnieją jakieś kamienie milowe, które muszą być dotrzymane?
- **8.1.3** Czy możliwe jest wdrożenie fazowe (np. najpierw dostawa, potem zwroty)?

### 8.2 Zasoby

- **8.2.1** Jaki zespół po stronie FMedia będzie zaangażowany w projekt (developerzy, testerzy, product owner)?
- **8.2.2** Czy FMedia ma dedykowanych DevOps/infrastrukturę do wsparcia wdrożenia?
- **8.2.3** Kto będzie odpowiedzialny za testy akceptacyjne (UAT)?



## 9. Uwagi dodatkowe

### 9.1 Ryzyka do rozważenia

- **Wydajność API InPost** - potencjalne opóźnienia w odpowiedziach API mogą wpłynąć na UX
- **Niedostępność Paczkomatów** - jak system powinien reagować, gdy wybrany Paczkomat jest pełny/niedostępny?
- **Caching** - jak bardzo bieżące dane muszą się znaleźć w liście paczkomatów do wyboru?
- **Synchronizacja statusów** - jak często statusy przesyłek będą synchronizowane między InPost a Magento?
- **Obsługa błędów** - jak system powinien reagować na błędy API InPost (retry logic, fallback)?
- **Produkty wielkogabarytowe** - jak system określi, które produkty mogą, a które nie mogą być wysłane do Paczkomatu? Jak będzie obliczany rozmiar paczki w przypadku zamówienia wielu produktów?
 
     
