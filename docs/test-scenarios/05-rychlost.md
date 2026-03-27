# Testovací scénář 5 – Rychlost

## Cíl

Ověřit, zda aplikace Conquer.io dosahuje přijatelných dob odezvy a dokáže zvládnout zátěž více souběžných hráčů bez degradace výkonu.

---

## TC-01: REST API odpovídá do 200 ms za normálního provozu

**Priorita:** Vysoká  
**Typ:** Výkonnostní / API  

**Předpoklady:** Backend je spuštěn; databáze obsahuje alespoň 10 záznamů.

**Kroky:**
1. Odeslat `GET /api/leaderboard` a změřit dobu odezvy.
2. Odeslat `GET /api/rooms` (s platným JWT) a změřit dobu odezvy.
3. Odeslat `GET /api/stats/{userId}` pro existujícího hráče a změřit dobu odezvy.
4. Opakovat každý požadavek 10×.

**Očekávaný výsledek:**  
Průměrná doba odezvy každého endpointu nepřekračuje **200 ms**. Žádný požadavek nepřesáhne **500 ms**.

---

## TC-02: Přihlášení a zobrazení prohlížeče místností do 3 sekund

**Priorita:** Vysoká  
**Typ:** E2E (Playwright)  

**Předpoklady:** Backend je spuštěn.

**Kroky:**
1. Zaznamenat čas před kliknutím na tlačítko „play".
2. Kliknout na „play" s platnými přihlašovacími údaji.
3. Čekat na zobrazení tlačítka „quick play".
4. Zaznamenat uplynulý čas.

**Očekávaný výsledek:**  
Prohlížeč místností se zobrazí do **3 sekund** od odeslání přihlašovacího formuláře.

---

## TC-03: WebSocket spojení je navázáno do 2 sekund

**Priorita:** Vysoká  
**Typ:** E2E (Playwright) / Integrační  

**Předpoklady:** Přihlášený uživatel je v prohlížeči místností.

**Kroky:**
1. Zaznamenat čas před kliknutím na „quick play".
2. Kliknout na „quick play".
3. Čekat na zobrazení elementu `<canvas>`.
4. Zaznamenat uplynulý čas.

**Očekávaný výsledek:**  
Herní plátno (`<canvas>`) je viditelné do **2 sekund** od kliknutí. Server přidá hráče do herní místnosti během jednoho až dvou herních ticků (50–100 ms při 20 Hz).

---

## TC-04: Herní server udržuje stabilní tick rate 20 Hz pod zátěží 10 hráčů

**Priorita:** Vysoká  
**Typ:** Zátěžový  

**Předpoklady:** Lokální prostředí nebo staging server; nástroj pro simulaci více WebSocket klientů (k6, vlastní skript).

**Kroky:**
1. Spustit 10 simultánních WebSocket klientů připojených do stejné místnosti.
2. Každý klient odesílá direction input každých 50 ms.
3. Sledovat serverové logy (`serilog`) a měřit skutečný interval mezi tick broadcasty po dobu 60 sekund.

**Očekávaný výsledek:**  
Průměrný interval mezi ticky je **50 ms ± 5 ms** (20 Hz ± 10 %). CPU využití serveru nepřesáhne 80 %. Žádný klient neobdrží více než 3 po sobě jdoucí ticky bez aktualizace stavu.

---

## TC-05: Zátěžový test – 50 souběžných uživatelů (login + quick play)

**Priorita:** Střední  
**Typ:** Zátěžový (k6 / Locust)  

**Předpoklady:** Staging nebo produkční server; k6 nebo ekvivalentní nástroj. (Viz `docs/load-test` pro existující scénáře.)

**Kroky:**
1. Spustit zátěžový scénář: 50 virtuálních uživatelů, každý provede registraci → přihlášení → připojení do hry.
2. Ramp-up: 0 → 50 VU za 30 sekund, ustálený stav 60 sekund.
3. Sledovat: dobu odezvy registrace, dobu odezvy přihlášení, čas navázání WebSocket spojení, chybovost.

**Očekávaný výsledek:**  
- Chybovost (HTTP 5xx, WebSocket odmítnutí) < **1 %**.  
- 95. percentil doby odezvy REST endpointů < **500 ms**.  
- Žádný hráč není vyhozen ze hry z důvodu timeoutu serveru.

---

## Poznámky

- TC-01 lze automatizovat pomocí `curl` s `--write-out "%{time_total}"` nebo skriptem v Pythonu/Node.js.
- TC-04 a TC-05 využívají existující zátěžové scénáře v adresáři `docs/load-test`.
- Testy rychlosti spouštějte na stroji s podobnými parametry jako produkční server, aby byly výsledky relevantní.
- Při výkonnostních testech vždy zaznamenávejte verzi kódu (commit SHA) a konfigurace prostředí.
