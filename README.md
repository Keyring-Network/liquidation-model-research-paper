# The Changing Role of the DeFi Liquidator and Implications for Protocol Design

LaTeX source for a working paper on liquidating redemption-gated tokenised collateral in on-chain lending markets.

## What this paper is about

Tokenised fund shares are entering DeFi lending as collateral, and their exit is not atomic. Converting a share to cash runs through the issuer's redemption process, a notice period plus a settlement queue; the motivating case is a 30-day notice period followed by a 60-day queue. Existing liquidation designs assume the liquidator seizes collateral, sells it, and repays the debt in one transaction, so the liquidator carries no market or credit risk and the bonus only has to cover slippage and gas.

When the holding period is positive the liquidator's role changes. Taking delivery means writing a bridge loan against an illiquid asset, holding market risk on its true value and credit risk on the redemption counterparty for the whole cycle. The required premium becomes a forward quantity containing funding cost net of yield, a `kappa * sigma * sqrt(T)` term for volatility over the holding period, fees, and the probability-weighted cost of the gate being extended or suspended. The discount on offer is `min(1 - H, maxDiscount)`, linear in the health factor and capped, and it cannot pay that premium: a position at `H = 0.99` offers at most a 1% discount while the collateral's three-month 99% expected shortfall is far larger. The queue also lengthens in exactly the stress that triggers liquidations, so the premium peaks when liquidator capital is scarcest.

The result is a position that is liquidatable in name and unliquidatable in fact for the entire redemption cycle, with lenders carrying the tail. This paper works out what mechanisms can close that gap without asking the issuer to coordinate, without touching the oracle provider, without fixed rates, and without standing idle capital.

## Structure

- **Section 1, How traditional finance clears and liquidates.** Central clearing and its regulation (waterfall, MPOR, Cover 2, EMIR and CDR 153/2013 margin floors), clearing-house episodes from Caisse de Liquidation to Lehman and Nasdaq 2018, bilateral haircut practice including Gorton-Metrick repo haircuts and Archegos, financing of gated assets under NAV-lending terms, levered credit and its settlement lags, how large liquidations were actually executed (LTCM, Amaranth, Lehman, MF Global, Metallgesellschaft, Archegos), and the DeFi record (Black Thursday, Solend, the Aave CRV hole, DeFi United).
- **Section 2, The problem in DeFi.** How on-chain liquidation works today across Euler, Aave v3 and v4, and Morpho Blue; why it breaks for redemption-gated collateral; the conservation bound; the constraint set; and the venue features the models assume.
- **Section 3, Method.** Two rounds of proposal generation across five design lenses with independent adversarial review on five failure axes, the second round run under the full constraint set.
- **Section 4, Models in order of preference.** The ranked mechanisms, each with its family, the liquidator balance sheet it demands, and its failure modes.
- **Section 5, Protocol adaptability to liquidation.** What each venue can host: Aave v3 and v4, Euler v2, Kamino, Morpho Blue and the Midnight line, and third-party adapter products.
- **Section 6, Composite architecture.** How the models compose into one rule-driven state machine and the order in which a venue should build them.
- **Appendix.** Twenty-one invalidated proposals with the constraint that killed each.

## Main results

Nine paths are ranked by deployability, liquidator balance sheet, trust surface, and how much of the problem each clears on its own:

| Rank | Model | Family | Liquidator capital |
| --- | --- | --- | --- |
| 1 | Bounded oracle repricing | Oracle | The full debt |
| 2 | Direct protocol redemption | Queue | None |
| 3 | Staged redemption | Queue | None |
| 4 | Routed auction machine | Auction | Debt or excess |
| 5 | Two-queue netting | Liability | None |
| 6 | Assumable note novation | Liability | Collateral minus debt |
| 7 | Windowed claim batches | Auction | One window |
| 8 | Borrower premium escrow | Funding | Reduced |
| 9 | Recovery claim accounting | Loss | None |

Key quantitative results:

- **Feasibility bound.** The largest discount any parameter scheme can deliver satisfies `x* <= 1 - LTV_true`. The discount comes out of the borrower's own collateral, so a solvent position cannot fund a premium larger than its collateral's excess over its debt. Liquidation LTVs must therefore be set at origination with the full forward premium inside that excess.
- **Repricing multiplier.** Where the discount tracks health, as on Euler and Aave v4, the price enters the liquidation twice, once through the discount factor and once through the token conversion, so a multiplier `k` delivers realised proceeds of `1 / (k^2 * H)` per unit of debt repaid and a target discount `x` requires `k = sqrt((1 - x) / H)`. The naive linear guess roughly doubles the intended payout near threshold. Where the bonus is fixed per market, as on Aave v3 and Morpho Blue, the same target needs `k = (1 - x)(1 + b)`. The adapter must be bounded, single use, and scoped to the position being liquidated.
- **Novation capital saving.** Minting each position as a transferable note that owns the collateral lock and owes the debt lets a bidder buy the position rather than the collateral, cutting the capital demanded from the full collateral value to its excess over the debt, roughly 3.3 times less at 70% LTV.
- **Protocol fit.** Euler v2 and Aave v4 are the most suitable venues. Euler v2's vault kit accepts custom collateral adapters; Aave v4's hub-and-spoke design admits a new spoke without migrating liquidity, and its liquidation engine already runs a health-linear bonus and partial repayment to a target health factor.

Where no liquidator appears, the protocol can redeem the seized collateral itself through the issuer using Keyring's [un]wind adapter, submitting the asset via `requestRedeem` on the ERC-7540 interface and paying lenders from settlement. That path needs no bidder, no discount, and no balance sheet, and it is the reserve under every other mechanism.

## Authors

A. McFarlane, S. Gujrati, J. Zierold, Keyring.Network Ltd.

## Building the PDF

The paper compiles with `pdflatex`, run twice so the table of contents and cross-references resolve:

```
pdflatex -interaction=nonstopmode clearing-models.tex
pdflatex -interaction=nonstopmode clearing-models.tex
```

`vault-flow.png` must sit alongside `clearing-models.tex`; the figure in Section 1 includes it directly. Its source is `vault-flow-diagram.tex`, a standalone TikZ document that compiles on its own and is rasterised to the PNG; edit the diagram there rather than the image.

`clearing-models.pdf` in this repository is built by GitHub Actions on every merge to `master`. Do not commit the PDF.

## Contributing

This repository is public and `master` is protected. Contributions go through a fork or a branch plus a pull request. A pull request must leave the LaTeX compiling; check locally with the two `pdflatex` runs above before opening it.

## Licence and citation

This is a working paper by Keyring Network. Cite it as: A. McFarlane, S. Gujrati, and J. Zierold, "The Changing Role of the DeFi Liquidator and Implications for Protocol Design," Keyring.Network Ltd., working paper, 13 August 2026.
