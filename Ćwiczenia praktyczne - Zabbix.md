# Ćwiczenia praktyczne - Zabbix

## Spis treści
1. [Ćwiczenia podstawowe - Konfiguracja środowiska](#ćwiczenia-podstawowe)
2. [Ćwiczenia średniozaawansowane - Items i Triggery](#ćwiczenia-średniozaawansowane)
3. [Ćwiczenia zaawansowane - Alerty i Automatyzacja](#ćwiczenia-zaawansowane)
4. [Ćwiczenia eksperckie - Scenariusze produkcyjne](#ćwiczenia-eksperckie)

---

## Ćwiczenia podstawowe

### Ćwiczenie 1: Dodanie hosta do monitoringu

**Cel:** Nauka podstawowej konfiguracji monitorowania hosta

**Zadania:**
1. Zainstaluj Zabbix Agent na wybranym hoście (Ubuntu/Debian lub CentOS/RHEL)
2. Skonfiguruj komunikację agent → serwer
3. Dodaj host w interfejsie Zabbix
4. Przypisz szablon "Linux by Zabbix agent"
5. Zweryfikuj czy dane są zbierane (Latest data)

**Kryteria sukcesu:**
- Host widoczny w Monitoring → Hosts z zielonym statusem
- W Latest data widoczne są dane z CPU, RAM, dysku
- Agent.ping zwraca wartość 1

**Czas:** 15-20 minut

---

### Ćwiczenie 2: Tworzenie grup hostów

**Cel:** Organizacja infrastruktury monitoringu

**Zadania:**
1. Stwórz grupy hostów:
   - Serwery produkcyjne
   - Serwery testowe
   - Serwery bazodanowe
2. Przypisz hosty do odpowiednich grup
3. Dodaj host który należy do dwóch grup jednocześnie
4. Użyj filtrowania w Monitoring → Hosts po grupach

**Kryteria sukcesu:**
- Utworzone co najmniej 3 grupy hostów
- Hosty poprawnie przypisane do grup
- Możliwość filtrowania widoku po grupach

**Czas:** 10 minut

---

### Ćwiczenie 3: Własny Item - monitoring procesu

**Cel:** Nauka tworzenia własnych elementów monitoringu

**Zadania:**
1. Odlinkuj szablon od hosta (zostaw host bez template)
2. Stwórz nowy Item który będzie monitorował:
   - Czy działa proces nginx/apache (proc.num[nginx])
   - Liczbę procesów systemowych (proc.num[])
3. Ustaw Update interval na 30s
4. Skonfiguruj typ informacji na Numeric (unsigned)
5. Wykonaj "Execute now" i sprawdź w Latest data

**Kryteria sukcesu:**
- Item poprawnie skonfigurowany
- Dane widoczne w Latest data
- Wartość liczbowa zgodna z rzeczywistością (ps aux | grep nginx)

**Czas:** 15 minut

---

## Ćwiczenia średniozaawansowane

### Ćwiczenie 4: Tworzenie triggerów - monitoring zasobów

**Cel:** Konfiguracja alertów na podstawie zebranych danych

**Zadania:**
1. Stwórz trigger który wykryje:
   - Użycie CPU powyżej 80% przez więcej niż 5 minut
   - Użycie RAM powyżej 90%
   - Wolne miejsce na dysku poniżej 20%
2. Ustaw odpowiednie severity:
   - CPU: Warning
   - RAM: High
   - Dysk: Average
3. Dodaj description z instrukcją rozwiązania problemu

**Przykładowe wyrażenia:**
```
CPU: avg(/Linux by Zabbix agent/system.cpu.util,5m)>80
RAM: last(/Linux by Zabbix agent/vm.memory.size[pavailable])<10
Dysk: last(/Linux by Zabbix agent/vfs.fs.size[/,pfree])<20
```

**Kryteria sukcesu:**
- 3 triggery poprawnie skonfigurowane
- Triggery można przetestować (symulacja obciążenia)
- Alerty widoczne w Monitoring → Problems

**Czas:** 20-25 minut

---

### Ćwiczenie 5: Pulpit nawigacyjny (Dashboard)

**Cel:** Wizualizacja danych monitoringu

**Zadania:**
1. Stwórz nowy Dashboard o nazwie "Monitoring [Twoje Imię]"
2. Dodaj następujące widgety:
   - **Graph** - wykres użycia CPU w czasie
   - **Plain text** - aktualny uptime serwera
   - **Top hosts** - top 5 hostów po użyciu CPU
   - **Problems** - aktualne problemy w infrastrukturze
3. Skonfiguruj automatyczne odświeżanie co 30 sekund
4. Ustaw Dashboard jako domyślny dla swojego użytkownika

**Kryteria sukcesu:**
- Dashboard zawiera minimum 4 różne widgety
- Dane aktualizują się automatycznie
- Dashboard jest wizualnie przejrzysty

**Czas:** 25-30 minut

---

### Ćwiczenie 6: Konfiguracja kanału powiadomień - Email

**Cel:** Uruchomienie powiadomień email

**Zadania:**
1. Skonfiguruj Media Type dla Email (Gmail lub serwer SMTP)
2. W ustawieniach użytkownika dodaj email
3. Stwórz Action która wysyła email gdy:
   - Trigger severity >= Warning
   - Problem występuje dłużej niż 3 minuty
4. Skonfiguruj treść wiadomości z makrami:
   - {HOST.NAME}
   - {TRIGGER.NAME}
   - {TRIGGER.SEVERITY}
   - {EVENT.DATE} {EVENT.TIME}
5. Przetestuj wysyłanie powiadomień

**Kryteria sukcesu:**
- Email poprawnie skonfigurowany
- Otrzymano testowego maila
- Action działa przy wystąpieniu problemu

**Czas:** 20-25 minut

---

### Ćwiczenie 7: SNMP - monitoring urządzenia sieciowego

**Cel:** Konfiguracja monitoringu przez SNMP

**Zadania:**
1. Włącz SNMP na hoście lub użyj symulatora
2. Stwórz nowy host z interfejsem SNMP
3. Przypisz szablon "Linux SNMP"
4. Skonfiguruj SNMP community (public/private)
5. Zweryfikuj zbieranie danych przez SNMP

**Kryteria sukcesu:**
- Host komunikuje się przez SNMP
- Dane widoczne w Latest data
- Możliwość porównania danych z Agent vs SNMP

**Czas:** 20 minut

---

## Ćwiczenia zaawansowane

### Ćwiczenie 8: Monitoring bazy danych MySQL

**Cel:** Konfiguracja specjalistycznego monitoringu

**Zadania:**
1. Zainstaluj MySQL/MariaDB na hoście
2. Stwórz użytkownika Zabbix w bazie danych:
```sql
CREATE USER 'zbx_monitor'@'localhost' IDENTIFIED BY 'password';
GRANT REPLICATION CLIENT,PROCESS,SHOW DATABASES,SHOW VIEW ON *.* TO 'zbx_monitor'@'localhost';
FLUSH PRIVILEGES;
```
3. Przypisz szablon "MySQL by Zabbix agent"
4. Skonfiguruj makro {$MYSQL.PASSWORD}
5. Monitoruj:
   - Liczba połączeń
   - Liczba zapytań per sekunda
   - InnoDB buffer pool
   - Slow queries

**Kryteria sukcesu:**
- Wszystkie items z szablonu zbierają dane
- Widoczne metryki wydajności bazy danych
- Brak błędów w Latest data

**Czas:** 30-35 minut

---

### Ćwiczenie 9: Automatyczne dodawanie hostów (Auto-discovery)

**Cel:** Automatyzacja procesu dodawania hostów

**Zadania:**
1. Skonfiguruj Discovery rule dla sieci 10.123.1.0/24
2. Ustaw sprawdzanie:
   - Zabbix agent (port 10050)
   - ICMP ping
3. Stwórz Discovery action która:
   - Automatycznie dodaje hosty
   - Przypisuje do grupy "Auto-discovered"
   - Dodaje szablon "Linux by Zabbix agent"
4. Uruchom discovery i poczekaj na wyniki

**Kryteria sukcesu:**
- Discovery rule poprawnie skonfigurowane
- Hosty automatycznie dodane
- Szablon automatycznie przypisany

**Czas:** 25-30 minut

---

### Ćwiczenie 10: Low-Level Discovery (LLD) - monitoring dysków

**Cel:** Dynamiczny monitoring zasobów

**Zadania:**
1. Przeanalizuj działanie LLD dla filesystems
2. Stwórz własny LLD rule dla:
   - Wszystkich interfejsów sieciowych
   - Monitorowanie ruchu IN/OUT per interface
3. Skonfiguruj Item prototypes
4. Stwórz Trigger prototypes dla:
   - Wysoki ruch sieciowy > 100MB/s
5. Zweryfikuj automatyczne tworzenie items

**Kryteria sukcesu:**
- LLD rule działa poprawnie
- Items tworzone automatycznie dla każdego interfejsu
- Triggery działają per interface

**Czas:** 35-40 minut

---

### Ćwiczenie 11: Integracja ze Slack/Discord

**Cel:** Alternatywne kanały powiadomień

**Zadania:**
1. Stwórz kanał #zabbix-alerts w Slack lub Discord
2. Skonfiguruj Webhook/Bot
3. W Zabbix skonfiguruj Media Type (Slack/Discord)
4. Dodaj webhook URL do konfiguracji
5. Stwórz Action dla powiadomień Critical
6. Przetestuj wysyłanie alertów

**Bonus:**
- Skonfiguruj różne kanały dla różnych severity
- Dodaj emotikony do wiadomości

**Kryteria sukcesu:**
- Powiadomienia docierają do Slack/Discord
- Wiadomości zawierają wszystkie istotne informacje
- Możliwość kliknięcia w link do problemu

**Czas:** 30 minut

---

## Ćwiczenia eksperckie

### Ćwiczenie 12: Monitoring aplikacji webowej

**Cel:** Kompleksowy monitoring aplikacji

**Zadania:**
1. Zainstaluj aplikację webową (np. WordPress, GitLab)
2. Skonfiguruj monitoring:
   - HTTP check (dostępność strony)
   - Response time
   - Status code monitoring
   - Certyfikat SSL (ważność)
3. Stwórz trigger dla:
   - Response time > 2s
   - HTTP status != 200
   - Certyfikat wygasa za mniej niż 30 dni
4. Dodaj Web scenario dla:
   - Logowanie do aplikacji
   - Kliknięcie w 3 różne strony
5. Stwórz dedykowany Dashboard dla aplikacji

**Kryteria sukcesu:**
- Wszystkie aspekty aplikacji monitorowane
- Web scenario działa poprawnie
- Alerty działają dla każdego przypadku

**Czas:** 45-60 minut

---

### Ćwiczenie 13: Monitorowanie logów (Log monitoring)

**Cel:** Analiza logów systemowych w czasie rzeczywistym

**Zadania:**
1. Skonfiguruj Item do monitorowania /var/log/auth.log
2. Użyj log[] key z parametrami:
   - Monitor failed SSH login attempts
   - Monitor sudo commands
3. Stwórz triggery dla:
   - 5+ nieudanych prób logowania w ciągu 5 minut
   - Wykrycie słowa "CRITICAL" w logach
4. Użyj wyrażeń regularnych w triggers
5. Skonfiguruj Value mapping

**Przykładowy Item key:**
```
log[/var/log/auth.log,"Failed password",,,skip,\d+,]
```

**Kryteria sukcesu:**
- Items zbierają dane z logów
- Triggery reagują na wzorce w logach
- Możliwość przejrzenia historii logów

**Czas:** 35-40 minut

---

### Ćwiczenie 14: Macros i zmienne - dynamiczna konfiguracja

**Cel:** Wykorzystanie zmiennych w konfiguracji

**Zadania:**
1. Stwórz globalne macro:
   - {$CPU.THRESHOLD} = 80
   - {$RAM.THRESHOLD} = 90
   - {$DISK.THRESHOLD} = 20
2. Zmodyfikuj istniejące triggery aby używały macro
3. Stwórz host-level macro który nadpisuje globalne
4. Użyj macro w nazwach triggerów i opisach
5. Przetestuj działanie po zmianie wartości macro

**Kryteria sukcesu:**
- Macro poprawnie zdefiniowane
- Triggery używają macro zamiast hardcoded values
- Zmiana macro wpływa na wszystkie triggery

**Czas:** 25 minut

---

### Ćwiczenie 15: Zarządzanie użytkownikami i uprawnieniami

**Cel:** Konfiguracja kontroli dostępu

**Zadania:**
1. Stwórz 3 grupy użytkowników:
   - Admini (pełen dostęp)
   - Operatorzy (odczyt + akcje)
   - Audytorzy (tylko odczyt)
2. Stwórz po 1 użytkowniku w każdej grupie
3. Skonfiguruj uprawnienia dla każdej grupy:
   - Dostęp do konkretnych grup hostów
   - Możliwość wykonywania akcji
4. Przetestuj dostęp logując się jako różni użytkownicy

**Kryteria sukcesu:**
- 3 grupy użytkowników z różnymi uprawnieniami
- Użytkownicy widzą tylko swoje zasoby
- Audytorzy nie mogą modyfikować konfiguracji

**Czas:** 30 minut

---

### Ćwiczenie 16: Maintenance windows

**Cel:** Zarządzanie oknami konserwacyjnymi

**Zadania:**
1. Zaplanuj maintenance window na wybranym hoście
2. Skonfiguruj:
   - Jednorazowe okno (tonight 22:00-23:00)
   - Cykliczne okno (każda sobota 2:00-4:00)
3. Wybierz typ:
   - With data collection (zbiera dane, nie wysyła alertów)
   - No data collection
4. Dodaj hosty/grupy do maintenance
5. Zweryfikuj że alerty są zatrzymane podczas maintenance

**Kryteria sukcesu:**
- Maintenance window aktywne
- Brak alertów podczas okna
- Po zakończeniu alertowanie wraca do normy

**Czas:** 20 minut

---

## Projekt końcowy

### Ćwiczenie 17: Kompleksowe środowisko monitoringu

**Cel:** Połączenie wszystkich poznanych elementów

**Scenariusz:**
Jesteś administratorem małej firmy. Masz do monitorowania:
- 3 serwery webowe (nginx)
- 1 serwer bazodanowy (MySQL)
- 1 serwer backupowy
- Router (SNMP)

**Zadania:**

1. **Infrastruktura (20 pkt)**
   - Dodaj wszystkie hosty
   - Stwórz logiczną strukturę grup
   - Przypisz odpowiednie szablony

2. **Monitoring (30 pkt)**
   - Skonfiguruj monitoring zasobów (CPU, RAM, Disk)
   - Monitoruj procesy nginx i mysql
   - Skonfiguruj monitoring połączeń bazodanowych
   - Dodaj monitoring dostępności HTTP
   - Skonfiguruj SNMP dla routera

3. **Alerty (25 pkt)**
   - Stwórz triggery dla wszystkich krytycznych zasobów
   - Ustaw odpowiednie severity
   - Skonfiguruj 2 kanały powiadomień (email + slack/discord)
   - Stwórz Actions z różnymi regułami eskalacji

4. **Wizualizacja (15 pkt)**
   - Stwórz Dashboard "Przegląd infrastruktury"
   - Dodaj wykresy dla kluczowych metryk
   - Stwórz widok problemów
   - Dodaj widget z mapą sieci

5. **Automatyzacja (10 pkt)**
   - Skonfiguruj Auto-discovery dla nowych serwerów
   - Użyj macro dla progów alertów
   - Zaplanuj maintenance windows na weekend

**Bonus:**
- Monitoring certyfikatów SSL
- Web scenario dla aplikacji webowej
- Custom external script sprawdzający backup

**Kryteria sukcesu:**
- Wszystkie hosty monitorowane
- Alerty działają poprawnie
- Dashboard przejrzysty i funkcjonalny
- Powiadomienia docierają na czas
- Automatyzacja zmniejsza ręczną pracę

**Czas:** 2-3 godziny

**Prezentacja:**
Po zakończeniu przygotuj 5-minutową prezentację:
- Pokaż strukturę infrastruktury
- Zademonstruj działanie alertów
- Zaprezentuj Dashboard
- Wyjaśnij logikę konfiguracji

---

## Dodatkowe wyzwania

### Wyzwanie 1: Niska latencja
Skonfiguruj monitoring z latencją < 10 sekund dla krytycznych metryk

### Wyzwanie 2: Stress test
Użyj narzędzi stress/stress-ng aby wywołać rzeczywiste alerty

### Wyzwanie 3: Custom metrics
Napisz własny skrypt w Pythonie/Bash który zbiera custom metryki

### Wyzwanie 4: Integracja z API
Użyj Zabbix API do automatycznego dodania 10 hostów

### Wyzwanie 5: High Availability
Zaplanuj architekturę Zabbix HA (dokumentacja)

---

## Przydatne komendy podczas ćwiczeń

### Symulacja obciążenia CPU:
```bash
yes > /dev/null &
# Zatrzymanie: killall yes
```

### Symulacja użycia RAM:
```bash
stress --vm 1 --vm-bytes 90% --timeout 60s
```

### Symulacja zapełnienia dysku:
```bash
dd if=/dev/zero of=/tmp/testfile bs=1G count=10
```

### Sprawdzenie połączeń MySQL:
```bash
mysql -e "SHOW STATUS LIKE 'Threads_connected';"
```

### Test SNMP:
```bash
snmpwalk -v2c -c public localhost
```

---

## Checklist po zakończeniu szkoleń

- [ ] Potrafię dodać host do monitoringu
- [ ] Umiem stworzyć własny Item
- [ ] Potrafię skonfigurować Trigger
- [ ] Umiem tworzyć Dashboard
- [ ] Potrafię skonfigurować powiadomienia (min. 2 kanały)
- [ ] Umiem używać SNMP
- [ ] Potrafię monitorować bazę danych
- [ ] Umiem skonfigurować Auto-discovery
- [ ] Rozumiem działanie LLD
- [ ] Potrafię zarządzać użytkownikami
- [ ] Umiem planować maintenance windows
- [ ] Potrafię monitorować logi
- [ ] Umiem używać macro i zmiennych
- [ ] Potrafię stworzyć kompleksowe środowisko monitoringu

---

## Notatki trenera

### Wskazówki prowadzenia:
1. Rozpocznij od ćwiczeń podstawowych - upewnij się że wszyscy nadążają
2. Ćwiczenia 1-3 można wykonać wspólnie jako demo
3. Ćwiczenia 4-11 uczestnicy wykonują samodzielnie z Twoim wsparciem
4. Ćwiczenia 12-16 dla grup zaawansowanych lub jako praca domowa
5. Projekt końcowy idealny na zakończenie 2-dniowego szkolenia

### Typowe problemy:
- Agent nie łączy się z serwerem → sprawdź firewall i selinux
- Brak danych w Items → verify agent configuration
- Triggery nie działają → sprawdź składnię wyrażeń
- Email nie dochodzi → SMTP + gmail app passwords

### Timing dla różnych formatów szkoleń:
- **1 dzień (8h):** Ćwiczenia 1-8
- **2 dni (16h):** Ćwiczenia 1-15 + projekt końcowy
- **3 dni (24h):** Wszystkie ćwiczenia + wyzwania dodatkowe

---

*Powodzenia na szkoleniu!*
