# Testovací scénář 4 – Chybové stavy

## Cíl

Ověřit, zda aplikace Conquer.io správně zachází s chybovými vstupy, nedostupnými zdroji a neočekávanými situacemi, a poskytuje uživateli srozumitelnou zpětnou vazbu.

---

## TC-01: Přihlášení s nesprávným heslem zobrazí chybovou zprávu

**Priorita:** Vysoká  
**Typ:** E2E (Playwright)  

**Předpoklady:** Backend je spuštěn.

**Kroky:**
1. Navigovat na `/`.
2. Vyplnit uživatelské jméno, které neexistuje (`definitlynotauser99999`).
3. Vyplnit libovolné heslo (`wrongpass1`).
4. Kliknout na „play".
5. Čekat maximálně 10 sekund.

**Očekávaný výsledek:**  
Zobrazí se chybová zpráva obsahující text „wrong username or password" (bez ohledu na velikost písmen). Uživatel zůstane na přihlašovací obrazovce.

---

## TC-02: Registrace s příliš krátkým heslem zobrazí validační chybu

**Priorita:** Vysoká  
**Typ:** E2E (Playwright)  

**Předpoklady:** Uživatel je na registračním formuláři.

**Kroky:**
1. Vyplnit uživatelské jméno a e-mail.
2. Zadat heslo `ab1` (méně než 6 znaků).
3. Kliknout na „register".

**Očekávaný výsledek:**  
Zobrazí se zpráva „Password must be at least 6 characters". Účet není vytvořen.

---

## TC-03: Registrace s heslem bez číslovky nebo malého písmene zobrazí příslušnou validaci

**Priorita:** Střední  
**Typ:** E2E (Playwright)  

**Předpoklady:** Uživatel je na registračním formuláři.

**Kroky – varianta A (chybějící číslovka):**
1. Zadat heslo `abcdef`.
2. Kliknout na „register".
3. Ověřit zprávu „Password must contain at least one digit".

**Kroky – varianta B (chybějící malé písmeno):**
1. Zadat heslo `ABCDEF1`.
2. Kliknout na „register".
3. Ověřit zprávu „Password must contain at least one lowercase letter".

**Očekávaný výsledek:**  
Každá validační chyba zobrazí specifickou, srozumitelnou zprávu popisující konkrétní požadavek na heslo.

---

## TC-04: Vytvoření soukromé místnosti bez join kódu zobrazí chybu

**Priorita:** Střední  
**Typ:** E2E (Playwright)  

**Předpoklady:** Přihlášený uživatel je v prohlížeči místností.

**Kroky:**
1. Kliknout na „create room".
2. Přepnout typ místnosti na „private".
3. Newyplňovat pole join kódu.
4. Kliknout na „create room" (potvrzovací tlačítko v modálu).

**Očekávaný výsledek:**  
Zobrazí se zpráva „join code required for private rooms". Místnost není vytvořena.

---

## TC-05: API vrátí `404` pro neexistující statistiky hráče

**Priorita:** Střední  
**Typ:** API  

**Předpoklady:** Backend je spuštěn.

**Kroky:**
1. Odeslat `GET /api/stats/neexistujici-id-123`.
2. Zkontrolovat stavový kód a tělo odpovědi.

**Očekávaný výsledek:**  
Server vrátí `404 Not Found` se zprávou `"No stats found for player 'neexistujici-id-123'."`. Aplikace nespadne ani nevrátí `500 Internal Server Error`.

---

## Poznámky

- TC-01 až TC-04 jsou plně automatizovatelné pomocí Playwright (viz existující `auth.spec.ts`, `rooms.spec.ts`).
- TC-05 lze ověřit přes `conquerio.http` nebo curl: `curl http://localhost:8080/api/stats/neexistujici-id-123`.
- Při testování chybových stavů je důležité ověřit nejen zobrazení zprávy, ale i to, že aplikace zůstane ve stabilním stavu (žádný pád, žádná prázdná obrazovka).
