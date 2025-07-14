<div align="right">
  <b>en</b> | <a href="readme_ru.md">ru</a>
</div>

# Automated Badge Assignment Mechanism via Blockchain

## Authorship:
Bastyon Development Team

## Introduction
It is proposed to introduce additional Badges - Verified User, Validator for the "Family" mini-application, Verified User of the "Family" mini-application.

## Goals
- Ensure badge assignment/revocation without code interventions, based on transactions.

## Proposal

To implement the functionality, it is proposed to use monetary transactions without metadata (Payload), with useful information recorded in the `scrippubkey` of the zero output after the OP_RETURN instruction.
Within the framework of PIP 115, the following valid values are introduced, any other values are currently ignored:

- `a:b:vf <address>` - Badge `verificated` assignment
- `a:u:vf <address>` - Badge `verificated` revocation
- `a:b:vl <address>` - Badge `validator` assignment
- `a:u:vl <address>` - Badge `validator` revocation
- `a:b:zn <address>` - Badge `verificated_zn` assignment
- `a:u:zn <address>` - Badge `verificated_zn` revocation

### Restrictions:

- To assign/revoke the `verificated` badge, the `developer` badge is required
- To assign/revoke the `validator` badge, the `developer` badge is required
- To assign/revoke the `verificated_zn` badge, the `developer` or `validator` badge is required
- Transaction fee is standard for monetary transactions.

## Implementation

The first version will be implemented in PIP 115 [PR #880](https://github.com/pocketnetteam/pocketnet.core/pull/880)

## Technical Details

Transaction validity verification will be implemented based on the current badge assignment mechanism in `ChainPostTransaction.cpp::IndexBadges`.
The functionality is implemented as a "soft fork" principle, i.e., it does not affect consensus. Badge management conditions are verified based on the `Main::Badges` table and the `PocketnetHelper.h::PocketnetDevelopers` array.
