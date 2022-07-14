# Leveraged Liquidity Mining Rewards Mechanism

The holder of sToken, i.e. the underwriter of Meta Defender, is entitled to the Leveraged Liquidity Mining rewards mentioned in[#adding-leverage-to-underwriting-capital](../project-architecture/underwriting-and-leveraged-mining.md#adding-leverage-to-underwriting-capital "mention").

In order to encourage underwriters to provide long-term, stable capital, sToken holders are required to hold their sTokens for a certain period of time (approximately 14 natural days, with minor variations depending on the speed of block creation on the mainnet) before they can participate in the withdrawal of this Rewards.

After withdrawing from the Capital Reserve and destroying the _sToken_, the holder will lose the eligibility to withdraw the proceeds.

The mining proceeds will be hosted separately by Meta Defender's governance contract and will be claimed from the pool for _sToken_ holders to withdraw at a fixed period and rate.
