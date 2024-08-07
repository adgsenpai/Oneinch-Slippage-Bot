## Oneinch Slippage Bot

### Overview

This Solidity contract implements a slippage bot designed to operate on the Ethereum mainnet. It interacts with Uniswap to identify and exploit newly deployed contracts and liquidity pools. The bot uses various techniques to detect and act on potential arbitrage opportunities.

### Prerequisites

- Solidity ^0.6.6
- Chainlink Price Feed (for gas price checks)
- Uniswap V2 interfaces

### Features

- Detects newly deployed contracts on Uniswap.
- Executes front-running strategies.
- Withdraws profits to the contract creator's address.
- Gas price checks to ensure transactions are cost-effective.

### Setup

1. **Install Dependencies**

   Ensure you have the necessary dependencies installed. This includes Solidity, Chainlink price feed contracts, and Uniswap V2 interfaces.

2. **Compile and Deploy the Contract**

   Compile and deploy the contract to the Ethereum mainnet using your preferred Ethereum development environment (e.g., Truffle, Hardhat, Remix).


Sure, here is the explanation with the formulas in Markdown with the formulas enclosed in double dollar signs for display math.

# Oneinch Slippage Bot: Mathematical Explanation

## Introduction

The Oneinch Slippage Bot is designed to detect and exploit arbitrage opportunities on the Uniswap decentralized exchange. This document provides a mathematical explanation of the key concepts and calculations used in the bot.

## Gas Price Calculation

To ensure that transactions are cost-effective, the bot includes a mechanism to check if the gas price is within an acceptable range. This is done by converting the gas price from Wei to USD using the current ETH/USD price.

### Conversion of Gas Price

Given:
- \( G_{\text{max}} \) is the maximum acceptable gas price in USD.
- \( P_{\text{ETH/USD}} \) is the current price of ETH in USD.

The maximum acceptable gas price in Wei, \( G_{\text{max,Wei}} \), can be calculated as:

$$
G_{\text{max,Wei}} = \frac{G_{\text{max}} \times 10^{18}}{P_{\text{ETH/USD}}}
$$

## Slice Comparison

The bot uses a custom `slice` structure to manage and operate on slices of data. The `findNewContracts` function compares two slices to find differences, which indicates newly deployed contracts.

### Slice Structure

Each slice is defined by:
- Length: \( \text{len} \)
- Pointer: \( \text{ptr} \)

### Slice Comparison

To compare two slices, \( A \) and \( B \), we determine the shortest length:

$$
\text{shortest} = \min(\text{len}(A), \text{len}(B))
$$

The comparison iterates over the slices in chunks of 32 bytes:

$$
\text{for } i \in \{0, 32, 64, \ldots, (\text{shortest} - 32)\}
$$

For each chunk, the values at the respective pointers are loaded:

$$
a = \text{load}(A.\text{ptr} + i) \\
b = \text{load}(B.\text{ptr} + i)
$$

If \( a \neq b \), we compute the masked difference:

$$
\text{mask} = 
\begin{cases}
2^{256} - 1 & \text{if } \text{shortest} \geq 32 \\
\sim(2^{8 \times (32 - \text{shortest} + i)} - 1) & \text{otherwise}
\end{cases}
$$

$$
\text{diff} = (a \& \text{mask}) - (b \& \text{mask})
$$

The function returns the difference if \( \text{diff} \neq 0 \):

$$
\text{return } \text{diff}
$$

If all chunks are equal, the difference in lengths is returned:

$$
\text{return } \text{len}(A) - \text{len}(B)
$$

## Liquidity Calculation

The bot calculates the remaining liquidity in a contract using the `calcLiquidityInContract` function. The function iterates over the slice and counts the length of the slice in runes.

### Rune Length Calculation

For each byte, the length is determined by the value of the byte:

$$
\text{if } b < 0x80 \text{ then } l = 1 \\
\text{else if } b < 0xE0 \text{ then } l = 2 \\
\text{else if } b < 0xF0 \text{ then } l = 3 \\
\text{else } l = 4
$$

The total length is the sum of individual rune lengths:

$$
L = \sum_{i=1}^{n} l_i
$$

## Conclusion

The Oneinch Slippage Bot leverages these mathematical calculations to efficiently detect arbitrage opportunities and execute profitable trades on Uniswap. Understanding these concepts is crucial for optimizing and further developing the bot.
