<div align="right">
  <b>en</b> | <a href="readme.md">ru</a>
</div>

# Automated Badge Assignment Mechanism via Blockchain

## Authorship:
Bastyon Development Team

## Introduction
It is proposed to introduce additional Badges - Verified User, Mentor for the "Dating" mini-application (working title), Verified User of the "Dating" mini-application (working title).

## Goals
- Ensure badge assignment/revocation without code interventions, based on transactions.

## Proposal

To implement the functionality, it is proposed to use the OP_RETURN field with the following values:
- `a:b:ve <address>` - Badge `verificated` assignment
- `a:u:ve <address>` - Badge `verificated` revocation
- `a:b:mn <address>` - Badge `mentor` assignment
- `a:u:mn <address>` - Badge `mentor` revocation
- `a:b:zn <address>` - Badge `verificated_zn` assignment
- `a:u:zn <address>` - Badge `verificated_zn` revocation

### Restrictions:

- To assign/revoke the `verificated` badge, the `developer` badge is required
- To assign/revoke the `mentor` badge, the `developer` badge is required
- To assign/revoke the `verificated_zn` badge, the `developer` or `mentor` badge is required

## Implementation

The first version will be implemented in PIP 116 (?)

## Technical Details
