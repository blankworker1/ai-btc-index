# ai-btc-x — Specification

**Version:** 0.1 (draft)
**Status:** theoretical model; no measured data yet

## 1. Purpose

To show that AI inference can be priced in bitcoin without passing through a fiat price, by using energy as the shared denominator.

A curtailable miner converts a joule into sats at a rate fixed by the Bitcoin network. A GPU converts a joule into tokens at a rate fixed by its hardware and model. Dividing one by the other gives an exchange rate between sats and tokens.

## 2. Scope

**In scope**

- A definition of the exchange rate and its units.
- Reference conditions that make two measurements comparable.
- A measurement procedure that one site with one miner, one GPU and one energy meter can carry out.

**Out of scope**

- A trading venue, payment gateway or token.
- Scaling, routing or reselling third-party compute.
- Verifying that inference was performed correctly (see §8).

## 3. Units

| Symbol | Meaning | Unit |
|---|---|---|
| `E_h` | Miner efficiency | J/TH |
| `E_t` | Inference efficiency | J/kTok (joules per 1,000 output tokens) |
| `R` | Mean block reward, subsidy plus fees | sats/block |
| `D` | Network difficulty | dimensionless |
| `S_J` | Hashing yield | sats/J |
| `P` | Parity price of inference | sats/MTok (per million output tokens) |

The joule is the base unit. Sats per kWh (`S_J × 3.6 × 10⁶`) is the display unit.

## 4. The model

Expected work per block, in terahashes:

```
W = D × 2³² / 10¹²
```

Hashing yield, in sats per joule:

```
S_J = R / (W × E_h)
```

Parity price, in sats per million output tokens:

```
P = S_J × E_t × 1000
```

`P` is the price at which a joule earns the same whether it hashes or infers. Below `P`, the miner is the better use of surplus energy; above it, inference is.

`R` and `D` are taken as the mean over one difficulty epoch (2,016 blocks), so the rate updates once per epoch and needs no price oracle.

## 5. Reference conditions

Two results are comparable only if every field below matches.

**Inference**

| Field | Value |
|---|---|
| Model | _TBD_ (open weights) |
| Weights file hash (SHA-256) | _TBD_ |
| Quantisation | _TBD_ |
| Runtime and version | _TBD_ |
| Prompt set | _TBD_ (fixed file, hash recorded) |
| Output length per prompt | _TBD_ tokens |
| Temperature | 0 |
| Concurrency | 1 (single stream) |
| Hardware | _TBD_ |

**Mining**

| Field | Value |
|---|---|
| Miner model | _TBD_ |
| Firmware and power setting | _TBD_ |
| Hashrate basis | Pool-accepted, not nameplate |

**Both**

- Energy is measured at the wall (AC), for the whole machine.
- Ambient temperature is recorded.

## 6. Measurement procedure

**Inference**

1. Record idle power for 10 minutes with the model loaded.
2. Run the prompt set in a loop for at least 30 minutes.
3. Record total energy `J_total` and total output tokens `N`.
4. Report two figures:
   - **Total:** `E_t = J_total / (N / 1000)`
   - **Marginal:** the same, with idle energy for the run period subtracted.

**Mining**

1. Run at the stated power setting for at least 24 hours.
2. Record total energy and mean pool-accepted hashrate.
3. Report `E_h` as joules divided by terahashes accepted.

Any figure taken from a datasheet instead of a meter is labelled *nameplate*.

## 7. Published series

| # | Series | Unit | Source |
|---|---|---|---|
| 1 | Hashing yield, by miner | sats/kWh | On-chain data and `E_h` |
| 2 | Inference efficiency, by hardware and model | J/kTok | Measured |
| 3 | Parity price | sats/MTok | Series 1 × series 2 |
| 4 | Market price of the same model | sats/MTok | Public API prices; a fiat cross, labelled as such |
| 5 | Inference premium | ratio | Series 4 ÷ series 3 |

Series 1–3 are the model. Series 4–5 are context and are the only ones that depend on a fiat price.

## 8. Worked example

Illustrative only. The inference figure is an estimate, not a measurement.

| Input | Value |
|---|---|
| Network hashrate | ~1.16 ZH/s (`W` ≈ 6.96 × 10¹¹ TH per block) |
| `R` | ~3.15 × 10⁸ sats |
| `E_h` | 23 J/TH |
| `E_t` | 7,200 J/kTok (≈ 0.5 MTok per kWh) |

```
S_J = 3.15×10⁸ / (6.96×10¹¹ × 23) ≈ 1.97×10⁻⁵ sats/J   (≈ 71 sats/kWh)
P   = 1.97×10⁻⁵ × 7200 × 1000    ≈ 140 sats/MTok
```

## 9. Known limits

- **Energy is not the whole cost.** Hardware depreciation dominates the cost of inference. `P` is an energy-parity floor, not a full cost or a market price.
- **Tokens are graded, not fungible.** A token from one model is not a token from another, and tokenisers differ. Every figure is quoted per named model; no quality adjustment is attempted.
- **Hashes self-verify; tokens do not.** Mining is standardised by protocol, inference only by the conventions in §5. This asymmetry is a finding of the model, not a defect to be engineered away here.
- **Demand is not modelled.** Mining always has a buyer. Inference has value only when a job is waiting.
- **The reference model will age.** See §10.

## 10. Versioning

The reference conditions are versioned (`ref-1`, `ref-2`, …). When the reference model is replaced, both versions are measured side by side for at least one difficulty epoch so the series can be linked.

## 11. Next steps

1. Fill in §5.
2. Take one measurement of `E_t` and one of `E_h` on the same site.
3. Publish series 1–3 for that single data point.
