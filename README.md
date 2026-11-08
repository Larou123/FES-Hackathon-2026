<div align="center">

# GeMainsam

**Nachhaltige Entscheidungen im Frankfurter Alltag: leicht zu finden, leicht zu tun, leicht nachzuvollziehen.**

🥈 2. Platz beim FES Hackathon 2026, Frankfurt Impact Challenge. In 24 Stunden von Team 01 gebaut.

[![React Native](https://img.shields.io/badge/React_Native-61DAFB?style=flat-square&logo=react&logoColor=black)](#stack)
[![Expo SDK 57](https://img.shields.io/badge/Expo_SDK_57-000020?style=flat-square&logo=expo&logoColor=white)](#selbst-starten)
[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)](#stack)
[![Plattform](https://img.shields.io/badge/Plattform-iOS,_Android,_Web-555?style=flat-square)](#selbst-starten)

</div>

<br>

<div align="center">
  <a href="docs/media/gemainsam-demo.mp4">
    <img src="docs/media/gemainsam-demo.gif" width="720" alt="Leon das Chamäleon führt durch die App: Stadtkarte mit Angeboten in der Nähe, Lebensmittelrettung mit KI-Fotoerfassung, und die Impact-Ansicht mit eingespartem CO2e">
  </a>
  <p><sub>Klick auf die Vorschau für das vollständige Demo-Video</sub></p>
</div>

---

## Die Idee

Nachhaltig handeln scheitert in der Stadt selten am Willen, sondern an drei
Dingen: Man weiß nicht, was gerade in der Nähe möglich ist. Man weiß hinterher
nicht, ob es etwas gebracht hat. Und wenn doch etwas belohnt wird, belohnt es
meistens die falsche Sache.

GeMainsam bündelt die Angebote, die es in Frankfurt ohnehin gibt — Bus und Bahn,
Mehrwegbehälter, gerettete Lebensmittel, saubere Plätze — an einem Ort, prüft
jede gemeldete Aktion gegen echte Daten und rechnet zwei Dinge getrennt aus:
**was es der Umwelt gebracht hat** und **was es dir bringt**. Durch die App
führt Leon, ein Chamäleon, das die Oberfläche je nach Bereich einfärbt und
jede Punktgutschrift erklärt.

---

## Was man damit machen kann

- 🚌 **Fahrt statt Fahrt mit dem Auto** — Weg mit Bus oder Bahn zurücklegen; die
  App gleicht den GPS-Verlauf gegen den Fahrplan ab und sagt, wie sicher sie
  sich ist.
- 🥕 **Lebensmittel retten** — Körbe und Fairteiler von foodsharing finden,
  Übergabe per QR-Code bestätigen, Inhalt per Foto erfassen.
- ♻️ **Mehrweg nutzen** — Vytal-Behälter ausleihen und zurückgeben, statt
  Einweg wegzuwerfen.
- 🧹 **Die Stadt sauber halten** — Verschmutzungen melden und an Aktionen
  teilnehmen.
- 🌍 **Wirkung sehen** — eingespartes CO2e und gerettete Kilogramm getrennt von
  den Punkten, mit Quelle hinter jeder Zahl.

---

## Fair konstruiert

Das ist der Teil, an dem die meisten Belohnungs-Apps scheitern, und der Teil,
über den wir am längsten gestritten haben.

- **Punkte und Impact sind zwei Währungen.** Impact ist Physik: Kilogramm CO2e,
  Kilogramm Lebensmittel. Punkte sind eine gestaltete Verhaltenswährung —
  gedeckelt, mit zunehmender Menge abnehmend, nie proportional.
- **Punkte gibt es für die Entscheidung, nicht für die Strecke oder die Menge.**
  Sonst lohnt sich die lange Fahrt mehr als die kurze, und das ist genau falsch
  herum.
- **Keine Punkte pro Müllstück.** Wer pro Stück zahlt, bekommt Müll — der
  Kobra-Effekt ist in `konzept/02` ausführlich beschrieben.
- **Der Nachweisstatus ist ein sichtbarer Multiplikator:** bestätigt 1,0 ·
  plausibel 0,7 · schwach plausibel 0,4 · selbst angegeben 0,3 · nicht
  zuordenbar 0,0. Er steht in der App, nicht im Kleingedruckten.
- **Jede Gutschrift hat ein „Warum"-Sheet** mit Status, Formel, Faktor und
  Quelle. Man kann jede Zahl bis zu ihrem Ursprung aufklappen.
- **Keine Rangliste als Hauptmotor.** Gemeinsame Ziele und der Vergleich mit
  der eigenen Vorwoche. Belohnt wird über Schwellen und Lose, nie über Platz.

Das vollständige Modell: [`konzept/02-PUNKTE-UND-ANTI-FEHLANREIZ.md`](konzept/02-PUNKTE-UND-ANTI-FEHLANREIZ.md).

---

## Wie es gebaut ist

Die App spricht nie direkt mit einer Datenquelle. Dazwischen liegen genau zwei
Schnittstellen, die in der ersten Stunde festgelegt und danach nur noch
erweitert wurden:

```
getOpportunities({lat, lon, radius}) -> Opportunity[]
award(event: ActionEvent)            -> {points, impact, status, reason[], idempotency_key}
```

`award` ist die **einzige** Stelle im Code, die Punkte vergibt. Keine Ausnahme.
Das klingt nach Bürokratie, hat aber dafür gesorgt, dass die Fairness-Regeln
oben an einer Stelle stehen und nicht über fünf Screens verstreut sind — und
dass der `idempotency_key` doppelte Gutschriften bei wackligem Netz verhindert.

Der Nachweis für Fahrten läuft über GPS gegen GTFS-Fahrplandaten mit einer
Konfidenz, nicht über NFC: In Frankfurter Bussen gibt es keine Tags, und ein
Fundament auf nicht vorhandener Hardware trägt nicht.

Jeder Screen hat einen Mock-Fallback. Fällt im Pitch eine API aus, zeigt der
Screen trotzdem etwas — als Demo-Daten gekennzeichnet.

---

## Stack

| Bereich | Technologie |
| :-- | :-- |
| App | React Native, Expo SDK 57, Expo Router, TypeScript |
| State | zustand |
| Karte | react-native-maps |
| Server | Trust-Server unter `apps/trust-server/` |
| Daten | GTFS Frankfurt + 30 km, foodsharing-API, Vytal, FES |

---

## Selbst starten

Node **22.18+**, npm und Expo Go mit Unterstützung für **SDK 57**.

```bash
cd apps/mobile
npm ci --include=optional
npm run dev:lan
```

`dev:lan` startet Trust-Server und Expo unter derselben privaten LAN-Adresse.
Rechner und Handy müssen im selben WLAN sein, unter iOS muss Expo Go Zugriff
auf das lokale Netzwerk bekommen. Es wird kein öffentlicher Tunnel geöffnet.

Nur im Browser: `npm run web`, für gemeinsame Daten zusätzlich
`npm run trust:start`. Weitere Startvarianten in
[`apps/mobile/README.md`](apps/mobile/README.md), der Demo-Ablauf in
[`apps/mobile/DEMO.md`](apps/mobile/DEMO.md).

---

## Was im Repo liegt

| Ordner | Inhalt |
| :-- | :-- |
| [`apps/mobile/`](apps/mobile/) | die App — Expo, React Native, expo-router |
| [`apps/trust-server/`](apps/trust-server/) | Server für geteilte Daten und Nachweise |
| [`konzept/`](konzept/) | Punktemodell, Anti-Fehlanreiz-Analyse, Entscheidungen |
| [`Mobilitätsdaten/`](Mobilitätsdaten/) | GTFS Frankfurt + 30 km, Haltestellen, Tagesgang |
| [`Foodsharing API/`](Foodsharing%20API/) | Schema und Anleitung zur foodsharing-API |
| [`docs/`](docs/) | Dokumentation und Demo-Medien |
| [`shared/`](shared/) | gemeinsame Typen und Hilfsfunktionen |

---

## Gut zu wissen

- **Das ist ein Hackathon-Prototyp**, gebaut in 24 Stunden. Lauffähig, aber
  nicht produktionsreif.
- **Nicht alle Daten sind echt.** Die GTFS-Fahrplandaten und die
  foodsharing-API sind es. Teile der Mobilitätsdaten
  (`haltestellen_avg.csv`, `tagesgang_avg.csv`, die Beispieldatensätze) sind
  synthetisch, Vytal läuft gegen Mocks. In der App ist beides als
  `Schätzung` oder `Demo-Daten` gekennzeichnet.
- **Die Emissionsfaktoren sind Schätzungen mit Quelle**, keine Messwerte.
- **`Foodsharing API/KEYS.txt` liegt in der Git-History dieses Repos.** Die
  Datei stammt aus dem ursprünglichen Team-Repo und ist dort schon öffentlich.
  Die Keys gelten als kompromittiert und gehören rotiert.

---

## Team

Gebaut von **Team 01** beim FES Hackathon 2026 in Frankfurt — sechs Leute,
24 Stunden. Veranstalter war die FES Frankfurter Entsorgungs- und Service GmbH;
Daten und Schnittstellen kamen von Transdev, traffiQ, Vytal und foodsharing.

Dieses Repository ist mein Stand des Teamprojekts. Das Demo-Video stammt von
meinem Teamkollegen [@Imado30](https://github.com/Imado30). Alle Beteiligten
stehen in der [Contributors-Liste](https://github.com/Larou123/FES-Hackathon-2026/graphs/contributors).
