# Transaction Signing

Transaction signing is a crucial security step that proves you have the authority to spend from an address. Trident provides multiple ways to sign transactions.

## Signing Methods

### 1. Sign with ApiWrapper Instance

The simplest way is to use the private key bound to your ApiWrapper instance:

```java
// The private key is already bound when creating the client
ApiWrapper client = ApiWrapper.ofNile("your_private_key");

// Sign directly with bound private key
Transaction signedTxn = client.signTransaction(transaction);
```

### 2. Sign with Specific KeyPair

You can also sign with any KeyPair:

```java
// Create or import a KeyPair
KeyPair keyPair = new KeyPair("private_key");

// Sign with specific KeyPair
Transaction signedTxn = client.signTransaction(transaction, keyPair);
```

## Signature Validation

Trident provides methods to validate signature correctness using transaction ID and signature message. The validation works by:

1. Recovering the public key from the signed message
2. Converting the public key to an address
3. Comparing with the initiator's address

### Verify Methods

```java
// Verify using raw data
boolean isValid = KeyPair.verify(byte[] txid, byte[] signature, byte[] ownerAddress);

// Verify using String format
boolean isValid = KeyPair.verify(String txid, String signature, String ownerAddress);
```

!!! note
    The transaction ID (txid) is obtained by calculating SHA256 of `Transaction.rawData`

!!! warning "Security Notice"
    - Never share or expose your private keys
    - Always verify the transaction details before signing
    - Keep your signing environment secure and isolated
