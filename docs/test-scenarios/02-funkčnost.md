# Testovací scénář 2 – Funkčnost

## Cíl

Ověřit, zda klíčové herní funkce a backendové API pracují správně za standardních podmínek.

---

## TC-01: Registrace a automatické přihlášení

**Priorita:** Vysoká  
**Typ:** E2E (Playwright)  

**Předpoklady:** Backend a databáze jsou spuštěny.

**Kroky:**
1. Navigovat na `/`.
2. Přepnout na registrační formulář.
3. Vyplnit unikátní uživatelské jméno, e-mail a heslo splňující požadavky (`testpass1`).
4. Kliknout na „register".
5. Počkat na zobrazení prohlížeče místností.

**Očekávaný výsledek:**  
Po úspěšné registraci je uživatel automaticky přihlášen a přesměrován na obrazovku prohlížeče místností s tlačítkem „quick play".

---

## TC-02: Quick Play spustí hru s herním plátnem

**Priorita:** Vysoká  
**Typ:** E2E (Playwright)  

**Předpoklady:** Uživatel je přihlášen v prohlížeči místností.

**Kroky:**
1. Kliknout na tlačítko „quick play".
2. Počkat maximálně 15 sekund.

**Očekávaný výsledek:**  
Na stránce se zobrazí element `<canvas>` – hráč je úspěšně připojen do herní místnosti.

---

## TC-03: Vytvoření soukromé místnosti a připojení pomocí kódu

**Priorita:** Vysoká  
**Typ:** E2E / API  

**Předpoklady:** Dva přihlášení uživatelé (lze simulovat dvěma prohlížečovými kontexty).

**Kroky:**
1. Uživatel A: kliknout na „create room", přepnout na „private", zadat join kód `ABC123`, potvrdit.
2. Ověřit, že je místnost vytvořena a A je do ní připojen.
3. Uživatel B: v prohlížeči místností zadat join kód `ABC123` a připojit se.
4. Ověřit, že oba uživatelé vidí sebe navzájem na herním plátně.

**Očekávaný výsledek:**  
Soukromá místnost je dostupná pouze s platným join kódem. Oba hráči jsou ve stejné místnosti.

---

## TC-04: Leaderboard a statistiky hráče

**Priorita:** Střední  
**Typ:** API  

**Předpoklady:** V databázi existuje alespoň jeden hráč se záznamy statistik.

**Kroky:**
1. Odeslat `GET /api/leaderboard`.
2. Ověřit, že odpověď obsahuje pole `Rank`, `Username`, `Elo`, `BestTerritoryPct`.
3. Odeslat `GET /api/stats/{userId}` pro existujícího hráče.
4. Ověřit, že odpověď obsahuje `TotalKills`, `TotalDeaths`, `TotalGames`.

**Očekávaný výsledek:**  
Oba endpointy vrátí stavový kód `200 OK` s očekávanou strukturou dat.

---

## TC-05: Odpojení ze hry vrátí hráče do prohlížeče místností

**Priorita:** Střední  
**Typ:** E2E (Playwright)  

**Předpoklady:** Hráč je aktivně ve hře (canvas je viditelný).

**Kroky:**
1. Stisknout klávesu `Escape`.
2. Ověřit zobrazení menu s nadpisem „menu".
3. Kliknout na tlačítko „disconnect".
4. Počkat na zobrazení tlačítka „quick play".

**Očekávaný výsledek:**  
Hráč je odpojen ze hry a přesměrován zpět na prohlížeč místností bez nutnosti obnovit stránku.

---

## Poznámky

- TC-01 a TC-02 jsou pokryty existujícími Playwright testy (`auth.spec.ts`, `game.spec.ts`).
- TC-04 lze ověřit přes `conquerio.http` soubor nebo nástrojem jako Postman/curl.
- Pro TC-03 je vhodné použít dva paralelní prohlížečové kontexty Playwright.
