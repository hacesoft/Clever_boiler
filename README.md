# CLEVER BOILER
## Ovládání běžného bojleru pomocí automatizace

### Návod na ovládání bojleru pomocí NODE-RED

> **Upozornění:** Pracujete s nebezpečným napětím! Pokud nevíte, co děláte, nesnažte se to zapojovat sami!

### Co je „hloupý“ bojler?
Jedná se o bojler, který má pouze mechanický termostat, umožňující nastavit teplotu a nic víc. Tento návod umožňuje jeho chytré ovládání, a to jak pro **jednofázové (1F)**, tak pro **třífázové (3F)** bojlery.

### Základní komponenty:
Hlavním prvkem je **Shelly 4PM Pro** [návod zde](https://www.navod-k-obsluze.cz/modul-shelly-pro-4pm-4nasobny-spinaci-modul-na-din-listu-wifi-106344-navod), který je umístěný na **DIN liště** v domovním rozvaděči.

![image](https://github.com/user-attachments/assets/4bec9e6b-5d37-409c-b07f-d5f3404ef31a)

Kromě toho je potřeba doplnit elektroinstalaci o další komponenty, jako jsou jističe a relé na DIN lištu.  
Tento návod poskytuje **obecné schéma zapojení** pro inspiraci – pro konkrétní použití může být nutné jej upravit.

![image](https://github.com/user-attachments/assets/0379a8e6-eee0-41a5-b062-8a0eae5bdfc3)

### Zapojení a funkce:
Schéma obsahuje hlavní **proudový chránič (FI)** a **přepěťovou ochranu**. Bojler je řízen stykačem **KM1** – výběr konkrétního modelu závisí na daném výkonu bojleru.

#### **Funkce výstupů O1 a O2:**
- **Výstup O1** ovládá stykač KM1 a zapíná či vypíná bojler.
- **Výstup O2** řídí režim provozu:
  - Pokud **O2 není aktivní**, ale **O1 je zapnutý** a **není aktivní HDO**, bojler se **nezapne**.
  - Pokud **O2 je aktivní**, bojler **běží neustále**, bez ohledu na stav HDO.
  - Tento režim je výhodný pro **uživatele s FVE**, kdy není potřeba řídit bojler podle nízkého tarifu, ale podle dostupnosti solární energie.
  - Výstupy **O3 a O4** jsou volné pro další použití.

**Vstup S1** monitoruje stav nízkého a vysokého tarifu (HDO). Tuto informaci lze využít pro další řízení domácí automatizace.

Toto zapojení poskytuje dostatek možností pro chytré řízení bojleru.

### Nastavení:
V **Shelly 4PM PRO** doporučuji nastavit **schedule** podle vašich preferencí:
- **Zimní měsíce:**
  - Zapnutí bojleru např. v **10:00** (většina energie se v tuto dobu bere z **GRIDu**).
  - Vypnutí ohřívání při **západu slunce**.
  - **O2 výstup vypnutý**, bojler se spouští podle nízkého tarifu.
- **Letní měsíce:**
  - Zapnutí bojleru např. v **11:00**.
  - **O2 výstup zapnutý**, bojler se zapíná i mimo nízký tarif.
  - Vypnutí hodinu před západem slunce.
  - Termostat nastaven na **MAX**.
- **Shedule slouží jako pojistka**, pokud selže automatizace (např. v **Node-RED**). Bojler se vždy zapne a vypne dle základního nastavení.

### Ovládání přes API:
Pro ovládání přes **HTTP požadavky** lze použít:
- Zapnout O1: `http://<IP_adresa>/relay/0?turn=on`
- Vypnout O1: `http://<IP_adresa>/relay/0?turn=off`
- Zapnout O2: `http://<IP_adresa>/relay/1?turn=on`
- Vypnout O2: `http://<IP_adresa>/relay/1?turn=off`

Pro zjištění stavu vstupů:
- `http://<IP_adresa>/rpc/Shelly.GetStatus`
- Ve struktuře dat hledejte položky `input:0`.

**Ukázka odpovědi:**
```json
  "input:0": {
    "id": 0,
    "state": true
  },
  "input:1": {
    "id": 1,
    "state": false
  },
  "input:2": {
    "id": 2,
    "state": false
  },
  "input:3": {
    "id": 3,
    "state": false
  }
```

### Upozornění:
- **Nepřipojujte Shelly do cloudu**, je to nespolehlivé.
- Udržujte všechny **Shelly komponenty v dosahu vaší automatizace**.
- Pokud používáte **VLANy**, nezapomeňte správně nastavit **firewallová pravidla**.
- **Preferujte připojení přes LAN kabel**, abyste se vyhnuli výpadkům WiFi.

#### **Stykač KM1:**
- Slouží jako hlavní **výkonový spínač bojleru**.
- Lze jej ovládat i **manuálně** v rozvaděči:
  - **Páčka nahoře** → trvale **zapnuto**, bojler běží nezávisle na ostatních relé.
  - **Páčka dole** → trvale **vypnuto**, bojler se nespustí bez ohledu na ostatní relé.
  - **Režim AUTO** → bojler se řídí podle nastavení relé **RE1–RE4**.

#### **Funkce jednotlivých relé:**
- **RE1** – Hlavní ovládání bojleru. Spínání dle nastaveného času a vzdálené ovládání přes **RE3**.
- **RE2** – Přepínání mezi nočním proudem (výchozí) a možností spustit bojler kdykoliv (užitečné při přebytku energie z FVE).
- **RE3** – Inteligentní relé (**Shelly 4PM Pro**) s 4 vstupy a 4 výstupy, umožňující ovládání přes:
  - Časový plán (**schedule**)
  - Skripty
  - Manuální režim
  - Cloud
  - **Home Assistant**, **Node-RED**, aj.
- **RE4** – Řídí vstup **RE3**. Aktivuje se vždy, když je signál **HDO**.


---

Tento systém umožňuje **plně automatizované** a **inteligentní řízení bojleru**, čímž lze optimalizovat spotřebu energie a využití přebytků z FVE.  
V případě jakýchkoli úprav doporučujeme zapojení konzultovat s **elektrikářem**.




---

## Pracujte pečlivě! Dvakrát měř, jednou řež...

---

## Zřeknutí se odpovědnosti

Autor tohoto projektu neposkytuje žádné záruky, výslovné ani implicitní, ohledně správnosti, spolehlivosti, funkčnosti nebo vhodnosti k jakémukoli účelu. Veškeré použití tohoto softwaru, kódu, schémat, návodů, technických řešení, produktů a jakýchkoli dalších poskytnutých materiálů je na vlastní odpovědnost uživatele.

Autor nenese žádnou odpovědnost za jakékoli škody, ztráty, finanční náklady, přímé či nepřímé škody vzniklé v důsledku použití těchto materiálů, a to včetně, ale nejen, ztráty dat, poškození zařízení, výpadků systému, poruchy elektrických či jiných instalací, požárů, ztrát příjmů nebo jiných nepředvídatelných následků.

Uživatel bere na vědomí, že jakékoli úpravy, sestavování, instalace, zapojení či implementace na základě poskytnutých informací provádí výhradně na vlastní riziko. Autor neposkytuje žádné garance funkčnosti, bezpečnosti ani souladu s platnými právními normami a předpisy.

Uživatel se zavazuje, že nevyužije žádné právní kroky vůči autorovi v souvislosti s jakýmikoli škodami nebo jinými nároky vyplývajícími z používání tohoto softwaru, produktů, schémat nebo návodů. Jakékoli právní nároky vůči autorovi jsou tímto výslovně vyloučeny a nevymahatelné, a to i soudní cestou.

Použitím těchto materiálů uživatel potvrzuje svůj souhlas s výše uvedenými podmínkami. Pokud s nimi nesouhlasíte, nepoužívejte tento software, schémata, návody ani jiné poskytnuté materiály.
