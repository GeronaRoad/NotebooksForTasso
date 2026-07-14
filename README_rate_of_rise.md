# Rate-of-Rise als Umkehrproblem

Ein eigenständiges Jupyter-Notebook zur simulationsbasierten Inferenz an
Rate-of-Rise-Messungen (Druckanstieg bei geschlossenem Ventil). Statt ein
Modell vorab festzulegen und zu fitten, wird der Posterior über die
physikalischen Parameter direkt geschätzt und daraus die informativste
Folgemessung abgeleitet.

## Fragestellung

Eine Rate-of-Rise-Kurve ist mehrdeutig: Ein echtes Leck (linearer, unbegrenzter
Anstieg) und ein gebundenes Reservoir bzw. virtuelles Leck (sättigender Anstieg)
können bei kurzer, kalter Messung nahezu dieselbe Kurve erzeugen. Das Notebook
behandelt diese Zuordnung als Inversionsproblem und zeigt drei Dinge, die über
einen klassischen Chi-Quadrat-Fit hinausgehen:

- **Amortisierte Posterior-Schätzung.** Ein neuronaler Schätzer wird einmal an
  simulierten Kurven trainiert und liefert danach für jede neue Messung in
  Millisekunden die volle Parameterverteilung – ohne Likelihood-Auswertung und
  ohne vorab gewähltes Modell.
- **Sichtbare Entartung.** Wo Leck und Reservoir aus einer Kurve nicht trennbar
  sind, ergibt sich kein Fehlwert, sondern ein explizit entarteter Posterior
  (antikorrelierte Verteilung im Parameterraum).
- **Selbstgeplante Folgemessung.** Über den erwarteten Informationsgewinn
  bestimmt das Notebook, welche Zusatzmessung die Entartung am stärksten bricht –
  hier die Wahl der Sondiertemperatur (Aufheizen sättigt das Reservoir und trennt
  es von einem echten Leck).

## Aufbau des Notebooks

1. Vorwärtsmodell des Vakuumsystems (Leck, Reservoir mit Arrhenius-Freisetzung,
   Messrauschen) und Darstellung der kalt/warm-Mehrdeutigkeit.
2. Amortisierter neuronaler Posterior (Mixture-Density-Netz).
3. Kalibrierungsprüfung mittels simulationsbasierter Kalibrierung (SBC).
4. Sichtbarmachung der Leck-Reservoir-Entartung an klaren und mehrdeutigen
   Fällen.
5. Bayessche Versuchsplanung: erwarteter Informationsgewinn über die
   Sondiertemperatur und Kollaps des Posteriors nach der empfohlenen Messung.
6. Einordnung, Grenzen und methodische Referenzen.

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

Alles läuft auf CPU. Das Training beider Schätzer dauert zusammen wenige Minuten.

## Voraussetzungen

- Python 3.9 oder neuer
- `numpy`, `matplotlib`, `scipy`, `torch`

## Hinweis zu den Daten

Sämtliche Kurven werden im Notebook simuliert. Das Vorwärtsmodell ist bewusst
kompakt gehalten; die Methodik (Posterior-Schätzung, Kalibrierung,
Versuchsplanung) bleibt unverändert, wenn man das Vorwärtsmodell durch ein
realistischeres ersetzt.

## Referenzen

- Cranmer, Brehmer, Louppe, *The frontier of simulation-based inference*, PNAS 2020.
- Papamakarios & Murray, *Fast epsilon-free inference of simulation models*, NeurIPS 2016.
- Talts et al., *Validating Bayesian inference algorithms with simulation-based calibration*, 2018.
- Lindley (1956); Ryan et al. (2016) zur bayesschen Versuchsplanung.
