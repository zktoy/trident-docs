# Contract Interaction

Trident provides methods to interact with deployed smart contracts. There are two types of contract interactions:

## View Functions (Constant Call)

View functions read data from the blockchain without modifying state. These calls are free and don't require transaction signing:

```java
// Initialize client
ApiWrapper client = ApiWrapper.ofNile("your_private_key");

// Call view function (e.g., balanceOf)
TransactionExtention txn = client.triggerConstantContract(
    contractAddress,                   // Contract address
    "balanceOf(address)",             // Method signature
    FunctionEncoder.encode(           // Encode parameters
        Arrays.asList(new Address("TRxxxxxxxxxxxxxxxxxxxxxxxxxxx"))
    ),
    0,                               // Call value (0 for view functions)
    ownerAddress                     // Caller address
);

// Decode the result
String result = Numeric.toHexString(txn.getConstantResult(0).toByteArray());
List<Type> decodedResult = FunctionReturnDecoder.decode(
    result,
    Arrays.asList(new TypeReference<Uint256>() {})
);
BigInteger balance = (BigInteger) decodedResult.get(0).getValue();
```

## State-Modifying Functions

Functions that modify contract state require a transaction and consume resources:

```java
// Transfer tokens
TransactionExtention txn = client.triggerContract(
    contractAddress,                   // Contract address
    "transfer(address,uint256)",      // Method signature
    FunctionEncoder.encode(           // Encode parameters
        Arrays.asList(
            new Address("TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t"),
            new Uint256(1000000)
        )
    ),
    0,                               // Call value (amount of TRX to send)
    0,                               // Token value (for TRC10)
    "",                             // Token ID (for TRC10)
    ownerAddress,                   // Caller address
    feeLimit                        // Maximum TRX fee willing to pay
);

// Sign and broadcast
Transaction signedTxn = client.signTransaction(txn);
String txid = client.broadcastTransaction(signedTxn);
```

## Error Handling

Check the transaction status after execution:

```java
// Check if the constant call was successful
if (!txn.getResult().getResult()) {
    String message = txn.getResult().getMessage().toStringUtf8();
    throw new RuntimeException("Contract call failed: " + message);
}

// For state-modifying functions, you can get the transaction receipt
TransactionInfo info = client.getTransactionInfoById(txid);
if (info.getResult() != SUCCESS) {
    throw new RuntimeException("Transaction failed: " + info.getResMessage().toStringUtf8());
}
```
