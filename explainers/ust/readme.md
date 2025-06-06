<div align="right">
  <b>en</b> | <a href="readme_ru.md">ru</a>
</div>

# Universal Social Transaction (UST)

## Authorship:
Bastyon Development Team

## Introduction
Universal Social Transaction (UST) can help expand the capabilities of nodes in terms of storing and processing transactions, without a specific binding to content, it acts as a free "action" in the Bastyon network.

## Goals
List the goals that should be achieved within this document. Remember - this document is a kind of technical specification, so it should contain the most precise instructions and steps for implementing the project.

## Proposal

Such a transaction has a set of fields for storing metadata, some of which are indexed and can already be used for simple queries. For more complex queries, an additional processing server can be used, having the logic of parsing the blockchain and building its own database and API server.

It is also proposed to use a HEX value for OP_RETURN in the form of ust:<free identifier> - this will allow to distinguish transactions at the highest level (for example, you can use a mini-application identifier to quickly find your transactions within a specific application).

Metadata used in the transaction can be used at the discretion of the developer - for example, it can simply be JSON data or script code that needs to be executed on the client.

## Implementation
The first version will be implemented in PIP 115 (https://github.com/pocketnetteam/pocketnet.core/pull/880)
