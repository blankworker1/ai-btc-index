BTC-settled compute gateway / aggregator (fastest MVP)
Agents send an x402-style request (HTTP 402 Payment Required). You accept Lightning BTC (or BTC on a Bitcoin L2), convert/route to the best available capacity from existing providers (Akash, Render, neoclouds, or direct GPU hosts), meter usage, and return the result with a cryptographic receipt.

Differentiator: Bitcoin-native settlement + routing + basic verification instead of forcing agents onto pure stablecoin rails.
Tech: x402 (now with Lightning support from Block), GOAT Network AgentKit or similar for Bitcoin-secured wallets/identity, simple backend that talks to provider APIs, metering + escrow.
Revenue: spread on FX/conversion, small take-rate, premium for guaranteed SLAs.
Why it works now: x402 already handles agent micropayments; Lightning makes sub-cent BTC practical; supply exists off-the-shelf.
