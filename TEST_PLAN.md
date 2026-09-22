# Testovací plán - Fit Poradce

## Přehled testů

Testy ověřují obě skills (`odhad-kalorii` a `doporuceni-kalorii`) a jejich integraci.

---

## BLOK 1: Skill "odhad-kalorii" - Základní funkce

### Test 1.1: Jednoduché jídlo
**Cíl**: Ověřit záznam jednoduchého jídla

```
PŘÍKAZ:
/odhad-kalorii
Právě jsem snědl jablko

OČEKÁVANÝ VÝSLEDEK:
✓ Agent odhadne kalorií (~95 kcal)
✓ Řekne: "Celkem dnes: 95 kcal, Zbývá ti: 2105 kcal"
✓ Vytvoří/upraví soubor: data/[DNEŠNÍ-DATUM].txt
✓ Obsah souboru:
  [HH:MM] Jídlo: jablko | Odhad kalorií: 95 | Celkem dnes: 95 kcal
```

---

### Test 1.2: Složitější jídlo s upřesněním
**Cíl**: Ověřit, že agent se ptá na nejasnosti

```
PŘÍKAZ:
/odhad-kalorii
Obědval jsem kuřecí plátek

OČEKÁVANÝ VÝSLEDEK (Agent se ptá):
✓ Agent: "Kolik to bylo? Je to plátek z jednoho prsa?"
✓ Agent: "Bylo to vařené, pečené nebo smažené?"

POKRAČOVÁNÍ:
Uživatel: "Jedno kuřecí prso, pečené"
Agent: "Se zeleninou?"
Uživatel: "Ano, se smaženou rýží. Malá porce."

OČEKÁVANÝ VÝSLEDEK:
✓ Agent odhadne (~500 kcal)
✓ Soubor bude upravený:
  [HH:MM] Jídlo: jablko | Odhad kalorií: 95 | Celkem dnes: 95 kcal
  [HH:MM] Jídlo: kuřecí prso (pečené) se smaženou rýží | Odhad kalorií: 500 | Celkem dnes: 595 kcal
✓ Agent řekne: "Celkem dnes: 595 kcal, Zbývá ti: 1605 kcal"
```

---

### Test 1.3: Více jídel během dne
**Cíl**: Ověřit, že agent správně sčítá kalorií

```
PŘÍKAZ SEKVENCE:

1) /odhad-kalorii
   "Snídal jsem chléb se máslem a marmeládou"
   → Odhad: 300 kcal, Celkem: 300 kcal ✓

2) /odhad-kalorii
   "Pil jsem sklenici mléka"
   → Odhad: 130 kcal, Celkem: 430 kcal ✓

3) /odhad-kalorii
   "Právě jsem snědl tyčinku müsli"
   → Odhad: 150 kcal, Celkem: 580 kcal ✓

OČEKÁVANÝ VÝSLEDEK:
✓ Všechna jídla v jednom souboru
✓ Součty v "Celkem dnes" stoupají správně
✓ Poslední záznam: 580 kcal
```

---

### Test 1.4: Překročení denního limitu
**Cíl**: Ověřit varování při překročení 2200 kcal

```
PŘÍKAZ:
/odhad-kalorii
Právě jsem snědl velký kus dortu

STAV PŘED:
Celkem dosavadního: 2100 kcal

OČEKÁVANÝ VÝSLEDEK:
✓ Agent řekne: "Pozor! Překročíš denní limit!"
✓ Vypočítá: "2100 + 400 (dort) = 2500 kcal"
✓ Upozorní: "Přebytek: +300 kcal"
✓ Přesto uloží záznam do souboru
✓ Řekne: "Zbývá ti: -300 kcal"
```

---

### Test 1.5: Neurčitý popis
**Cíl**: Ověřit, že agent požaduje upřesnění

```
PŘÍKAZ:
/odhad-kalorii
Snědl jsem oběd

OČEKÁVANÝ VÝSLEDEK:
✓ Agent se ptá: "Co obsahoval tvůj oběd?"
✓ Čeká na konkrétní popis (bez neurčitých slov)
✗ Nepřijme jen "oběd" - je příliš vágní
```

---

## BLOK 2: Skill "doporuceni-kalorii" - Základní funkce

### Test 2.1: Návrh bez záznamů (ráno)
**Cíl**: Ověřit, že agent navrhne celodenní plán

```
PŘÍKAZ:
/doporuceni-kalorii
Navrhni mi jídelníček na dnešek

STAV:
Soubor data/[DNEŠNÍ-DATUM].txt neexistuje

OČEKÁVANÝ VÝSLEDEK:
✓ Agent: "Zatím nemáš žádný záznam z dnešku"
✓ Nabídne celodenní plán:
  - Snídaně (400 kcal) - konkrétní jídlo
  - Oběd (600 kcal) - konkrétní jídlo
  - Svačina (200 kcal) - konkrétní jídlo
  - Večeře (500 kcal) - konkrétní jídlo
  - Buffer (500 kcal) - čaj, nápoje, atd.
✓ Řekne: "Celkem: 2200 kcal"
✓ Všechna jídla mají časy (08:00, 12:00, 15:00, 18:30)
```

---

### Test 2.2: Návrh s částečným záznamem (v průběhu dne)
**Cíl**: Ověřit návrh zbývajících jídel

```
STAV:
data/[DNEŠNÍ-DATUM].txt obsahuje:
[08:00] Jídlo: chléb se sádlem | Odhad kalorií: 300 | Celkem dnes: 300 kcal
[11:00] Jídlo: jablko | Odhad kalorií: 95 | Celkem dnes: 395 kcal

PŘÍKAZ:
/doporuceni-kalorii
Co ještě bych měl jíst?

OČEKÁVANÝ VÝSLEDEK:
✓ Agent: "Čtu tvůj záznam z dnešku"
✓ Vypočítá: "Dosavadní: 395 kcal, Zbývá: 1805 kcal"
✓ Navrhne zbývající jídla (oběd, svačina, večeře)
✓ Součet navrhnutých = 1805 kcal (nebo max ±100 kcal)
✓ Všechna jídla mají časy
✓ NE opakovanie snídaně (již byla)
```

---

### Test 2.3: Návrh těsně před večeří
**Cíl**: Ověřit adaptaci návrhu na aktuální čas

```
STAV:
[08:00] Snídaně: 400 kcal → Celkem: 400
[12:30] Oběd: 600 kcal → Celkem: 1000
[15:00] Svačina: 180 kcal → Celkem: 1180

AKTUÁLNÍ ČAS: 18:00

PŘÍKAZ:
/doporuceni-kalorii
Navrhni mi večeři

OČEKÁVANÝ VÝSLEDEK:
✓ Agent: "Zbývá ti: 1020 kcal"
✓ Navrhne jednu VEČEŘI (ne svačiny nebo ostatní)
✓ Velikost: 500-600 kcal
✓ Čas: 18:30 nebo později
✓ NE vícero jídel (je pozdě)
```

---

### Test 2.4: Návrh při překročení limitu
**Cíl**: Ověřit chování po překročení

```
STAV:
data/[DNEŠNÍ-DATUM].txt:
Dosavadní součet: 2350 kcal (překročeno o 150)

PŘÍKAZ:
/doporuceni-kalorii
Co ještě mohu jíst?

OČEKÁVANÝ VÝSLEDEK:
✓ Agent: "Pozor! Již jsi překročil limit o 150 kcal"
✓ Nabídne LEHKÉ SVAČINY:
  - Čaj bez cukru (0 kcal)
  - Voda
  - Černá káva
✓ Řekne: "Můžeš si dát jen něco bez kalorií"
✓ Být vlídný, NE kritický
```

---

### Test 2.5: Čtení chybného souboru
**Cíl**: Ověřit error handling

```
STAV:
Soubor data/[DNEŠNÍ-DATUM].txt je poškozený nebo nečitelný

PŘÍKAZ:
/doporuceni-kalorii
Navrhni mi jídelníček

OČEKÁVANÝ VÝSLEDEK:
✓ Agent: "Nedaří se mi přečíst záznam z dnešku"
✓ Nabídne fallback: "Začínejme od nuly" nebo "Zkus to znovu"
✗ NE crash, NE nepříjemná chyba
```

---

## BLOK 3: Integrační testy

### Test 3.1: Kontinuita během dne
**Cíl**: Ověřit, že oba skills pracují spolu

```
SEKVENCE:

1) Ráno - NÁVRH
   /doporuceni-kalorii "Navrhni mi celodenní plán"
   → Agent navrhne všechna jídla

2) Snídaně
   /odhad-kalorii "Snědl jsem chlebba se máslem a mléko"
   → Agent záznamuje: 400 kcal

3) Půldenní UPDATE
   /doporuceni-kalorii "Jak je na tom se zbytkem?"
   → Agent zápis oběd + svačinu + večeři (1800 kcal)

4) Oběd
   /odhad-kalorii "Obědval jsem kuřecí prso s rýží"
   → Agent: 600 kcal, Celkem: 1000

5) Večer - FINÁLNÍ NÁVRH
   /doporuceni-kalorii "Navrhni mi večeři"
   → Agent: "Zbývá ti 1200 kcal na večeři + buffer"

6) Večeře
   /odhad-kalorii "Snědl jsem omlet se sýrem a chlébem"
   → Agent: 500 kcal, Celkem: 1500

7) Noc - ZBÝVAJÍCÍ
   /doporuceni-kalorii "Mohu si vzít něco na noc?"
   → Agent: "Zbývá ti 700 kcal - lehka svačina, čaj"

OČEKÁVANÝ VÝSLEDEK:
✓ Všechny záznamy v jednom souboru dat/[DNEŠNÍ].txt
✓ Kontinuální sčítání
✓ Logické návrhy v každý čas
✓ Součty se shodují
```

---

### Test 3.2: Více dní (trvalé uchovávání dat)
**Cíl**: Ověřit, že data zůstávají oddělená po dnech

```
DEN 1 (25.9.2026):
/odhad-kalorii "Jablko, chléb"
→ Vytvoří: data/2026-09-25.txt

DEN 2 (26.9.2026):
/odhad-kalorii "Mléko"
→ Vytvoří: data/2026-09-26.txt (NE v původním souboru!)

OVĚŘENÍ:
✓ data/2026-09-25.txt obsahuje pouze záznamy ze 25.9
✓ data/2026-09-26.txt obsahuje pouze záznamy z 26.9
✓ Oba soubory existují
✓ Agent se v den 26.9 nezeptá na záznamy z 25.9
```

---

### Test 3.3: Formát datových souborů
**Cíl**: Ověřit správný formát zápisu

```
KONTROLA SOUBORU: data/2026-09-22.txt

OČEKÁVANÝ FORMÁT:
[14:30] Jídlo: jablko | Odhad kalorií: 95 | Celkem dnes: 95 kcal
[15:20] Jídlo: kuřecí prso se rýží | Odhad kalorií: 500 | Celkem dnes: 595 kcal
[16:45] Jídlo: jogurt | Odhad kalorií: 150 | Celkem dnes: 745 kcal

KONTROLY:
✓ Každý řádek má formát: [HH:MM] Jídlo: ... | Odhad kalorií: ... | Celkem dnes: ... kcal
✓ Časy jsou reálné (HH:MM)
✓ Kalorií jsou čísla (přirozená čísla)
✓ "Celkem dnes" se zvyšuje
✓ UTF-8 kódování (č, ř, š, etc.)
```

---

## BLOK 4: Edge cases a výjimky

### Test 4.1: Nulová kalorie (voda, čaj bez cukru)
```
PŘÍKAZ:
/odhad-kalorii
Pil jsem vodu a čaj bez cukru

OČEKÁVANÝ VÝSLEDEK:
✓ Agent: "Voda a čaj bez cukru = 0 kcal"
✓ Uloží záznam: "0 | Celkem dnes: [předchozí součet]"
✓ Nechybuje se ani nula
```

### Test 4.2: Muito vysoká kalorie (dort, sladkosti)
```
PŘÍKAZ:
/odhad-kalorii
Snědl jsem část dortu

AGENT PTÁNÍ:
"Jak velký kus? (malý ~150 kcal, střední ~400 kcal, velký ~600+ kcal)"

OČEKÁVANÝ VÝSLEDEK:
✓ Agent správně upraví estimate na základě odpovědi
```

### Test 4.3: Neurčitá jednotka (lžíce, hrst, kousky)
```
PŘÍKAZ:
/odhad-kalorii
Snědl jsem hrst ořechů

OČEKÁVANÝ VÝSLEDEK:
✓ Agent se zeptá: "Kolik ořechů? (asi 30g = 180 kcal, nebo více?)"
✓ Čeká na upřesnění
```

### Test 4.4: Jídlo bez odhadu (neznámé jídlo)
```
PŘÍKAZ:
/odhad-kalorii
Snědl jsem X (výmyšlené jídlo)

OČEKÁVANÝ VÝSLEDEK:
✓ Agent: "Neznám toto jídlo, mohu zkusit odhadnout?"
✓ Zeptá se na složení (maso, sýr, chléb, atd.)
✓ Vytvoří kompozitní odhad
```

---

## BLOK 5: Uživatelský komfort

### Test 5.1: Přirozený jazyk (opravy)
```
PŘÍKAZY (různé variace):
- "Snědl jsem jablko"
- "Právě jsem snědl jablko"
- "Jablko"
- "Měl jsem jablko"

OČEKÁVANÝ VÝSLEDEK:
✓ Agent chápá všechny varianty
✓ Všechny se zaznamenají správně
```

### Test 5.2: Gramatika a čísla
```
PŘÍKAZY:
- "2 jablka" → 2 × 95 = 190 kcal
- "tři brambory" → 3 × 20 = 60 kcal
- "půl porce kuřete" → 150 kcal

OČEKÁVANÝ VÝSLEDEK:
✓ Agent správně parsuje čísla
✓ Násobí správně
```

### Test 5.3: Motivující tón agenta
```
PŘI PŘEKROČENÍ LIMITU:
✗ NE: "Ty tlustý, překročils limit!"
✓ ANO: "Neboj, všem se to stává. Zítřívám si dáš pozor!"

OČEKÁVANÝ VÝSLEDEK:
✓ Agent je vždy vlídný a podporující
✓ NE sarkastický nebo kritický
```

---

## BLOK 6: Příkazové speciály

### Test 6.1: Jak se vyzývá skills
```
VARIANTY:
- /odhad-kalorii Snědl jsem jablko
- /odhad-kalorii
  Snědl jsem jablko
- Snědl jsem jablko (bez slashe)
- Vyzvi skill odhad-kalorii: Snědl jsem jablko

OČEKÁVANÝ VÝSLEDEK:
✓ Alespoň jedna/dvě varianty fungují
```

---

## Shrnutí testů

| Blok | Počet testů | Priorita |
|------|-------------|----------|
| **1. Odhad kalorií** | 5 | 🔴 Kritické |
| **2. Doporučení** | 5 | 🔴 Kritické |
| **3. Integrace** | 3 | 🟠 Vysoká |
| **4. Edge cases** | 4 | 🟡 Střední |
| **5. UX** | 3 | 🟡 Střední |
| **6. Speciály** | 1 | 🟢 Nízká |
| **CELKEM** | **21** | |

---

## Jak spustit testy

1. **Manuální testy** (jednoduché, pro rychlou validaci)
   - Spusť jednotlivé testy z tohoto dokumentu
   - Ověř výstupy agenta

2. **Automatizované testy** (pokud budou k dispozici)
   - Prepare testovací datové soubory
   - Simuluj uživatelské vstupy
   - Kontroluj výstupy

3. **Testování v čase**
   - Spouštěj testy v různých časy (ráno, v poledne, večer)
   - Ověř, že návrhy jídel jsou kontextové

---

## Poznámky

- ✓ = Očekávaný výsledek
- ✗ = Chybný výsledek
- Každý test by měl trvat cca 2-5 minut
- Při selhání testu zaznamenat: Co se pokazilo a proč

Vždycky si pamatuj: **Cíl je ověřit, že aplikace funguje intuitivně a pomáhá uživateli sledovat svůj kalorický příjem.**
