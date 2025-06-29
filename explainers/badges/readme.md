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

To implement the functionality, it is proposed to use the OP_RETURN field with the following values:
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

## Implementation

The first version will be implemented in PIP 116 (?)

## Technical Details
