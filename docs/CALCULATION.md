# Calculation

For a target quantity, the calculator walks the Yes and No ask books from lowest price upward. Each side gets an independently executable quantity. Settlement uses only the minimum of those quantities and the target.

```text
executable = min(yes executable, no executable, target)
settlement = executable × 1 USDC
gross      = settlement - yes cost - no cost
net        = gross - taker fees - slippage buffer - safety buffer
net ROI    = net / total cost
```

Every monetary, quantity, price, fee, and ratio value is a Python `Decimal`. Duplicate price levels are merged, invalid/negative/non-finite values are dropped, bids are sorted descending, and asks ascending. A partial result is explicit and can be rejected by configuration. Empty asks, stale quotes, unknown fees, closed markets, and insufficient depth cannot become valid opportunities.

The current official fee function is evaluated separately for the Yes and No legs and rounded to five decimal places. Display rounding never feeds back into calculations.
