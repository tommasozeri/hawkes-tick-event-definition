# The event definition is the model: fitting a multivariate Hawkes process to tick quotes

A practical note on fitting a multivariate exponential Hawkes process to high-frequency quotes, on the Binance BTC perpetual and spot markets. The main lesson comes before any estimation: what counts as an event decides what the model says.

**Paper:** [paper/hawkes-tick-event-definition.pdf](paper/hawkes-tick-event-definition.pdf) (9 pages).
A plain-language explanation in Italian is in [paper/hawkes-tick-event-definition-spiegazione.pdf](paper/hawkes-tick-event-definition-spiegazione.pdf).

## What it finds

- **Every mid change is the wrong event.** It gives 1.4 million events a day, with a median gap of 33 microseconds and kernels with half-lives of a few microseconds.
- **Filtering the bursts is not enough.** The remaining moves have different sizes, and the fitted model implies a basis that widens permanently, with an hourly standard deviation of hundreds of dollars, against a basis that in the data reverts within seconds.
- **A threshold on a reference price fixes it.** An event is a move of at least 0.5 bp from a reference price, on quotes cleaned of glitches. That gives a tenth of the events with nearly identical sizes on both legs. The fit is then stationary in every window of thirteen days (spectral radius 0.64 to 0.91), cross-excitation exceeds self-excitation on every BTC day from 2024, and the time-rescaling statistics fall by an order of magnitude.
- **The toolkit.** O(N) recursive likelihood with analytic gradients, two exponentials per pair, a piecewise baseline against spurious criticality, multi-start optimisation, time-rescaling diagnostics, and tests that simulate with known parameters and recover them.

## Data and sources

Thirteen days between December 2022 and September 2026, best quotes at microsecond resolution from Tardis.dev. The quotes are licensed and are not in this repository. The code excerpts in the paper are quoted from the author's thesis code, which is not public yet.

`paper/src/` holds the LaTeX sources of the paper and of the Italian explanation and the CSV files behind the figures. Build with `latexmk -xelatex paper.tex` from that folder.

## Limits

Two exponentials per pair do not exhaust the clustering: the Ljung–Box and dispersion tests on the rescaled times still reject in a large share of the windows. The spot stream has no exchange timestamp, which bounds the precision of the fastest kernels.

Based on the author's master thesis on Hawkes models of the perpetual–spot basis.
