# Testovací scénář 1 – Uživatelská přívětivost a srozumitelnost

## Cíl

Ověřit, zda je uživatelské rozhraní aplikace Conquer.io intuitivní, přehledné a srozumitelné pro nového i vracejícího se hráče.

---

## TC-01: Přihlašovací obrazovka je okamžitě srozumitelná

**Priorita:** Vysoká  
**Typ:** Manuální / E2E  

**Předpoklady:** Uživatel otevře aplikaci poprvé.

**Kroky:**
1. Navigovat na hlavní URL aplikace (`http://localhost:5173`).
2. Zkontrolovat viditelnost nadpisu „conquerio".
3. Zkontrolovat přítomnost polí pro uživatelské jméno a heslo.
4. Zkontrolovat přítomnost tlačítka „play".
5. Zkontrolovat přítomnost odkazu „no account? register".

**Očekávaný výsledek:**  
Všechny prvky jsou viditelné bez nutnosti posouvat stránku. Hráč okamžitě ví, jak se přihlásit nebo zaregistrovat.

---

## TC-02: Přepínání mezi přihlášením a registrací je jasné

**Priorita:** Vysoká  
**Typ:** Manuální / E2E  

**Předpoklady:** Uživatel je na přihlašovací obrazovce.

**Kroky:**
1. Kliknout na text „no account? register".
2. Ověřit, že se zobrazí pole pro e-mail a tlačítko „register".
3. Kliknout na text „have an account? login".
4. Ověřit, že pole pro e-mail zmizí a zobrazí se tlačítko „play".

**Očekávaný výsledek:**  
Přepínání mezi formuláři je plynulé a uživatel nikdy není v pochybnostech, ve které části toku se nachází.

---

## TC-03: Prohlížeč místností je přehledný po přihlášení

**Priorita:** Vysoká  
**Typ:** Manuální / E2E  

**Předpoklady:** Uživatel je přihlášen.

**Kroky:**
1. Ověřit přítomnost tlačítek: „quick play", „create room", „my profile", „settings", „logout".
2. Ověřit, že seznam dostupných místností zobrazuje jméno místnosti, počet hráčů a velikost hřiště.
3. Ověřit, že soukromé místnosti jsou vizuálně odlišeny (ikonou zámku nebo štítkem).

**Očekávaný výsledek:**  
Hráč na první pohled vidí, co může dělat, a snadno rozliší veřejné a soukromé místnosti.

---

## TC-04: Herní HUD je čitelný během hry

**Priorita:** Střední  
**Typ:** Manuální  

**Předpoklady:** Hráč je připojen do herní místnosti.

**Kroky:**
1. Zkontrolovat, zda je minimap zobrazen v rohu obrazovky a nezakrývá herní plochu.
2. Zkontrolovat, zda leaderboard zobrazuje jméno hráče (ne UUID).
3. Na mobilním zařízení ověřit, že virtuální joystick je umístěn v levém dolním rohu (ne uprostřed obrazovky).
4. Na mobilním zařízení ověřit, že jsou k dispozici tlačítka schopností s ikonami (ne jen text „BOOST"/"SHIELD").

**Očekávaný výsledek:**  
HUD nepřekáží výhledu, leaderboard ukazuje čitelné přezdívky a mobilní ovládací prvky jsou přístupné.

---

## TC-05: Obrazovka smrti jasně informuje hráče

**Priorita:** Střední  
**Typ:** Manuální / E2E  

**Předpoklady:** Hráč byl zabit jiným hráčem.

**Kroky:**
1. Nechat hráče zahynout kolizí se stopou soupeře.
2. Ověřit, že se zobrazí obrazovka smrti s přezdívkou zabijáka (ne jeho UUID).
3. Ověřit, že je k dispozici tlačítko pro respawn nebo návrat do prohlížeče místností.

**Očekávaný výsledek:**  
Hráč ihned ví, kdo ho zabil, a může se rychle vrátit do hry.

---

## Poznámky

- Testy TC-01 až TC-03 jsou plně automatizovatelné pomocí Playwright.
- TC-04 a TC-05 vyžadují manuální ověření na fyzickém mobilním zařízení (iOS Safari, Android Chrome).
