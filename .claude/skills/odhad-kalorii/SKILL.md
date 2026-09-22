# Skill: Odhad kalorií

## Účel
Agent přijímá popis snězeného jídla v přirozeném jazyce a odhaduje jeho kalorický obsah.

## Jak funguje

1. **Uživatel zadá jídlo**: "Právě jsem snědl/a [popis jídla]"
2. **Agent odhadne kalorií**: Na základě standardní databáze potravin
3. **Agent uloží záznam**: Do souboru `data/[YYYY-MM-DD].txt`
4. **Agent sdělí stav**: Kolik kalorií zbývá na dnešek

## Instrukce pro agenta

### Krok 1: Přijetí a potvrzení
- Přijmi popis jídla v přirozeném jazyce
- Pokud je popis neurčitý (např. "oběd", "malá porce"), zeptej se na upřesnění
- Příklady upřesnění: "Je to velká nebo malá porce?", "Kolik to bylo gramů?", "Bylo to se sádlem?"

### Krok 2: Odhad kalorií
Použij standardní tabulky pro odhad. Příklady:

**Ovoce a zelenina**:
- Jablko: 95 kcal
- Banán: 105 kcal
- Mrkev (1 střední): 25 kcal
- Paprika: 30 kcal

**Mléčné produkty**:
- Mléko (200 ml): 130 kcal
- Jogurt (150g): 80 kcal
- Sýr (30g): 120 kcal

**Chlebové a zrnité produkty**:
- Chléb (1 krajíc): 80 kcal
- Rýže (150g vařená): 150 kcal
- Těstoviny (150g vařené): 150 kcal
- Müsli (50g): 180 kcal

**Maso a ryby**:
- Kuřecí prso (100g): 165 kcal
- Vepřové maso (100g): 270 kcal
- Losos (100g): 200 kcal
- Vajce (1 velké): 155 kcal

**Ostatní**:
- Máslo (1 lžíce): 100 kcal
- Sádlo (1 lžíce): 115 kcal
- Olej (1 lžíce): 120 kcal
- Čokoláda (1 čtverec): 50 kcal
- Chleba se sádlem (2 kusy): 300 kcal

### Krok 3: Čtení aktuálního stavu
1. Zkontroluj, zda existuje soubor `data/[YYYY-MM-DD].txt` (kde [YYYY-MM-DD] je dnešní datum)
2. Pokud soubor existuje, přečti jej a sečti všechny kalorií
3. Pokud soubor neexistuje, součet je 0

### Krok 4: Uložení záznamu
Přidej nový řádek do souboru `data/[YYYY-MM-DD].txt` v append módu:

```
[HH:MM] Jídlo: <popis jídla> | Odhad kalorií: <kcal> | Celkem dnes: <suma> kcal
```

Příklad:
```
[14:30] Jídlo: jablko | Odhad kalorií: 95 | Celkem dnes: 95 kcal
[15:45] Jídlo: kuřecí prso se smaženou rýží | Odhad kalorií: 500 | Celkem dnes: 595 kcal
```

### Krok 5: Sdělení stavu
Poté, co záznamu uložíš, řekni uživateli:
- Kolik kalorií měl odhad
- Celkový součet kalorií za dnešek
- Kolik kalorií zbývá (2200 - suma)

**Příklad zprávy**:
```
Zaznamenáno: kuřecí plátek se smaženou rýží (750 kcal)
Celkem dnes: 1350 kcal
Zbývá ti: 850 kcal na dnešek
```

## Edgecase: Překročení denního cíle
Pokud se součet přiblíží nebo překročí 2200 kcal, informuj uživatele:
```
Pozor! Tímto jídlem překročíš denní limit o X kcal.
Aktuálně: 2050 kcal + 200 kcal (toto jídlo) = 2250 kcal
```

Stále jej ulož, ale dej uživateli toto varování.

## Edgecase: Chyba v čtení souboru
Pokud soubor nelze přečíst, řekni:
```
Nedaří se mi přečíst záznam z dnešku. Začínám od nuly.
```

A pokračuj dál. Nový záznam se uloží.

## Příklady úplné interakce

### Příklad 1: Jednoduché jídlo
```
Uživatel: Právě jsem snědl jablko
Agent: Zaznamenávám: jablko (~95 kcal)
       Celkem dnes: 95 kcal
       Zbývá ti: 2105 kcal
```

### Příklad 2: Složitější jídlo s upřesněním
```
Uživatel: Obědval jsem kuřecí plátek
Agent: Kolik to bylo? Je to plátek z jednoho prsa, nebo menší kousek?
       A bylo to vařené, pečené, nebo smažené?

Uživatel: Jedno kuřecí prso, pečené
Agent: A bylo to se zeleninou, nebo samo?

Uživatel: Se smaženou rýží
Agent: Kolik rýže přibližně? (malá porce ~150g, velká ~250g?)

Uživatel: Malá porce
Agent: Zaznamenávám: kuřecí prso (pečené) se smaženou rýží (~500 kcal)
       Celkem dnes: 595 kcal
       Zbývá ti: 1605 kcal
```

## Poznámky pro agenta
- Vždy buď vlídný a motivující
- Pokud uživatel zadá neurčitý popis, zeptej se před odhadem
- Při větších porších si pamatuj, že obvyklé porce jsou 150-250g (maso) nebo 100-200g (ostatní)
- Pokud si nejsi jistý odhadem, raději prozraď výsledek odhadu s rozpětím ("asi 400-500 kcal")
