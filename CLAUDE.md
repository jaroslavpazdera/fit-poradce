# Aplikace Fit poradce

Chatbot aplikace pro sledování a optimalizaci kalorického příjmu.

## O aplikaci

**Fit poradce** je asistent, který pomáhá uživateli sledovat svůj kalorický příjem a optimalizovat svůj jídelníček. Aplikace funguje jako interaktivní agent, který:

- Přijímá informace o snězeném jídle v přirozeném jazyce
- Odhaduje kalorický obsah na základě standardní databáze potravin
- Ukládá záznamy do denních souborů (`data/[YYYY-MM-DD].txt`)
- Navrhuje zbývající jídla na základě denního cíle

## Modelová osoba

Aplikace je optimalizována pro následující profil:
- **Věk**: 50 let
- **Pohlaví**: Muž
- **Výška**: 190 cm
- **Váha**: 105 kg
- **Zaměstnání**: Sedavé
- **Cílová denní kalorie**: 2200 kcal

## Jak používat aplikaci

### Skill 1: Odhad kalorií

Používejte skills `odhad-kalorii` k záznamů o snězeném jídle.

**Příklady**: 
- "Právě jsem snědl jablko"
- "Obědval jsem kuřecí plátek se smaženou rýží"
- "Pil jsem sklenici mléka"

Agent odhadne kalorický obsah a uloží záznam do souboru dne.

**Formát souboru** (`data/2026-09-22.txt`):
```
[14:30] Jídlo: jablko | Odhad kalorií: 95 | Celkem dnes: 95 kcal
[13:00] Jídlo: kuřecí plátek se smaženou rýží | Odhad kalorií: 750 | Celkem dnes: 845 kcal
```

### Skill 2: Doporučení jídelníčku

Používejte skill `doporuceni-kalorii` k získání doporučení zbývajících jídel.

**Příklady**:
- "Navrhni mi jídelníček na zbytek dne"
- "Co bych měl ještě jíst?"

Agent:
1. Přečte záznam dne (`data/[YYYY-MM-DD].txt`)
2. Vypočítá zbývající kapacitu kalorií
3. Navrhne konkrétní jídla a časy

## Datové soubory

Záznamy se ukládají do souborů formátu `data/[YYYY-MM-DD].txt`:

- Jeden soubor za den
- Append mode (nové záznamy se přidávají na konec)
- Formát: `[HH:MM] Jídlo: <popis> | Odhad kalorií: <kcal> | Celkem dnes: <suma> kcal`

## Příklady interakce

### Příklad 1: Záznam jídla

```
Uživatel: Právě jsem snědl 2 kusy chleba se sádlem
Agent: Zaznamenám si chleba se sádlem (~300 kcal). 
       V souboru 2026-09-22.txt: [14:20] Jídlo: 2 kusy chleba se sádlem | Odhad kalorií: 300 | Celkem dnes: 300 kcal
       Zbývá ti: 1900 kcal na dnešek. Kolik jsi snědl/a ráno?
```

### Příklad 2: Návrh jídelníčku

```
Uživatel: Navrhni mi oběd a večeři
Agent: Čtu tvůj záznam z dnešku. Zatím jsi snědl/a: 300 kcal.
       Zbývá ti: 1900 kcal.
       
       Návrh:
       - Oběd (600 kcal): Kuřecí filé (150g) se zeleninou a těstovinami
       - Svačina (200 kcal): Jogurt s ovocem
       - Večeře (500 kcal): Losos (150g) s bramborami a salátem
       - Buffer (300 kcal): Nápoje, ovoce, čaj
```

## Konfigurace

Základní parametry jsou v souboru `.claude/settings.json`:
- Osobní údaje modelové osoby
- Denní cíl kalorií (2200 kcal)
- Doporučené rozdělení kalorií na jídla
- Cesta k datovému adresáři

## Landing page

Veřejná stránka `index.html` slouží jako propagace a instruktážní stránka aplikace.
