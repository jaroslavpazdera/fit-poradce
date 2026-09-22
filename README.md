# Fit Poradce

Inteligentní chatbot aplikace pro sledování a optimalizaci kalorického příjmu.

## 📋 O projektu

**Fit Poradce** je chatbot, který vám pomáhá sledovat svůj kalorický příjem a optimalizovat svůj jídelníček. Aplikace funguje v Claude Code a komunikuje s vami v přirozeném česttině.

### Hlavní funkce:
- 📊 **Odhad kalorií** - agent odhadne kalorický obsah vašeho jídla
- 💾 **Automatický záznam** - záznamy se ukládají do denních souborů
- 🍽️ **Doporučení jídelníčku** - agent navrhne zbývající jídla
- 🎯 **Sledování cíle** - vždy víte, kolik kalorií vám zbývá
- 💬 **Přirozený jazyk** - komunikace bez formulářů
- 🔒 **Soukromí** - data zůstávají lokálně na vašem počítači

## 📁 Struktura projektu

```
fit-poradce/
├── index.html                    # Landing page
├── README.md                      # Tento soubor
├── data/                          # Adresář pro denní záznamy
│   └── [YYYY-MM-DD].txt          # Denní záznamy (automaticky vytvářeno)
└── .claude/
    ├── settings.json              # Konfigurace aplikace
    ├── CLAUDE.md                  # Dokumentace pro agenty
    └── skills/
        ├── odhad-kalorii/
        │   └── SKILL.md           # Skill pro odhad kalorií
        └── doporuceni-kalorii/
            └── SKILL.md           # Skill pro návrh jídelníčku
```

## 🚀 Jak začít

### 1. Otevřete projekt v Claude Code
```bash
claude-code fit-poradce
```

### 2. Vyzveďte skill "odhad-kalorii"
Řekněte agentovi, co jste snědl/a:
```
Právě jsem snědl jablko a kousek chleba se sádlem
```

Agent:
- Odhadne kalorií
- Uloží záznam do `data/[dnešní-datum].txt`
- Řekne vám, kolik kalorií zbývá

### 3. Sledujte svůj pokrok
Agent vám čekoliv řekne aktuální stav:
```
Celkem dnes: 295 kcal
Zbývá ti: 1905 kcal
```

### 4. Vyzveďte skill "doporuceni-kalorii"
Pokud potřebujete radu:
```
Navrhni mi jídelníček na zbytek dne
```

Agent:
- Přečte záznam dne
- Vypočítá zbývající kalorií
- Navrhne konkrétní jídla a časy

## 📊 Modelový profil

Aplikace je optimalizována pro:
- **Věk**: 50 let
- **Pohlaví**: Muž
- **Výška**: 190 cm
- **Váha**: 105 kg
- **Zaměstnání**: Sedavé
- **Cílová denní kalorie**: 2200 kcal
- **BMR**: 1650 kcal
- **TDEE**: 2200 kcal

## 📝 Příklady použití

### Příklad 1: Záznam jídla
```
Uživatel: Obědval jsem kuřecí prso s rýží
Agent: Kolik to bylo? Je to plátek z jednoho prsa?
       A bylo to vařené, pečené, nebo smažené?

Uživatel: Jedno kuřecí prso, pečené se smaženou rýží. Malá porce.
Agent: Zaznamenávám: kuřecí prso (pečené) se smaženou rýží (~500 kcal)
       
       Záznam: data/2026-09-22.txt
       [13:00] Jídlo: kuřecí prso (pečené) se smaženou rýží | Odhad kalorií: 500 | Celkem dnes: 500 kcal
       
       Zbývá ti: 1700 kcal na dnešek
```

### Příklad 2: Návrh jídelníčku
```
Uživatel: Navrhni mi zbývající jídla
Agent: Čtu tvůj záznam z dnešku.
       Dosavadní příjem: 500 kcal
       Zbývající kapacita: 1700 kcal
       
       DOPORUČUJI:
       - 15:00 Svačina: Jablko + kousek sýra (180 kcal)
       - 18:30 Večeře: Losos s brambory (500 kcal)
       - Ostatní (čaj, nápoje): 520 kcal
       
       Celkem s návrhem: 1700 kcal
```

## 📂 Datové soubory

Záznamy se ukládají do `data/[YYYY-MM-DD].txt`:

```
[14:30] Jídlo: jablko | Odhad kalorií: 95 | Celkem dnes: 95 kcal
[15:45] Jídlo: kuřecí prso se smaženou rýží | Odhad kalorií: 500 | Celkem dnes: 595 kcal
[16:20] Jídlo: jogurt s ovocem | Odhad kalorií: 150 | Celkem dnes: 745 kcal
```

## ⚙️ Konfigurace

Základní parametry jsou v `.claude/settings.json`:
- Osobní údaje
- Denní cíl kalorií
- Doporučené rozdělení kalorií na jídla
- Cesta k datovému adresáři

Pokud chcete změnit denní cíl nebo parametry, editujte tento soubor.

## 🌐 Landing page

Veřejná stránka `index.html` slouží jako propagace a instruktážní stránka aplikace.

Pokud máte GitHub repozitář, můžete ji publikovat na GitHub Pages:
1. Nahrajte obsah na GitHub
2. Aktivujte GitHub Pages v nastavení repozitáře
3. Stránka bude dostupná na `https://<username>.github.io/<repo-name>`

## 💡 Tipy a triky

### Jak si pamatovat porce?
- **Malá porce mäsa/ryby**: ~100g (160-200 kcal)
- **Normální porce**: ~150g (250-300 kcal)
- **Velká porce**: ~200g+ (300+ kcal)

### Neurčité popisy
Pokud zadáte "oběd" nebo "velkou porci", agent se zeptá na upřesnění. To pomáhá přesnějšímu odhadu.

### Překročení denního cíle
Pokud překročíte 2200 kcal, agent vás upozorní, ale stále vám umožní si jídlo zaznamenat. Není to katastrofa!

### Soubory s daty
Můžete si kdykoliv podívat, co jste snědl/a, přečtením souboru `data/[datum].txt`.

## 🔒 Bezpečnost a soukromí

- Všechna data jsou uložena **lokálně** na vašem počítači
- Nic se neodesílá na server
- Jsou to prostě textové soubory ve vašem adresáři
- Můžete je kdyloliv smazat nebo archivovat

## 📞 Podpora

Pokud máte otázky nebo nápady, podívejte se na:
- `.claude/CLAUDE.md` - detailní dokumentace pro agenty
- `.claude/skills/odhad-kalorii/SKILL.md` - jak funguje odhad
- `.claude/skills/doporuceni-kalorii/SKILL.md` - jak funguje návrh

## 📄 Licence

Tato aplikace je zdarma k používání. Vytvořeno s Claude Code.

---

**Vážení uživatelé, všechno je připraveno. Začněte s `/odhad-kalorii` skillelem!** 🍎
