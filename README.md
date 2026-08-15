# The Changing Role of the DeFi Liquidator and Implications for Protocol Design

A working paper by A. McFarlane, S. Gujrati and J. Zierold, Keyring.Network Ltd.

Tokenised fund shares are entering DeFi lending as collateral, and their exit is not atomic: converting a share to cash runs through the issuer's redemption process, a notice period followed by a settlement queue. On-chain liquidation assumes the opposite. The liquidator is expected to seize collateral, sell it, and repay the debt in one transaction, so the bonus only has to cover slippage and gas. When the collateral takes months to redeem, the liquidator is instead writing a bridge loan against an illiquid asset and carrying market and credit risk for the whole cycle, and the capped discount on offer cannot pay for it. The position is liquidatable in name and unliquidatable in fact, with lenders carrying the tail.

The paper reviews how traditional finance clears and liquidates gated and illiquid positions, sets out the constraint set that any on-chain answer has to satisfy, ranks nine candidate mechanisms against it, and assesses which lending venues can host them.

## Contents

- `clearing-models.tex` — the paper.
- `vault-flow-diagram.tex` — the TikZ figure, embedded by the paper and also compilable on its own.
- `clearing-models.pdf` — the current build.

## Building

```
pdflatex -interaction=nonstopmode clearing-models.tex
pdflatex -interaction=nonstopmode clearing-models.tex
```

Run it twice so the table of contents and cross-references resolve, and commit the rebuilt `clearing-models.pdf` alongside any change to the source.

## Contributing

`master` is protected. Contributions go through a fork or a branch plus a pull request, which needs one approving review to merge. Changes under `.github/`, to `.gitignore`, or to `clearing-models.pdf` also need review from the code owners in `.github/CODEOWNERS`.

## Citation

A. McFarlane, S. Gujrati, and J. Zierold, "The Changing Role of the DeFi Liquidator and Implications for Protocol Design," Keyring.Network Ltd., working paper, 2026.
