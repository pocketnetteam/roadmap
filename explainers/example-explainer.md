# Title: Cross-Chain Bridge Implementation for Bastyon Network

## Author(s):
- Lead Author: Technical Architect
- Contributors: Blockchain Team

## Status of this Document

This document serves as an entry point for engaging the Bastyon community and individuals passionate about democratizing global communications. It aims to develop collaborative solutions aligned with the decentralization paradigm. As solutions evolve, this document will be maintained as an archive and updated to reflect the current implementation status and location of ongoing work.

<!--
Status Options:
- ACTIVE
- ARCHIVED
- WITHDRAWN
-->
Current Status: ACTIVE

<!--
Example:
Current venue: https://github.com/pocketnetteam/pocketnet.gui
Current venue: https://github.com/pocketnetteam/pocketnet.core
-->
Current venue: https://github.com/pocketnetteam/pocketnet.core

<!--
Example: For ungraduated explainers, reference this document.
For graduated proposals, reference the Product/Functional Spec in the implementation repository.
-->
Current version: explainer-cross-chain-bridge-v1.md

<!--
Optional: Specify if there's a reward for solving this problem
Example: 
Bounty Offered: 1,000,000 PKOIN
-->
Bounty Offered: 500,000 PKOIN

## Introduction

Bastyon's blockchain currently operates as an independent network, limiting its interoperability with other blockchain ecosystems. This proposal introduces a cross-chain bridge solution to enable seamless asset transfers and data communication between Bastyon and other major blockchain networks, particularly Ethereum and Binance Smart Chain (BSC).

The cross-chain bridge will enhance Bastyon's utility by allowing users to leverage their PKOIN tokens across multiple blockchain ecosystems while maintaining the network's core principles of decentralization and censorship resistance.

## Goals

1. Enable bi-directional transfer of PKOIN tokens between Bastyon and target blockchains (Ethereum, BSC)
2. Maintain decentralization by implementing a federated bridge architecture
3. Ensure secure and verifiable cross-chain transactions
4. Minimize transaction confirmation times while maintaining security
5. Provide a user-friendly interface for cross-chain operations
6. Enable smart contract interoperability for future DApp development

## Non Goals

1. Supporting every available blockchain network
2. Implementing direct fiat currency gateways
3. Creating a centralized exchange service
4. Modifying the core consensus mechanism of Bastyon
5. Supporting non-fungible token (NFT) transfers in the initial implementation

## Proposal

The cross-chain bridge implementation will utilize a federated validator system with the following components:

1. **Bridge Smart Contracts:**
   - Deploy bridge contracts on target chains (Ethereum, BSC)
   - Implement locked liquidity pools for token backing
   - Create wrapper token contracts (wPKOIN) on target chains

2. **Validator Network:**
   - Establish a network of trusted validators
   - Implement multi-signature requirements for bridge operations
   - Deploy monitoring and security measures

3. **Bridge Protocol:**
   - Define standard message format for cross-chain communications
   - Implement verification and confirmation mechanisms
   - Create fallback and recovery procedures

4. **Integration Layer:**
   - Develop APIs for wallet integration
   - Create monitoring and analytics tools
   - Implement rate limiting and security measures

## Code/Pseudocode

```solidity
// Bridge Contract Example (Ethereum Side)
contract BastyonBridge {
    mapping(bytes32 => bool) public processedTransactions;
    address public immutable wPKOIN;
    uint256 public required_signatures;
    
    mapping(address => bool) public validators;
    
    event Bridge(
        address indexed from,
        string bastyon_address,
        uint256 amount,
        bytes32 transaction_id
    );
    
    function bridgeToBastyon(
        string memory bastyon_address,
        uint256 amount
    ) external {
        require(IERC20(wPKOIN).transferFrom(msg.sender, address(this), amount));
        bytes32 txId = keccak256(
            abi.encodePacked(msg.sender, bastyon_address, amount, block.number)
        );
        emit Bridge(msg.sender, bastyon_address, amount, txId);
    }
    
    // Additional functions for validator management and transaction processing
}
```

## Examples

1. **PKOIN to Ethereum Transfer:**
```
User initiates transfer of 1000 PKOIN to Ethereum
→ PKOIN locked in Bastyon bridge contract
→ Validators confirm transaction
→ wPKOIN minted on Ethereum
→ User receives wPKOIN in their Ethereum wallet
```

2. **Ethereum to PKOIN Transfer:**
```
User burns wPKOIN on Ethereum
→ Bridge contract emits event
→ Validators confirm burn
→ PKOIN released on Bastyon network
→ User receives PKOIN in their Bastyon wallet
```

## Alternatives Considered

1. **Atomic Swaps:**
   - Pros: Truly trustless
   - Cons: Complex UX, longer confirmation times
   
2. **Centralized Bridge:**
   - Pros: Simpler implementation, faster
   - Cons: Single point of failure, trust requirements

3. **Layer-2 Solution:**
   - Pros: Faster transactions, lower fees
   - Cons: Additional complexity, separate security considerations

## Considerations

### Privacy
- Bridge transactions will be publicly visible on both chains
- User addresses will be linked across chains
- Implement optional privacy features in future iterations

### Security
- Multi-signature requirement for validator operations
- Time-locks on large transfers
- Regular security audits
- Emergency shutdown mechanism
- Validator stake requirements

### Performance
- Target confirmation time: < 5 minutes
- Gas optimization for bridge contracts
- Batching capabilities for validator operations
- Scalability considerations for validator set

## Questions

### Open Questions

1. What should be the initial validator set size?
2. How to handle validator rewards and incentives?
3. What should be the minimum stake requirement for validators?
4. How to handle network upgrades on either chain?

### Answered Questions

1. **Q: Should we support other tokens initially?**  
   A: No, focus on PKOIN first to ensure security and stability.

2. **Q: What's the minimum transfer amount?**  
   A: 100 PKOIN to ensure economic viability considering gas costs.

3. **Q: How many confirmations before finalizing transfers?**  
   A: 12 blocks on Bastyon, 20 blocks on Ethereum, 50 blocks on BSC.