# Reibungsereignisse am Tragband: Ortung mit einem einzelnen Sensor

Ein eigenständiges Jupyter-Notebook zur simulationsbasierten Inferenz an
Vibrationssignalen einer Absenkwinde. Ein einzelner Beschleunigungssensor an
der Winde liefert nicht nur ein Ja/Nein für ein Reibungsereignis, sondern –
über die Dispersion der Biegewelle im Tragband – auch dessen Tiefe und Stärke,
inklusive kalibrierter Unsicherheit und einer selbst geplanten Folgemessung.

Enthält zwei Notebooks:

| Datei | Status |
|---|---|
| `legend_band_lokalisierung_PROTOTYP.ipynb` | Machbarkeitsnachweis. Zeigt die Methode und die Kernergebnisse, dokumentiert aber offen eine nicht bestandene Kalibrierungsprüfung. |
| `legend_band_lokalisierung.ipynb` | Finale Fassung. Behebt die Kalibrierungsschwäche des Prototyps und besteht den Kalibrierungsnachweis. |

Für den Einsatz genügt die finale Fassung; der Prototyp ist als Beleg dafür
erhalten, dass ein überzeugend aussehender Schätzer nicht automatisch ein
kalibrierter ist.

## Fragestellung

Ein dünnes Tragband ist für Biegewellen ein dispersives Medium
($\omega = a k^2$): hohe Frequenzen laufen schneller als tiefe. Ein
Reibungsereignis in Tiefe *d* kommt am Sensor als Chirp an, dessen
Zeit-Frequenz-Struktur die Laufstrecke kodiert – Ortung aus einer einzigen
Zeitreihe, wo klassisch mehrere verteilte Sensoren nötig wären. Die
Dispersionskonstante *a* hängt jedoch von der (im Betrieb nicht exakt
bekannten) Bandspannung ab; aus dem passiven Signal sind Tiefe und Spannung
nur als Verhältnis $d/\sqrt{a}$ bestimmt – eine echte Entartung. Das Notebook
behandelt das als Inversionsproblem:

- **Amortisierte Posterior-Schätzung** $p(d, s, a \mid x)$ aus simulierten
  Signalen, mit den drei Bausteinen, die eine belastbare Kalibrierung
  überhaupt erst ermöglichen: Vollkovarianz-Mixture-Komponenten (die schräge
  Entartungsrichtung braucht gekippte, nicht achsenparallele Gaußkomponenten),
  Logit-Transformation der beschränkten Parameter und ein affiner Debias nach
  Early Stopping.
- **Kalibrierungsnachweis (SBC).** Erst nach diesen drei Korrekturen bestehen
  alle drei Parameter die simulationsbasierte Kalibrierung; der Prototyp tat
  das noch nicht.
- **Bayessche Versuchsplanung.** Der erwartete Informationsgewinn vergleicht
  zwei Optionen – ein weiteres passives Ereignis abwarten oder einen aktiven
  Ping senden, dessen Echo über die bekannte Bandlänge die Spannung direkt
  misst und die Entartung bricht.

## Aufbau der finalen Fassung

1. Dispersives Vorwärtsmodell (passives Ereignis und aktiver Ping) und
   Visualisierung der Chirp-Signatur.
2. Zeit-Frequenz-Merkmale und Vollkovarianz-Mixture-Density-Netz mit
   Logit-Transformation und Debias.
3. Simulationsbasierte Kalibrierung (SBC) als quantitativer Nachweis.
4. Sichtbarmachung der Tiefe–Spannungs-Entartung an mehreren Ereignissen.
5. Erwarteter Informationsgewinn: passiv warten vs. aktiver Ping, und der
   tatsächliche Kollaps des Posteriors nach der empfohlenen Messung.
6. Automatisierte Tests, Einordnung, Grenzen und methodische Referenzen.

## Ausführen

### Google Colab

Notebook hochladen (colab.research.google.com, *Datei -> Notebook hochladen*)
und die Zellen der Reihe nach ausführen. `torch` und `scipy` sind in Colab
vorinstalliert.

### Lokal

```bash
pip install numpy matplotlib scipy torch jupyter
jupyter notebook
```

Alles läuft auf CPU. Die finale Fassung trainiert drei Schätzer und dauert
insgesamt einige Minuten.

## Voraussetzungen

- Python 3.9 oder neuer
- `numpy`, `matplotlib`, `scipy`, `torch`

## Hinweis zu den Daten

Sämtliche Signale werden im Notebook simuliert. Das Wellenmodell ist bewusst
minimal (eine Biegemode, ortskonstante Dispersionskonstante, feste
Quellsignatur, frequenzproportionale Pauschaldämpfung); ein reales Band bringt
zusätzlich einen spannungsabhängigen Saitenanteil, mehrere Moden und
Temperaturgradienten mit. Die Methodik – Posterior-Schätzung, Kalibrierung,
Versuchsplanung – bleibt unverändert, wenn man das Vorwärtsmodell durch ein
realistischeres ersetzt.

## Referenzen

- Cranmer, Brehmer, Louppe, *The frontier of simulation-based inference*, PNAS 2020.
- Papamakarios & Murray, *Fast epsilon-free inference of simulation models*, NeurIPS 2016.
- Talts et al., *Validating Bayesian inference algorithms with simulation-based calibration*, 2018.
- Lindley (1956); Ryan et al. (2016) zur bayesschen Versuchsplanung.
- Croxford et al., *Strategies for guided-wave structural health monitoring*, Proc. R. Soc. A, 2007.
