# Data summary: *Beyond Forecast Accuracy: Time-Series Foundation Models for Futures Volatility and Returns*

Dai, Guo and Lien, submitted to the *Journal of Forecasting*.

This repository documents the data used in the paper. **It contains no data.** The futures prices are licensed from Refinitiv (LSEG) Datastream through Wharton Research Data Services (WRDS) and cannot be redistributed. Neither the raw prices nor anything derived from them (front-contract series, volatility series, forecasts) is shared here. A WRDS subscriber can rebuild every dataset with the steps below.

Wharton Research Data Services (WRDS) was used in preparing this paper.

## 1. Source

| | |
|---|---|
| Provider | Refinitiv (LSEG) Datastream, futures contract database |
| Access | Wharton Research Data Services (WRDS), institutional subscription |
| Extract | every listed contract of the 21 markets below, daily, 2000–2026 |
| Fields | contract identifier (FutCode), market class (ClsCode), date, open, high, low, settlement, volume, open interest; contract metadata (mnemonic, currency, last trading day, settlement and first-notice dates, trading months) |
| Sample used | January 2005 (the first date with complete high and low prices) to the data end of each market |

## 2. Markets

21 liquid benchmark futures in the United States and Europe. Four core markets (ES, CL, GC, ZS) are also reported separately. The statistics are those of Table 1 of the paper, from January 2005.

| Class | Ticker | Contract | Exchange | Datastream ClsCode | Data end | Days | Ann. vol. (%) | AC(1) | Announcements |
|---|---|---|---|---|---|---|---|---|---|
| Equity | ES | E-mini S&P 500 | CME | 1035 | 2026-04-03 | 5,356 | 18.6 | 0.70 | FOMC, CPI, jobs |
| Equity | NQ | E-mini Nasdaq-100 | CME | 238 | 2026-04-03 | 5,357 | 21.8 | 0.60 | FOMC, CPI, jobs |
| Rates | ZN | 10-year T-note | CBOT | 3896 | 2026-04-03 | 5,352 | 6.2 | 0.56 | FOMC, CPI, jobs |
| Rates | ZB | 30-year T-bond | CBOT | 2441 | 2026-04-03 | 5,352 | 11.0 | 0.52 | FOMC, CPI, jobs |
| Energy | CL | WTI crude oil | NYMEX | 1482 | 2026-04-03 | 5,348 | 46.0 | 0.66 | EIA |
| Energy | NG | Natural gas | NYMEX | 1539 | 2026-04-03 | 5,348 | 51.0 | 0.57 | EIA |
| Metals | GC | Gold | COMEX | 1508 | 2026-04-03 | 5,350 | 21.9 | 0.58 | FOMC, CPI, jobs |
| Metals | SI | Silver | COMEX | 1574 | 2026-04-03 | 5,348 | 39.8 | 0.60 | FOMC, CPI, jobs |
| Metals | HG | Copper | COMEX | 1512 | 2026-04-03 | 5,348 | 33.6 | 0.61 | FOMC, CPI, jobs |
| Agriculture | ZC | Corn | CBOT | 2420 | 2026-04-03 | 5,352 | 26.0 | 0.57 | WASDE, USDA |
| Agriculture | ZS | Soybeans | CBOT | 2522 | 2026-04-03 | 5,352 | 21.9 | 0.46 | WASDE, USDA |
| FX | 6E | Euro FX | CME | 1999 | 2026-04-03 | 5,359 | 9.5 | 0.57 | FOMC, CPI, jobs |
| Equity | FESX | Euro Stoxx 50 | Eurex | 600 | 2026-10-27 | 5,556 | 24.2 | 0.70 | FOMC, CPI, jobs |
| Equity | FDAX | DAX | Eurex | 581 | 2026-10-27 | 5,548 | 23.1 | 0.67 | FOMC, CPI, jobs |
| Equity | Z | FTSE 100 | ICE Europe | 1261 | 2026-10-27 | 5,513 | 20.1 | 0.71 | FOMC, CPI, jobs |
| Rates | FGBL | Euro-Bund | Eurex | 626 | 2026-10-27 | 5,556 | 6.5 | 0.60 | FOMC, CPI, jobs |
| Energy | BRN | Brent crude oil | ICE Europe | 1175 | 2026-10-27 | 5,626 | 36.9 | 0.66 | EIA |
| Energy | G | Gasoil | ICE Europe | 1176 | 2026-10-27 | 5,626 | 39.9 | 0.68 | EIA |
| Agriculture | SB | Sugar No. 11 | ICE US | 3299 | 2026-10-27 | 5,488 | 29.9 | 0.50 | WASDE, USDA |
| Agriculture | KC | Coffee C | ICE US | 3289 | 2026-10-27 | 5,488 | 31.9 | 0.38 | — |
| Agriculture | CT | Cotton No. 2 | ICE US | 1487 | 2026-10-27 | 5,489 | 25.2 | 0.51 | WASDE, USDA |

- **Data end:** the Datastream feed for the CME Group markets stops on 3 April 2026; the ICE and Eurex markets run to 27 October 2026.
- **Ann. vol.:** square root of 252 times the mean daily variance (Section 3).
- **AC(1):** first-order autocorrelation of daily log variance.
- **ZN, ZS and 6E** use Datastream's composite series.

## 3. Construction

### Front-contract series
- For each market and day *t*, the contract held is the one with the **largest open interest on day t − 1** among the **four nearest** contracts not yet past their last trading day.
- The series rolls only forward, never back to an earlier expiry, and the choice uses no day-*t* information.
- Every day-*t* quantity (return, range, overnight gap) comes from that one contract, so roll days carry no artificial jump.

### Cleaning
- Exact duplicate rows in the WRDS export (some rows appear up to six times) are removed.
- Exchange holidays on which Datastream carries the last price forward (open = high = low = close, zero return) are dropped as non-trading days.
- Open and settlement prices are clipped into the day's high–low range, since Datastream settlements can sit just outside it.

### Returns
- Daily log return within the held contract: *r_t* = ln(*S_t* / *S_{t−1}*), with both settlements from the same contract.
- These are excess returns: they include the contract's convergence to spot (carry) but no jump between contracts.
- The roll-free log price is the cumulative sum of these returns.
- *h*-day return: the change in the roll-free log price over the next *h* trading days.

### Volatility
- Daily variance = squared overnight gap + Garman–Klass (1980) estimator:
  *v_t* = ln(*O_t* / *C_{t−1}*)² + 0.5 ln(*H_t* / *L_t*)² − (2 ln 2 − 1) ln(*C_t* / *O_t*)²,
  with *C* the settlement price. On days without a high–low range the squared return is used.
- Forecast target: *y_t* = ln *v_t*, and the *h*-day target is the mean of *y* over days *t* + 1 to *t* + *h*, for *h* = 1, 5 and 22 trading days.
- The Rogers–Satchell (1991) estimator is computed as a robustness check.

### Calendar variables (known at the forecast origin)
- weekday of day *t* + 1;
- calendar days from *t* to *t* + 1 (weekends and holidays);
- roll day at *t* + 1;
- days to expiry of the held contract.

### Additional contract variables
Volume and open interest of the held contract and across contracts, and the basis between the next-deferred and the held contract (used by the gradient-boosting benchmark).

## 4. Scheduled announcements

Release dates of scheduled announcements, 2005–2026. Only **scheduled** events are used, because a covariate must be known in advance; unscheduled FOMC moves and conference calls are excluded.

| Announcement | Time | Source | Markets |
|---|---|---|---|
| FOMC statement (last day of each scheduled meeting) | 14:00 ET | Federal Reserve historical pages and meeting calendars | ES, NQ, ZN, ZB, 6E, FESX, FDAX, FGBL, Z, GC, SI, HG |
| Consumer Price Index | 08:30 ET | Bureau of Labor Statistics news-release archive | as FOMC |
| Employment Situation (jobs report) | 08:30 ET | Bureau of Labor Statistics news-release archive | as FOMC |
| EIA Weekly Petroleum Status Report | 10:30 ET (Wednesday; Thursday after a federal holiday) | U.S. Energy Information Administration | CL, BRN, G |
| EIA Weekly Natural Gas Storage Report | Thursday | U.S. Energy Information Administration | NG |
| World Agricultural Supply and Demand Estimates (WASDE) | 12:00 ET | USDA, ESMIS publication listings | ZC, ZS, CT, SB |
| Grain Stocks (quarterly) | 12:00 ET | USDA NASS, ESMIS | ZC, ZS |
| Prospective Plantings (March), Acreage (June) | 12:00 ET | USDA NASS, ESMIS | ZC, ZS, CT, SB |

- A release dated on a market's non-trading day is moved to that market's next trading day.
- European markets settle before the 14:00 ET FOMC statement, so its effect reaches them on the next trading day; the models therefore use both the indicator of the origin day and the share of release days in the forecast window.
- Actual rather than originally scheduled dates are used in the government-shutdown months; EIA dates before 2025 follow EIA's holiday rule.
- The compiled calendar is built from public sources, and the paper states that it is provided with the code.

## 5. Evaluation periods

| Period | Dates | Use |
|---|---|---|
| Estimation history | January 2005 onward | all models are refitted on data up to each forecast origin |
| Walk-forward | 1 January 2015 – 30 September 2025 (about 2,700 forecast days per market) | main results; monthly 21-day test blocks, training rows purged by horizon |
| Post-release | 1 October 2025 – data end (128–277 trading days per market) | after the release of the foundation models used; contamination and robustness check |
| Prospective test | after the current data end | specification frozen before the data exist |

## 6. Foundation models

TimesFM 3 (Google) and Chronos-2 (Amazon), publicly released pretrained checkpoints, applied zero-shot (no parameter estimated on these data) to the daily log variance with a 2,048-day context.

## 7. Obtaining the data

1. With a WRDS subscription that includes Datastream futures, export the contract-level daily file (prices and contract metadata) for the market classes (ClsCode) listed in Section 2, 2000–2026.
2. Rebuild the front-contract series, the variance series and the announcement indicators following Sections 3 and 4.

The code that performs these steps, estimates all models and produces every table will be released with the paper.

## License

This repository holds documentation only. The futures data are the property of Refinitiv (LSEG) and are available under licence through WRDS. The announcement dates are public information from the U.S. Federal Reserve, Bureau of Labor Statistics, Energy Information Administration and Department of Agriculture.
