# Skill: Doporučení jídelníčku

## Účel
Agent přečte záznam dnešních jídel a navrhne zbývající jídla na dosažení denního cíle 2200 kcal.

## Jak funguje

1. **Agent čte záznam dne**: Čte soubor `data/[YYYY-MM-DD].txt`
2. **Sčítá kalorií**: Přičítá všechny odhady z dnešního dne
3. **Vypočítá zbytek**: 2200 - suma = zbývající kapacita
4. **Navrhne jídla**: Konkrétní jídla a časy pro zbývající část dne
5. **Sdělí návrh**: Detailní návrh s časovým plánováním

## Instrukce pro agenta

### Krok 1: Čtení záznamu dne
1. Zjisti dnešní datum ve formátu YYYY-MM-DD
2. Čti soubor `data/[YYYY-MM-DD].txt`
3. Pokud soubor neexistuje, informuj uživatele: "Zatím nemáš žádný záznam z dnešku. Můžeme začít od nuly?"
4. Pokud soubor existuje, přečti jej a analyzuj záznamy

### Krok 2: Analýza záznamů
Rozeber každý řádek:
```
[14:30] Jídlo: jablko | Odhad kalorií: 95 | Celkem dnes: 95 kcal
```

Vytáhni:
- Čas: 14:30
- Jídlo: jablko
- Kalorií: 95 kcal

Sečti všechny kalorií z posledního řádku (poslední číslo v "Celkem dnes: X kcal").

### Krok 3: Výpočet zbytku
```
Zbývající kalorií = 2200 - suma z dnešního dne
```

Pokud je výsledek:
- **Nad 2000 kcal**: Bude potřeba velik kompletní jídelníček (snídaně, oběd, večeře)
- **800-2000 kcal**: Bude potřeba jeden větší jídel nebo dva menší
- **300-800 kcal**: Bude potřeba jen jedné či dvě menší jídla (svačina, malá večeře)
- **Pod 300 kcal**: Jen malá svačina (ovoce, nápoj, atd.)
- **Záporná čísla**: Uživatel překročil denní limit (stále mu nabídni návrhy, ale upozorni)

### Krok 4: Návrh jídelníčku

#### Doporučené rozdělení kalorií:
- **Snídaně**: 400 kcal
- **Oběd**: 600 kcal
- **Svačina**: 200 kcal
- **Večeře**: 500 kcal
- **Buffer** (čaj, nápoje, ostatní): 500 kcal

#### Postup návrhu:

1. **Zjisti aktuální čas**
2. **Na základě zbývajících kalorií a času, navrhni zbývající jídla**

**Příklady nástrojů**:

Pokud je ráno (před 12:00) a zbývá >1800 kcal:
```
Doporučuji celý den: snídaně (400), oběd (600), svačina (200), večeře (500)
```

Pokud je odpoledne (12:00-17:00) a zbývá 800-1200 kcal:
```
Doporučuji zbytek: oběd (600), večeře (500) - nebo vybrani mezi nimi
```

Pokud je večer (po 17:00) a zbývá 300-500 kcal:
```
Doporučuji jen lehkou večeři a/nebo svačinu
```

### Krok 5: Konkrétní návrhy jídel

Používej tyto kategorie a příklady:

#### Snídaně (300-500 kcal):
- Pane se máslem a marmeládou (2 krajíce) + sklenice mléka (400 kcal)
- Vejce se chlébem a sádlem (350 kcal)
- Müsli s jogurtem a ovocem (380 kcal)
- Chlebová kaše se máslem (320 kcal)

#### Oběd (500-700 kcal):
- Kuřecí prso s bramborem a zeleninou (600 kcal)
- Vepřový guláš s knedlíkem (650 kcal)
- Losos s těstovinami a salátem (620 kcal)
- Hovězí goulash s chlebem (600 kcal)

#### Svačina (150-250 kcal):
- Jablko + kousek sýra (150 kcal)
- Jogurt s ovocem (180 kcal)
- Chlebník se sádlem (200 kcal)
- Banán + mandle (220 kcal)
- Sklenice mléka s chlebem (200 kcal)

#### Večeře (400-600 kcal):
- Omlet se zeleninou a sýrem (450 kcal)
- Kuřecí pečeně se smaženými brambory (550 kcal)
- Ryba na másle s bramborami (520 kcal)
- Hovězí pečeně s chlebem a máslem (480 kcal)
- Polévka + chlebník (400 kcal)

#### Lehké svačiny a nápoje (50-150 kcal):
- Čaj s cukrem (50 kcal)
- Ovoce (50-100 kcal)
- Sklenice mléka (130 kcal)
- Chleba + máslo (100 kcal)
- Jogurt (80 kcal)

### Krok 6: Sdělení návrhu

Strukturu návrhu:

```
ANALÝZA DNEŠNÍHO STAVU:
- Dosavadní příjem: X kcal
- Denní cíl: 2200 kcal
- Zbývající kapacita: Y kcal

DOPORUČENÝ JÍDELNÍČEK:
- [HH:MM] Svačina: [jídlo] (X kcal)
- [HH:MM] Večeře: [jídlo] (Y kcal)
- [HH:MM] Další: [jídlo] (Z kcal)

SHRNUTÍ:
- Celkem s návrhem: 2150 kcal
- Máš rozpočet na: 50 kcal (např. čaj s cukrem)
```

## Edgecase: Bez záznamů z dnešku

Pokud soubor neexistuje:
```
Zatím nemáš žádný záznam z dnešku. Můžeme ti doporučit celodenní jídelníček?

Doporučuji:
- Snídaně (8:00): Chlebba se máslem a marmeládou + mléko (400 kcal)
- Oběd (12:00): Kuřecí prso s rýží (600 kcal)
- Svačina (15:00): Jablko + jogurt (180 kcal)
- Večeře (18:30): Losos s brambory (500 kcal)
- Buffer (čaj, nápoje): 520 kcal

Celkem: 2200 kcal
```

## Edgecase: Překročení denního cíle

Pokud je suma > 2200 kcal:
```
Pozor! Již jsi překročil/a denní limit o X kcal.
Dosavadní příjem: 2350 kcal
Denní cíl: 2200 kcal
Přebytek: +150 kcal

Můžu ti doporučit jen velmi lehkou svačinu, nebo se můžeš pokusit
o lehkou pohybovou aktivitu na vykompenzování.
```

Stále mu nabídni lehké svačiny (čaj, nápoj bez cukru, atd.).

## Edgecase: Chybí data

Pokud soubor nelze přečíst:
```
Nedaří se mi přečíst záznam z dnešku. Mohu ti doporučit celodenní plán od nuly,
nebo můžeš za chvíli zkusit znovu.
```

## Příklady úplné interakce

### Příklad 1: Ráno bez záznamů
```
Uživatel: Navrhni mi jídelníček na dnešek
Agent: Zatím nemáš žádný záznam z dnešku. Doporučuji celodenní plán:

DOPORUČENÝ JÍDELNÍČEK:
- 07:00 Snídaně: Chlebba se máslem, marmeládou + sklenice mléka (400 kcal)
- 12:00 Oběd: Kuřecí prso s bramborem a zeleninou (600 kcal)
- 15:00 Svačina: Jablko + kusy sýra (150 kcal)
- 18:30 Večeře: Losos se smaženými brambory (500 kcal)
- Ostatní (čaj, nápoje, ovoce): 550 kcal

CELKEM: 2200 kcal
```

### Příklad 2: Odpoledne s již snězeným
```
Uživatel: Co ještě můžu jíst?
Agent: Čtu tvůj záznam z dnešku:

DOSAVADNÍ PŘÍJEM:
- 07:30 Snídaně: chléb se sádlem (300 kcal)
- 12:15 Oběd: kuřecí prso s rýží (700 kcal)
- Celkem dosavadního: 1000 kcal

ZBÝVAJÍCÍ KAPACITA: 1200 kcal

DOPORUČUJI:
- 15:00 Svačina: Jogurt s ovocem + chlebník (220 kcal)
- 18:30 Večeře: Omlet se zeleninou a sýrem (450 kcal)
- Ostatní (čaj, nápoje): 530 kcal

CELKEM S NÁVRHEM: 2200 kcal
```

## Poznámky pro agenta
- Vždy buď vlídný a motivující
- Pokud uživatel překročil limit, nevím mu nadávat - jen mu nabídni lehké options
- Řekni konkrétní doby jídel (ne jen "něco jít")
- Pokud uživatel požaduje "lehčí" nebo "těžší" varianty, adaptuj návrhy
