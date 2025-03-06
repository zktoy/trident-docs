# Contract Interaction

Trident provides methods to interact with deployed smart contracts. There are two types of contract interactions:

## View Functions (Constant Call)

View functions read data from the blockchain without modifying state. These calls are free and don't require transaction signing:

```java
// Call view function (e.g., balanceOf)
Function balanceOfFunction = new Function(
        "balanceOf",
        Collections.singletonList(new Address(accountAddr)),
        Collections.singletonList(new TypeReference<Uint256>() {})
);

String encodedHex = FunctionEncoder.encode(balanceOfFunction);

TransactionExtention txn = client.triggerConstantContract(
        ownerAddress,    // Caller address
        contractAddress, // Contract address
        encodedHex      // Encoded function call
);

// Decode the result
String result = Numeric.toHexString(txnExt.getConstantResult(0).toByteArray());
BigInteger balance = (BigInteger) FunctionReturnDecoder.decode(
        result, 
        balanceOfFunction.getOutputParameters()
).get(0).getValue();
```

## State-Modifying Functions

Functions that modify contract state require a transaction and consume resources:

```java
// Transfer tokens
Function trc20Transfer = new Function(
        "transfer",
        Arrays.asList(
                new Address(toAddress),
                new Uint256(BigInteger.valueOf(10).multiply(BigInteger.valueOf(10).pow(6))) //decimals
        ),
        Collections.singletonList(new TypeReference<Bool>() {})
);
String encodedHex = FunctionEncoder.encode(trc20Transfer);

TransactionExtention transactionExtention = client.triggerContract(
        fromAddr,        // Sender Address
        contractAddress, // Contract Address
        encodedHex,      // Encoded function call
        0,              // call value
        0,              // token value
        null,           // token id
        150_000_000L    // fee Limit
);

// Sign and broadcast
Transaction signedTxn = client.signTransaction(transactionExtention);
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
