# Testovací scénář 3 – Bezpečnost

## Cíl

Ověřit, zda aplikace Conquer.io správně chrání uživatelská data, odmítá neoprávněné přístupy a bezpečně zachází s autentizačními tokeny.

---

## TC-01: Chráněné endpointy odmítají požadavky bez JWT

**Priorita:** Kritická  
**Typ:** API  

**Předpoklady:** Backend je spuštěn.

**Kroky:**
1. Odeslat `GET /api/rooms` bez hlavičky `Authorization`.
2. Odeslat `POST /api/rooms` bez hlavičky `Authorization`.
3. Odeslat `GET /api/auth/me` bez hlavičky `Authorization`.

**Očekávaný výsledek:**  
Všechny tři požadavky vrátí stavový kód `401 Unauthorized`. Žádná data nejsou vrácena.

---

## TC-02: WebSocket spojení je odmítnuto s neplatným nebo chybějícím JWT

**Priorita:** Kritická  
**Typ:** Integrační  

**Předpoklady:** Backend je spuštěn.

**Kroky:**
1. Pokusit se navázat WebSocket spojení s neplatným tokenem v query parametru (`?token=invalid`).
2. Pokusit se navázat WebSocket spojení bez tokenu.
3. Pokusit se navázat WebSocket spojení s expirovaným tokenem.

**Očekávaný výsledek:**  
Server odmítne všechna tři spojení s kódem `401` ještě před vstupem do herní smyčky. Žádný hráč není přidán do místnosti.

---

## TC-03: Heslo není uloženo v čitelné podobě

**Priorita:** Kritická  
**Typ:** Databázová inspekce  

**Předpoklady:** Přístup k MySQL databázi (vývojové prostředí).

**Kroky:**
1. Zaregistrovat nového uživatele s heslem `testpass1`.
2. Připojit se k databázi a spustit dotaz: `SELECT PasswordHash FROM AspNetUsers WHERE UserName = '<jméno>';`

**Očekávaný výsledek:**  
Sloupec `PasswordHash` obsahuje BCrypt hash (začíná `$2a$` nebo `$2b$`), nikoli plain-text heslo. ASP.NET Core Identity hash je výsledkem správného použití `UserManager.CreateAsync`.

---

## TC-04: Uživatelské jméno obsahující „@" je odmítnuto

**Priorita:** Střední  
**Typ:** API / E2E  

**Předpoklady:** Backend je spuštěn.

**Kroky:**
1. Odeslat `POST /api/auth/register` s tělem `{ "username": "user@evil", "email": "x@test.com", "password": "testpass1" }`.

**Očekávaný výsledek:**  
Server vrátí `400 Bad Request` se zprávou `"Username cannot contain '@' and cannot be an email address."` Token není vydán.

---

## TC-05: JWT token expiruje po 12 hodinách

**Priorita:** Střední  
**Typ:** API (simulace)  

**Předpoklady:** Backend je spuštěn; možnost manipulace s časem nebo dekódování tokenu.

**Kroky:**
1. Přihlásit se a získat JWT token.
2. Dekódovat token (např. na jwt.io) a ověřit, že pole `exp` je rovno `iat + 43200` (12 hodin v sekundách).
3. (Volitelně v testovacím prostředí) Simulovat čas +13 hodin a odeslat požadavek na chráněný endpoint s tímto tokenem.

**Očekávaný výsledek:**  
Token má nastavenou expiraci přesně 12 hodin od vydání. Po uplynutí expirace je požadavek odmítnut s kódem `401 Unauthorized`.

---

## Poznámky

- TC-01 a TC-02 jsou částečně pokryty existujícími integračními testy v `backend/UnitTests`.
- TC-03 vyžaduje přístup k databázi – provádět pouze v izolovaném vývojovém prostředí.
- TC-05 je vhodné automatizovat pomocí mock systémového času nebo krátkodobé expirace tokenu nastavenou v testovací konfiguraci.
