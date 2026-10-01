<img src="logo.png" width="96" align="left" alt="Preflight">

# Preflight

**Färdplanering för VFR-flygning.** Vind på höjd, tryck- och densitetshöjd, TAS, sträckberäkning, bränsle, landningsmassa och sidvind mot banan.

<br clear="left">

---

> [!WARNING]
> **Detta är ett planeringshjälpmedel, inte ett certifierat färdplaneringssystem.**
> Appen är varken godkänd eller granskad av någon luftfartsmyndighet och får inte användas
> som enda underlag för en flygning. Kontrollera alltid mot officiell briefing, luftfartygets
> flyghandbok (POH) och gällande regelverk. Befälhavaren ansvarar ensam för färdplaneringen.

---

## Vad den gör

Varje steg matar nästa: densitetshöjden går in i TAS, TAS och vinden går in i sträckorna, sträckbränslet går in i både bränslekalkylen och landningsmassan.

| # | Steg | Innehåll |
|---|------|----------|
| 1 | Karta | Checklista för kartförberedelsen |
| 2 | Vind och temperatur | Interpolation mellan 2000 ft, FL050 och FL100 |
| 3 | Tryck- och densitetshöjd | QNH-korrigering och ISA-avvikelse |
| 4 | TAS | Räknad på densitetshöjden |
| 5 | Sträckor | WCA, GS, KK, interval- och ackumulerad tid och bränsle |
| 6 | Bränsle | Taxi, sträcka, kontingens, alternat, reserv, extra och marginal |
| 7 | Landningsmassa | Startmassa minus förbrukat bränsle |
| 8 | Tid | HH:mm:ss till decimalform och tillbaka |
| 9 | Sidvind | Grafisk banvy med norr uppåt och vindstrut |

Varje uträknat fält visar formeln som används, med dina egna värden insatta.

## Formler

```
Interpolation      f = (h − h₁)/(h₂ − h₁),  värde = v₁ + f × (v₂ − v₁)
                   riktning interpoleras över kortaste vinkeln

Tryckhöjd          TH = IA + (1013 − QNH) × 27
ISA                ISA = 15 − 2 × h/1000
Densitetshöjd      DH = TH + 120 × (OAT − ISA)

TAS                σ = (1 − 6,8756×10⁻⁶ × h)^4,2559
                   TAS = IAS / √σ

Vindvinkel         θ = vindriktning − rättvisande kurs
WCA                WCA = arcsin(vind × sin θ / TAS)
Kurser             RV = RK + WCA,  MK = RV − missvisning,  KK = MK − deviation
Groundspeed        GS = TAS × cos WCA − vind × cos θ
Tid och bränsle    tid = distans / GS × 60,  bränsle = tid/60 × flöde
Marktakt           nm/min = distans / tid = GS / 60

Sidvind            banriktning = bannummer × 10
                   sidvind = vind × sin θ,  mot-/medvind = vind × cos θ
```

Kursformlerna följer regeln *öst är minst* — ostlig missvisning och deviation dras ifrån.

## Installation

Appen är statisk och ligger i katalogen `docs/`, som körs var som helst. På GitHub Pages:

1. **Settings → Pages → Deploy from a branch**, välj `main` och `/docs`.
2. Adressen blir `https://<användarnamn>.github.io/<repo>/`.

### På telefonen

Öppna adressen i Safari eller Chrome, välj dela och **Lägg till på hemskärmen**. Appen startar då i helskärm med egen ikon och fungerar offline efter första besöket.

## Offline

En service worker cachar appen vid första besöket. Vid uppdatering måste cacheversionen höjas, annars sitter redan installerade enheter kvar på den gamla versionen:

```js
// docs/sw.js
const CACHE = 'preflight-v1';   // → 'preflight-v2'
```

## Filer

```
docs/                        appen, det som publiceras
  index.html                 hela appen, typsnitt inbakade, inga externa beroenden
  sw.js                      offline-cache
  manifest.webmanifest       namn, färger och ikoner för hemskärmen
  icon-192.png               ikon
  icon-512.png               ikon
  icon-maskable.png          ikon med marginal för Android
  apple-touch-icon.png       ikon för iOS
logo.png                     logotyp för den här filen
```

## Integritet

Inget lämnar enheten. Alla värden sparas i webbläsarens `localStorage` och appen gör inga nätverksanrop — varken analys, spårning eller externa typsnitt. Knappen **Nollställ allt** längst ned raderar sparad data.

## Teknik

Ren HTML, CSS och JavaScript utan ramverk eller byggsteg. Typsnitten är [Source Sans 3](https://github.com/adobe-fonts/source-sans) och [IBM Plex Mono](https://github.com/IBM/plex), båda under SIL Open Font License 1.1 och inbakade som woff2.

## Licens

[MIT](LICENSE) — se särskilt ansvarsfriskrivningen överst och garantifriskrivningen i licenstexten.
