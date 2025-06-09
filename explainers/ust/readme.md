<div align="right">
  <b>en</b> | <a href="readme_ru.md">ru</a>
</div>

# Universal Social Transaction (UST)

## Authorship:
Bastyon Development Team

## Introduction
Universal Social Transaction (UST) can help expand the capabilities of nodes in terms of storing and processing transactions, without a specific binding to content, it acts as a free "action" in the Bastyon network.

## Goals
- A flexible tool for implementing multiple mechanisms not described in the node consensus.

## Proposal

Such a transaction has a set of fields for storing metadata, some of which are indexed and can already be used for simple queries. For more complex queries, an additional processing server can be used, having the logic of parsing the blockchain and building its own database and API server.

It is also proposed to use a HEX value for OP_RETURN in the form of ust:<free identifier> - this will allow to distinguish transactions at the highest level (for example, you can use a mini-application identifier to quickly find your transactions within a specific application).

Metadata used in the transaction can be used at the discretion of the developer - for example, it can simply be JSON data or script code that needs to be executed on the client.

## Implementation

The first version will be implemented in PIP 115 (https://github.com/pocketnetteam/pocketnet.core/pull/880)

Main development of version 1 (https://github.com/pocketnetteam/pocketnet.core/pull/882)

## Technical Details

The UST transaction is based on the `src/pocketdb/models/base/SocialTransaction.h` model, which previously was just a basic layer for private cases and implemented the basic functionality of any social DTOs. Since it is most suitable for the role of UST by meaning, the transaction type `TxType::SOCIAL = 10` was added and the consensus class `src/pocketdb/consensus/UST.hpp` was implemented.

Recognition of the transaction type is implemented based on the OP_RETURN message of the zero output:
```
tx.outs[0].scriptpubkey.hex.substr(0, 8) == "7573743a"

hex.encode("ust:") -> "7573743a"

ust:example01   - true
ust:example:01  - true
ust:example 01  - true (01 will be ignored in identifier determination)
ust_example     - false
ust example     - false
```

The consensus proposes a minimal set of conditions:
- Mandatory presence of address in `s1`
- Mandatory registration of address from `s1` as an account
- Total size of metadata (payload) - 120000 bytes
- Fee is calculated from the transaction size (including metadata)
