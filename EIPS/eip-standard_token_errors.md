---
eip: [assignedEIPNumber]
title: Standard Token Errors
description: List of Solidity custom errors covering standard token implementations (EIP-20, EIP-721 and EIP-1155)
author: Francisco Giordano (@frangio), Ernesto García (@ernestognw)
discussions-to: <URL>
status: Draft
type: Standards Track
category: ERC
requires: 20, 721, 1155
created: <date created on, in ISO 8601 (yyyy-mm-dd) format>
---

## Abstract

The following specification allows for the use of a standard list of Solidity [custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) to be used within EIP-20, EIP-721 and EIP-1155 tokens.

Ethereum applications (DApps) and wallets have historically used [revert statements](https://docs.soliditylang.org/en/v0.8.16/control-structures.html#revert-statement) to show relevant failure data to the users, however, this EIP provides a standard list of errors designed to give at least the same relevant information, but in a structured and expected way.

## Motivation

Since the introduction of Solidity custom errors in [v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4), these have provided a way to show failures in a more expresive way with dynamic arguments, while reducing deployment costs.

At the moment of the release of custom errors, the standard tokens (EIP-20, EIP-721, EIP-1155) were already in finalized state, so no error specification was included.

An error specification will allow users to expect more consistent error messages across applications or testing environments, while exposing pertinent arguments and overall reducing the need of writing expensive revert strings in the deployment bytecode.

The list of token standard errors is defined in the specification below.

## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

The provided interfaces with errors SHOULD conform with the [error grammar rules](#error-grammar-rules) described in the [rationale](#rationale) section.

It's important to note that errors MAY be thrown in each scenario, but that's up to the implementers, unless a revert reason is specified in their corresponding EIPs.

This EIP aims to provide a clear standard only in case an error will be thrown.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

/// @title Standard ERC20 Errors
/// @dev See https://eips.ethereum.org/EIPS/eip-20
///  https://eips.ethereum.org/EIPS/eip-[assignedEIPNumber]
interface ERC20Errors {
    /// @notice Indicates a failure with the token sender.
    /// @param _sender The address of whose tokens are transferred from.
    /// @dev Throw when the cause of failure is the sender in a transfer.
    ///  MUST NOT be thrown for approval operations.
    ///  MUST NOT be thrown for balance or allowance requirements.
    ///     Use `ERC20InsufficientBalance` or `ERC20InsufficientAllowance` instead.
    ///  MUST be thrown for unintended transfers from the zero address.
    error ERC20InvalidSender(address _sender);

    /// @notice Indicates a failure with the token receiver.
    /// @param _receiver The address of whose tokens are transferred to.
    /// @dev Throw when the cause of failure is the receiver in a transfer.
    ///  MUST NOT be thrown for approval operations.
    ///  MUST be thrown for unintended transfers to the zero address.
    ///  MUST be thrown for unintended transfers to non-compatible addresses (eg. contract addresses).
    error ERC20InvalidReceiver(address _receiver);

    /// @notice Indicates an error related to the current `_balance` of a sender in a transfer.
    /// @param _sender The address of whose tokens are transferred from.
    /// @param _balance The current amount of token the sender has.
    /// @param _needed The amount of token the sender needs to perform the operation.
    /// @dev Throw when the cause of failure is the balance of a sender.
    ///  MUST NOT be thrown if `_balance` is equal or greater than `_needed`.
    ///  MUST be thrown when `_balance` is less than `_needed`.
    error ERC20InsufficientBalance(
        address _sender,
        uint256 _balance,
        uint256 _needed
    );

    /// @notice Indicates a failure with the `_approver` of a token to be approved.
    /// @param _approver The owner's of the tokens to be approved.
    /// @dev Throw when the cause of failure is the owner of the tokens to be approved.
    ///  MUST NOT be thrown for transfer operations.
    ///  MUST be thrown for unintended approvals from the zero address.
    error ERC20InvalidApprover(address _approver);

    /// @notice Indicates a failure with the `_spender` to be approved.
    /// @param _spender The address of who's getting owner's approval.
    /// @dev Throw when the cause of failure is the spender to be approved.
    ///  MUST NOT be thrown for transfer operations.
    ///     Use `ERC20InsufficientAllowance` instead.
    ///  MUST be thrown for unintended approvals to the zero address.
    ///  MUST be thrown for unintended approvals to the owner itself.
    error ERC20InvalidSpender(address _spender);

    /// @notice Indicates a failure with the `_spender`'s `_allowance` in a transfer.
    /// @param _spender The address of who's spending the tokens.
    /// @param _allowance The current amount of tokens a spender can use.
    /// @param _needed The allowance needed to perform the operation.
    /// @dev Throw when the cause of failure is the spender's allowance.
    ///  MUST NOT be thrown if `_allowance` is equal or greater than `_needed`.
    ///  MUST be thrown when `_allowance` is less than `_needed`.
    error ERC20InsufficientAllowance(
        address _spender,
        uint256 _allowance,
        uint256 _needed
    );
}

/// @title Standard ERC721 Errors
/// @dev See https://eips.ethereum.org/EIPS/eip-721
///  https://eips.ethereum.org/EIPS/eip-[assignedEIPNumber]
interface ERC721Errors {
    /// @notice Indicates a failure with the token sender.
    /// @param _sender The address of whose token is transferred from.
    /// @dev Throw when the cause of failure is the sender in a transfer.
    ///  MUST NOT be thrown for approval operations.
    ///  MUST NOT be thrown for ownership or approval requirements.
    ///     Use `ERC721InvalidOwner` or `ERC721InsufficientApproval` instead.
    ///  MUST be thrown for unintended transfers from the zero address.
    error ERC721InvalidSender(address _sender);

    /// @notice Indicates a failure with the token receiver.
    /// @param _receiver The address of whose token is transferred to.
    /// @dev Throw when the cause of failure is the receiver in a transfer.
    ///  MUST NOT be thrown for approval operations.
    ///  MUST be thrown for unintended transfers to the zero address.
    ///  MUST be thrown for unintended transfers to non-ERC721TokenReceiver contracts
    ///     or those that reject a transfer.
    ///     (eg. returning an invalid response in `onERC721Received`)
    error ERC721InvalidReceiver(address _receiver);

    /// @notice Indicates an error related to the ownership over a particular token.
    /// @param _sender The address of whose token is transferred from.
    /// @param _tokenId The id of the token to be transferred.
    /// @dev Throw when the cause of failure is the ownership of a token.
    ///  MUST NOT be thrown for approval operations.
    ///  MUST be thrown when `ownerOf(_tokenId)` is not `_sender`.
    error ERC721InvalidOwner(address _sender, uint256 _tokenId);

    /// @notice Indicates a failure with the `_owner` of a token to be approved.
    /// @param _approver The owner's of the token to be approved.
    /// @dev Throw when the cause of failure is the owner of the tokens to be approved.
    ///  MUST NOT be thrown for transfer operations.
    ///  MUST be thrown for unintended approvals from the zero address.
    error ERC721InvalidApprover(address _approver);

    /// @notice Indicates a failure with the `_operator` to be approved.
    /// @param _operator The address of who's getting owner's approval.
    /// @dev Throw when the cause of failure is the spender to be approved.
    ///  MUST NOT be thrown for transfer operations.
    ///     Use `ERC721InsufficientApproval` instead.
    ///  MUST be thrown for unintended approvals to the zero address.
    ///  MUST be thrown for unintended approvals to the owner itself.
    error ERC721InvalidOperator(address _operator);

    /// @notice Indicates a failure with the `_operator`'s approval in a transfer.
    /// @param _operator The address of who's transferring a token.
    /// @param _tokenId The token to be transferred.
    /// @dev Throw when the cause of failure is the operator's approval.
    ///  MUST be thrown when operator `isApprovedForAll(_owner, _operator)` is false
    ///  MUST be thrown when operator `getApproved(_tokenId)` is not `_operator`
    error ERC721InsufficientApproval(address _operator, uint256 _tokenId);
}

/// @title Standard ERC1155 Errors
/// @dev See https://eips.ethereum.org/EIPS/eip-1155
///  https://eips.ethereum.org/EIPS/eip-[assignedEIPNumber]
interface ERC1155Errors {
    /// @notice Indicates a failure with the token sender.
    /// @param _sender The address of whose tokens are transferred from.
    /// @dev Throw when the cause of failure is the sender in a transfer.
    ///  MUST NOT be thrown for approval operations.
    ///  MUST NOT be thrown for balance or allowance requirements.
    ///     Use `ERC1155InsufficientBalance` or `ERC1155InsufficientApproval` instead.
    ///  MUST be thrown for unintended transfers from the zero address.
    error ERC1155InvalidSender(address _sender);

    /// @notice Indicates a failure with the token receiver.
    /// @param _receiver The address of whose tokens are transferred to.
    /// @dev Throw when the cause of failure is the receiver in a transfer.
    ///  MUST NOT be thrown for approval operations.
    ///  MUST be thrown for unintended transfers to the zero address.
    ///  MUST be thrown for unintended transfers to non-ERC1155TokenReceiver contracts
    ///     or those that reject a transfer.
    ///     (eg. returning an invalid response in `onERC1155Received`)
    error ERC1155InvalidReceiver(address _receiver);

    /// @notice Indicates an error related to the current `_balance` of a sender in a transfer.
    /// @param _sender The address of whose tokens are transferred from.
    /// @param _balance The current amount of token the sender has.
    /// @param _needed The amount of token the sender needs to perform the operation.
    /// @param _id The id of the token.
    /// @dev Throw when the cause of failure is the balance of a sender.
    ///  MUST NOT be thrown if `_balance` is equal or greater than `_needed` for an `_id`.
    ///  MUST be thrown when `_balance` is less than `_needed` for an `id`.
    error ERC1155InsufficientBalance(
        address _sender,
        uint256 _balance,
        uint256 _needed,
        uint256 _id
    );

    /// @notice Indicates a failure with the `_approver` of a token to be approved.
    /// @param _approver The owner's of the tokens to be approved.
    /// @dev Throw when the cause of failure is the owner of the tokens to be approved.
    ///  MUST NOT be thrown for transfer operations.
    ///  MUST be thrown for unintended approvals from the zero address.
    error ERC1155InvalidApprover(address _approver);

    /// @notice Indicates a failure with the `_operator` to be approved.
    /// @param _operator The address of who's getting owner's approval.
    /// @dev Throw when the cause of failure is the spender to be approved.
    ///  MUST NOT be thrown for transfer operations.
    ///     Use `ERC1155InsufficientApproval` instead.
    ///  MUST be thrown for unintended approvals to the zero address.
    ///  MUST be thrown for unintended approvals to the owner itself.
    error ERC1155InvalidOperator(address _operator);

    /// @notice Indicates a failure with the `_operator`'s approval in a transfer.
    /// @param _operator The address of who's transferring a token.
    /// @param _id The token to be transferred.
    /// @dev Throw when the cause of failure is the operator's approval.
    ///  MUST be thrown when operator `isApprovedForAll(_owner, _operator, _id)` is false
    error ERC1155InsufficientApproval(address _operator, uint256 _id);
}
```

## Rationale

The objectives of creating a standard for token errors are to provide context about the error, while keeping gramatical sense, and making a moderated use of meaningful arguments.

Considering the outlined above, the errors are designed based on the standard actions that can be performed on each token, and the [subjects](#actions-and-subjects) involved.

### Actions and subjects

The main actions that can be performed within a token are:

- **Transfer**: An operation in which a _sender_ moves any form of _balance_ to a _receiver_.
- **Approval**: An operation in which an _approver_ grants any form of _approval_ to an _operator_

Giving the following list of subjects:

- **Sender**
- **Receiver**
- **Balance**
- **Approver**
- **Operator**
- **Approval**

These subjects are expected to be exhaustive to represent _what_ can go wrong on a transaction, which can be shown as an error by adding a [failure prefix](#error-prefixes).

Note that `Token` or `TokenID` MUST NOT be a subject for the following reasons:

1. The [Domain](#domain) is explicit enough to identify it as a token error.
2. Although `TokenID` may identify an error, the token itself SHOULD NOT the root of the error, but can be additional information.

### Error prefixes

- **Invalid**: Used for general incorrectness.
- **Insufficient**: Used when defining an error for amounts (eg. balance, approval).

These prefixes combined with the subjects create the following list of base standard errors.

### Base standard errors

- **InvalidSender**
- **InvalidReceiver**
- **InsufficientBalance**
- **InvalidApprover**
- **InvalidOperator**
- **InsufficientApproval**

### Domain

Although these errors are considered to be expressive enough, each error's arguments may vary depending on the token domain, causing a `DeclarationError` if there are errors with the same name and different arguments.

An example of this is:

```solidity
InsufficientApproval(address _spender, uint256 _allowance, uint256 _needed);
InsufficientApproval(address _operator, uint256 _tokenId);
```

For that reason, a domain prefix is proposed to avoid declaration clashing, which is the name of the ERC and its corresponding number appended at the beginning.

Example:

```solidity
ERC20InsufficientApproval(address _spender, uint256 _allowance, uint256 _needed);
ERC721InsufficientApproval(address _operator, uint256 _tokenId);
```

### Subject naming adjustments

While the base standard errors can work with the current standard token implementations, in practice, some of the subjects in a contract might be called different while they represent the same. These are listed below:

- **Balance**: Represents the access to a certain amount of tokens for an address.
  - EIP-721: Instead of _balance_, an address is _owner_ of a token.
- **Approval**: Represents a permission granted to an operator.
  - EIP-20: Instead of _approval_, an operator has _allowance_.
- **Operator**: Represents an address that has received approval over third-party tokens.
  - EIP-20: Instead of _operator_, an address with allowance is an _spender_.

If a subject is called different on a particular token standard, the error MUST be consistent with the standard's naming convention.

### Arguments

The selection of arguments depends on the subject involved, and it MUST follow the order presented below:

1. _Who_ is involved with the error (an `address`) - _REQUIRED_
2. _What_ failed (eg. `uint256 _allowance`) - _OPTIONAL_
3. _Why_ it failed, expressed in additional arguments (eg. `uint256 _needed`) - _OPTIONAL_

Consider that sometimes _Who_ is also the _What_, that's why argument 2 is also optional.

Note: Some tokens may need a `_tokenId` or `_id`. This is suggested to include at the end as additional information, according to the [subjects list](#actions-and-subjects)

### Error grammar rules

Given the above, we can summarize the construction of error names with a grammar that errors MUST follow:

```
<Domain><ErrorPrefix><Subject>(<Arguments>);
```

Where:

- _Domain_: SHOULD be `ERC20`, `ERC721` or `ERC1155`. Although other token standards may be suggested if not considered in this EIP.
- _ErrorPrefix_: MUST be `Invalid` or `Insufficient`.
- _Actor_: SHOULD be `Sender`, `Receiver`, `Balance`, `Approver`, `Operator` or `Approval`, and MUST make adjustments based on domain's naming convention.
- _Arguments_: MUST follow the [_who_, _what_ and _why_ order](#arguments).

## Backwards Compatibility

<!-- All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright. -->

This EIP can not be enforced on non-upgradeable already deployed tokens, however, these tokens generally use similar conventions with small variations such as:

- including/removing the [domain](#domain).
- using different [error prefixes](#error-prefixes).
- including similar [subjects](#actions-and-subjects).
- changing the grammar order.

New deployed contracts MUST use this EIP whenever possible. Errors in extensions are not considered in this EIP and MAY have different subjects.

Upgradeable contracts MAY be upgraded to implement this EIP.

Implementers and DApp developers SHOULD expect these EIP errors to be present on new deployed contracts, but MUST handle variation errors for existing contracts, and revert strings that may be returned.

## Reference Implementation

<!-- An optional section that contains a reference/example implementation that people can use to assist in understanding or implementing this specification. If the implementation is too large to reasonably be included inline, then consider adding it as one or more files in `../assets/eip-####/`. -->

## Security Considerations

There are no known signature hash collision for the specified errors.

Tokens upgraded to implement this EIP may break assumptions in other systems relying on revert strings.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).

## Citation

Please cite this document as:

Francisco Giordano, Ernesto García, "EIP-[assignedEIPNumber]: Standard Token Errors [DRAFT]," Ethereum Improvement Proposals, no. [assignedEIPNumber], November 2022. [Online serial]. Available: https://eips.ethereum.org/EIPS/eip-[assignedEIPNumber].
