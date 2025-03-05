# Transaction Guide

A transaction is an operation that needs to be recorded on the TRON blockchain. These on-chain operations (like TRX transfer, stake & unstake, Trigger Smart Contract, etc.) require bandwidth and energy consumption.

For security considerations, Trident builds all transactions locally and returns a `TransactionExtention` structure for signing and broadcasting, rather than relying on remote transaction construction.

## Transaction Lifecycle

A normal routine for sending a transaction is:

```
Create -> Sign -> Broadcast -> (wait) -> Lookup and get receipt
```

### 1. Create Transaction

Creating a transaction locally, for example:

```java
// Transfer TRX
TransactionExtention transactionExtention = client.transfer("from_address", "to_address", amount);
```

### 2. Add Extra Parameters

`TransactionBuilder` allows you to add or modify parameters before signing:

```java
TransactionBuilder builder = new TransactionBuilder(transaction); //transaction is transactionExtention.getTransaction();
builder.setFeeLimit(100000000L);  // Only required for smart contract transactions
builder.setMemo("memo");
Transaction transaction = builder.build();
```

### 3. Sign Transaction

There are two ways to sign a transaction:

#### Sign with the binding private key

```java
// Using the private key bound to ApiWrapper instance
Transaction signedTxn = client.signTransaction(transaction);
```

#### Sign with a specific private key

```java
// Using any private key
Transaction signedTxn = client.signTransaction(transaction, SECP256K1.KeyPair);
```

### 4. Broadcast Transaction

```java
String txid = client.broadcastTransaction(signedTxn);
System.out.println("Transaction sent: " + txid);
```

!!! note
    Transaction hash may change due to any modification to the original transaction (except the signature).
    The hash is re-calculated before broadcasting.

### 5. Verify Transaction

```java
import org.tron.trident.proto.Response.TransactionInfo;

// Get transaction info by ID
TransactionInfo txInfo = client.getTransactionInfoById(txid);
System.out.println("Transaction status: " + (txInfo.getResult() == SUCCESS));
```

## Transaction Types

Trident supports most types of transactions on TRON network. For details about transaction types, please refer to [Transaction Types](transaction-types.md).

## Advanced Transaction Creation

By default, Trident builds transactions locally but needs to query the fullnode for block information:

1. Gets latest BlockID info via `getBlock` API
2. Sets reference block and expiration time

For advanced users who are familiar with java-tron's internal mechanisms, Trident provides a way to optimize performance by manually specifying block information:

```java
// Initialize client
ApiWrapper client = ApiWrapper.ofNile("your_private_key");

// Enable local creation with specified block info
client.enableLocalCreate(
    blockId,       // Block ID from a recent block
    expireTime     // Transaction expiration time (current timestamp + N hours)
);

// Create transaction (no block query needed)
TransactionExtention txn = client.transfer(from, to, amount);

// Remember to disable local creation when done
client.disableLocalCreate();
```

!!! warning "Advanced Usage"
    - This is an advanced feature that requires deep understanding of java-tron
    - Incorrect block reference or expiration time may cause transaction failures
    - Block ID must be from a recent block within the node's solid block range
    - Always call `disableLocalCreate()` after you're done to release resources
    - **For most users, the default transaction creation method is recommended**
