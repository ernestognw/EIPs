---
eip: [assignedEIPNumber]
title: Custom errors for ERC tokens
description: List of Solidity custom errors covering standard token implementations (EIP-20, EIP-721 and EIP-1155)
author: Ernesto García (@ernestognw), Francisco Giordano (@frangio), Hadrien Croubois (@Amxx)
discussions-to: <URL>
status: Draft
type: Standards Track
category: ERC
requires: 20, 721, 1155
created: <date created on, in ISO 8601 (yyyy-mm-dd) format>
---

## Abstract

The following specification allows for the use of a standard list of Solidity [custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) to be used within [EIP-20](./eip-20.md), [EIP-721](./eip-721.md) and [EIP-1155](./eip-1155.md) tokens.

Ethereum applications (DApps) and wallets have historically used [revert statements](https://docs.soliditylang.org/en/v0.8.16/control-structures.html#revert-statement) to show relevant failure data to the users, however, this EIP provides a standard list of errors designed to give at least the same relevant information, but in a structured and expected way.

## Motivation

Since the introduction of Solidity custom errors in [v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4), these have provided a way to show failures in a more expresive way with dynamic arguments, while reducing deployment costs.

At the moment of the release of custom errors, the standard tokens ([EIP-20](./eip-20.md), [EIP-721](./eip-721.md), [EIP-1155](./eip-1155.md)) were already in finalized state, so no error specification was included.

An error specification will allow users to expect more consistent error messages across applications or testing environments, while exposing pertinent arguments and overall reducing the need of writing expensive revert strings in the deployment bytecode.

The list of token standard errors is defined in the specification below.

## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

The provided interfaces with errors SHOULD conform with the [error grammar rules](#error-grammar-rules) described in the [rationale](#rationale) section.

It's important to note that errors MAY be thrown in each scenario, but that's up to the implementers, unless a revert reason is specified in their corresponding EIPs.

This EIP aims to provide a clear standard only in case an error will be thrown.

### [EIP-20](./eip-20.md)

#### `ERC20InvalidSender(address)`

Indicates a failure with the token sender.

##### Parameters

- `address sender`: The address of whose tokens are transferred from.

Used when the cause of failure is the sender in a transfer.

- MUST NOT be thrown for approval operations.
- MUST NOT be thrown for balance or allowance requirements.
  - Use `ERC20InsufficientBalance` or `ERC20InsufficientAllowance` instead.
- MUST be thrown for disallowed transfers from the zero address.

#### `ERC20InvalidReceiver(address)`

Indicates a failure with the token receiver.

##### Parameters

- `address receiver` The address of whose tokens are transferred to.

Used when the cause of failure is the receiver in a transfer.

- MUST NOT be thrown for approval operations.
- MUST be thrown for disallowed transfers to the zero address.
- MUST be thrown for disallowed transfers to non-compatible addresses (eg. contract addresses).

#### `ERC20InsufficientBalance(address, uint256, uint256)`

Indicates an error related to the current `balance` of a sender in a transfer.

##### Parameters

- `address sender`: The address of whose tokens are transferred from.
- `uint256 balance`: The current amount of token the sender has.
- `uint256 needed`: The amount of token the sender needs to perform the operation.

Used when the cause of failure is the balance of a sender.

- MUST NOT be thrown if `balance` is equal or greater than `needed`.
- MUST be thrown when `balance` is less than `needed`.

#### `ERC20InvalidApprover(address)`

Indicates a failure with the `approver` of a token to be approved.

##### Parameters

- `address approver`: The owner's of the tokens to be approved.

Used when the cause of failure is the owner of the tokens to be approved.

- MUST NOT be thrown for transfer operations.
- MUST be thrown for disallowed approvals from the zero address.

#### `ERC20InvalidSpender(address)`

Indicates a failure with the `spender` to be approved.

##### Parameters

- `address spender` The address of who's getting owner's approval.

Used when the cause of failure is the spender to be approved.

- MUST NOT be thrown for transfer operations.
  - Use `ERC20InsufficientAllowance` instead.
- MUST be thrown for disallowed approvals to the zero address.
- MUST be thrown for disallowed approvals to the owner itself.

#### `ERC20InsufficientAllowance(address, uint256, uint256)`

Indicates a failure with the `spender`'s `allowance` in a transfer.

##### Parameters

- `address spender`: The address of who's spending the tokens.
- `uint256 allowance`: The current amount of tokens a spender can use.
- `uint256 needed`: The allowance needed to perform the operation.

Used when the cause of failure is the spender's allowance.

- MUST NOT be thrown if `allowance` is equal or greater than `needed`.
- MUST be thrown when `allowance` is less than `needed`.

### [EIP-721](./eip-721.md)

#### `ERC721InvalidSender(address)`

Indicates a failure with the token sender.

##### Parameters

- `address sender`: The address of whose token is transferred from.

Used when the cause of failure is the sender in a transfer.

- MUST NOT be thrown for approval operations.
- MUST NOT be thrown for ownership or approval requirements.
  - Use `ERC721InvalidOwner` or `ERC721InsufficientApproval` instead.
- MUST be thrown for disallowed transfers from the zero address.

#### `ERC721InvalidReceiver(address)`

Indicates a failure with the token receiver.

##### Parameters

- `address receiver`: The address of whose token is transferred to.

Used when the cause of failure is the receiver in a transfer.

- MUST NOT be thrown for approval operations.
- MUST be thrown for disallowed transfers to the zero address.
- MUST be thrown for disallowed transfers to non-ERC721TokenReceiver contracts or those that reject a transfer. (eg. returning an invalid response in `onERC721Received`).

#### `ERC721InvalidOwner(address, uint256)`

Indicates an error related to the ownership over a particular token.

- `address sender`: The address of whose token is transferred from.
- `uint256 tokenId`: The id of the token to be transferred.

##### Parameters

Used when the cause of failure is the ownership of a token.

- MUST NOT be thrown for approval operations.
- MUST be thrown when `ownerOf(tokenId)` is not `sender`.

#### `ERC721InvalidApprover(address)`

Indicates a failure with the `owner` of a token to be approved.

##### Parameters

- `address approver`: The owner's of the token to be approved.

Used when the cause of failure is the owner of the tokens to be approved.

- MUST NOT be thrown for transfer operations.
- MUST be thrown for disallowed approvals from the zero address.

#### `ERC721InvalidOperator(address)`

Indicates a failure with the `operator` to be approved.

##### Parameters

- `address operator`: The address of who's getting owner's approval.

Used when the cause of failure is the spender to be approved.

- MUST NOT be thrown for transfer operations.
  - Use `ERC721InsufficientApproval` instead.
- MUST be thrown for disallowed approvals to the zero address.
- MUST be thrown for disallowed approvals to the owner itself.

#### `ERC721InsufficientApproval(address, uint256)`

Indicates a failure with the `operator`'s approval in a transfer.

##### Parameters

- `address operator`: The address of who's transferring a token.
- `uint256 tokenId`: The token to be transferred.

Used when the cause of failure is the operator's approval.

- MUST be thrown when operator `isApprovedForAll(owner, operator)` is false.
- MUST be thrown when operator `getApproved(tokenId)` is not `operator`.

### [EIP-1155](./eip-1155.md)

#### `ERC1155InvalidSender(address)`

Indicates a failure with the token sender.

##### Parameters

- `address sender`: The address of whose tokens are transferred from.

Used when the cause of failure is the sender in a transfer.

- MUST NOT be thrown for approval operations.
- MUST NOT be thrown for balance or allowance requirements.
  - Use `ERC1155InsufficientBalance` or `ERC1155InsufficientApproval` instead.
- MUST be thrown for disallowed transfers from the zero address.

#### `ERC1155InvalidReceiver(address)`

Indicates a failure with the token receiver.

##### Parameters

- `address receiver` The address of whose tokens are transferred to.

Used when the cause of failure is the receiver in a transfer.

- MUST NOT be thrown for approval operations.
- MUST be thrown for disallowed transfers to the zero address.
- MUST be thrown for disallowed transfers to non-ERC1155TokenReceiver contracts or those that reject a transfer. (eg. returning an invalid response in `onERC1155Received`).

#### `ERC1155InsufficientBalance(address, uint256, uint256, uint256)`

Indicates an error related to the current `balance` of a sender in a transfer.

##### Parameters

- `address sender`: The address of whose tokens are transferred from.
- `uint256 balance`: The current amount of token the sender has.
- `uint256 needed`: The amount of token the sender needs to perform the operation.
- `uint256 id`: The id of the token.

Used when the cause of failure is the balance of a sender.

- MUST NOT be thrown if `balance` is equal or greater than `needed` for an `id`.
- MUST be thrown when `balance` is less than `needed` for an `id`.

#### `ERC1155InvalidApprover(address)`

Indicates a failure with the `approver` of a token to be approved.

##### Parameters

- `address approver`: The owner's of the tokens to be approved.

Used when the cause of failure is the owner of the tokens to be approved.

- MUST NOT be thrown for transfer operations.
- MUST be thrown for disallowed approvals from the zero address.

#### `ERC1155InvalidOperator(address)`

Indicates a failure with the `operator` to be approved.

##### Parameters

- `address operator`: The address of who's getting owner's approval.

Used when the cause of failure is the spender to be approved.

- MUST NOT be thrown for transfer operations.
  - Use `ERC1155InsufficientApproval` instead.
- MUST be thrown for disallowed approvals to the zero address.
- MUST be thrown for disallowed approvals to the owner itself.

#### `ERC1155InsufficientApproval(address, uint256)`

Indicates a failure with the `operator`'s approval in a transfer.

##### Parameters

- `address operator`: The address of who's transferring a token.
- `uint256 id`: The token to be transferred.

Used when the cause of failure is the operator's approval.

- MUST be thrown when operator `isApprovedForAll(owner, operator, id)` is false.

### Error additions

Any addition to this EIP is considerred a new proposal, but SHOULD follow the guidelines presented in the [rationale](#rationale) section to keep consistency.

## Rationale

The objectives of creating a standard for token errors are to provide context about the error, while keeping gramatical sense, and making a moderated use of meaningful arguments.

Considering the outlined above, the errors are designed based on the standard actions that can be performed on each token, and the [subjects](#actions-and-subjects) involved.

### Actions and subjects

The main actions that can be performed within a token are:

- **Transfer**: An operation in which a _sender_ moves any form of _balance_ to a _receiver_.
- **Approval**: An operation in which an _approver_ grants any form of _approval_ to an _operator_.

Whose subjects outlined are expected to be exhaustive to represent _what_ can go wrong in a transaction, which can be shown as an error by adding a [failure prefix](#error-prefixes).

Note that `Token` or `TokenID` MUST NOT be a subject for the following reasons:

1. The [Domain](#domain) is explicit enough to identify it as a token error.
2. Although `TokenID` may identify an error, the token itself SHOULD NOT the root of the error, but can be additional information.

Consider that, in practice, some of the subjects in a contract might be called different while they represent the same. These are listed below:

- **Balance**: Represents the access to a certain amount of tokens for an address.
  - EIP-721: Instead of _balance_, an address is _owner_ of a token.
- **Approval**: Represents a permission granted to an operator.
  - EIP-20: Instead of _approval_, an operator has _allowance_.
- **Operator**: Represents an address that has received approval over third-party tokens.
  - EIP-20: Instead of _operator_, an address with allowance is an _spender_.

If a subject is called different on a particular token standard, the error MUST be consistent with the standard's naming convention.

### Error prefixes

An error prefix is what complements a subject, so it can be read as an error.
Developers can think about an error prefix as the _why_ an error happened.

A prefix can be `Invalid` for general incorrectness, or more specific like `Insufficient` for amounts (or lack of).

### Domain

Although these errors are considered to be expressive enough, each error's arguments may vary depending on the token domain, causing a `DeclarationError` if there are errors with the same name and different arguments.

An example of this is:

```solidity
InsufficientApproval(address spender, uint256 allowance, uint256 needed);
InsufficientApproval(address operator, uint256 tokenId);
```

For that reason, a domain prefix is proposed to avoid declaration clashing, which is the name of the ERC and its corresponding number appended at the beginning.

Example:

```solidity
ERC20InsufficientApproval(address spender, uint256 allowance, uint256 needed);
ERC721InsufficientApproval(address operator, uint256 tokenId);
```

### Arguments

The selection of arguments depends on the subject involved, and it MUST follow the order presented below:

1. _Who_ is involved with the error (an `address`) - _REQUIRED_
2. _What_ failed (eg. `uint256 allowance`) - _OPTIONAL_
3. _Why_ it failed, expressed in additional arguments (eg. `uint256 needed`) - _OPTIONAL_

Consider that sometimes _Who_ is also the _What_, that's why argument 2 is also optional.

Note: Some tokens may need a `tokenId` or `id`. This is suggested to include at the end as additional information, according to the [subjects notes](#actions-and-subjects)

### Error grammar rules

Given the above, we can summarize the construction of error names with a grammar that errors MUST follow:

```
<Domain><ErrorPrefix><Subject>(<Arguments>);
```

Where:

- _Domain_: SHOULD be `ERC20`, `ERC721` or `ERC1155`. Although other token standards may be suggested if not considered in this EIP.
- _ErrorPrefix_: MAY be `Invalid`, `Insufficient`, or another if it's more appropiated.
- _Subject_: MAY be `Sender`, `Receiver`, `Balance`, `Approver`, `Operator`, `Approval` or another if it's more appropiated, and MUST make adjustments based on domain's naming convention.
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

### Solidity

```solidity
pragma solidity ^0.8.4;

/// @title Standard ERC20 Errors
/// @dev See https://eips.ethereum.org/EIPS/eip-20
///  https://eips.ethereum.org/EIPS/eip-[assignedEIPNumber]
interface ERC20Errors {
    /// @notice See [reference](#erc20invalidsender).
    error ERC20InvalidSender(address sender);
    /// @notice See [reference](#erc20invalidreceiver).
    error ERC20InvalidReceiver(address receiver);
    /// @notice See [reference](#erc20insufficientbalance).
    error ERC20InsufficientBalance(address sender, uint256 balance, uint256 needed);
    /// @notice See [reference](#erc20invalidapprover).
    error ERC20InvalidApprover(address approver);
    /// @notice See [reference](#erc20invalidspender).
    error ERC20InvalidSpender(address spender);
    /// @notice See [reference](#erc20insufficientallowance).
    error ERC20InsufficientAllowance(address spender, uint256 allowance, uint256 needed);
}

/// @title Standard ERC721 Errors
/// @dev See https://eips.ethereum.org/EIPS/eip-721
///  https://eips.ethereum.org/EIPS/eip-[assignedEIPNumber]
interface ERC721Errors {
    /// @notice See [reference](#erc721invalidsender).
    error ERC721InvalidSender(address sender);
    /// @notice See [reference](#erc721invalidreceiver).
    error ERC721InvalidReceiver(address receiver);
    /// @notice See [reference](#erc721invalidowner).
    error ERC721InvalidOwner(address sender, uint256 tokenId);
    /// @notice See [reference](#erc721invalidapprover).
    error ERC721InvalidApprover(address approver);
    /// @notice See [reference](#erc721invalidoperator).
    error ERC721InvalidOperator(address operator);
    /// @notice See [reference](#erc721insufficientapproval).
    error ERC721InsufficientApproval(address operator, uint256 tokenId);
}

/// @title Standard ERC1155 Errors
/// @dev See https://eips.ethereum.org/EIPS/eip-1155
///  https://eips.ethereum.org/EIPS/eip-[assignedEIPNumber]
interface ERC1155Errors {
    /// @notice See [reference](#erc1155invalidsender).
    error ERC1155InvalidSender(address sender);
    /// @notice See [reference](#erc1155invalidreceiver).
    error ERC1155InvalidReceiver(address receiver);
    /// @notice See [reference](#erc1155insufficientbalance).
    error ERC1155InsufficientBalance(address sender, uint256 balance, uint256 needed, uint256 id);
    /// @notice See [reference](#erc1155invalidapprover).
    error ERC1155InvalidApprover(address approver);
    /// @notice See [reference](#erc1155invalidoperator).
    error ERC1155InvalidOperator(address operator);
    /// @notice See [reference](#erc1155insufficientapproval).
    error ERC1155InsufficientApproval(address operator, uint256 id);
}
```

## Security Considerations

There are no known signature hash collision for the specified errors.

Tokens upgraded to implement this EIP may break assumptions in other systems relying on revert strings.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).

## Citation

Please cite this document as:

Ernesto García, Francisco Giordano, Hadrien Croubois, "EIP-[assignedEIPNumber]: Standard Token Errors [DRAFT]," Ethereum Improvement Proposals, no. [assignedEIPNumber], November 2022. [Online serial]. Available: https://eips.ethereum.org/EIPS/eip-[assignedEIPNumber].
