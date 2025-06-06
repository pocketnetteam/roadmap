<div align="right">
  <b>en</b> | <a href="readme_ru.md">ru</a>
</div>

# Communities (Groups)

## Authorship:
Bastyon Development Team

## Introduction
The "Communities" project is a comprehensive solution for creating and managing decentralized communities in the Bastyon ecosystem. It is proposed to build community functionality based on existing mechanics with minimal modifications.

## Goals
1. Creating a full-fledged infrastructure for community functioning and restricting access to communities.
2. Implementing a consensus-based moderation system.
3. Ensuring efficient storage and processing of community data.
4. Integration with existing blockchain infrastructure.
5. Providing a user-friendly interface.

## Proposal

1. A user account can be converted into a community account by adding an additional field to the JSON profile metadata (or profile settings), which will not affect the mandatory part of the data during transaction processing, but will allow nodes to allocate such accounts to a separate table for indexing communities and further linking participants.
2. An application to join a community = subscription to a community account. Reverse subscription - approval of entry. Unsubscribing - exclusion from the community. Mutual subscription = confirmed membership.
3. Based on the new table and the presence of subscriptions, we can start filtering incoming transactions in the node:
   - For immediate launch, it is allowed to restrict user actions without community membership only in the interface and in updated nodes, when accepting transactions into the mempool (but ignoring new rules for already formed blocks).
   - A block number is assigned for the soft fork, starting from which, new rules for processing transactions taking into account community functionality come into full force for any cases.
4. Publication of posts to a community is implemented by an additional setting in the content transaction (post, video, etc.) - an additional table for indexing the "community - content" relationship.
5. Community feed based on posts marked with the community (instead of the author's feed).
6. Appointing multiple moderators for a community is a complex task that is supposed to be solved separately.

