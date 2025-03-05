# Application Binary Interface

The Application Binary Interface (ABI) is a data encoding scheme for working with smart contracts. The types defined in the ABI are the same as those you encounter when writing smart contracts with Solidity.

## Using ABI in Trident

The `abi` package in Trident includes libraries to encode/decode ABIs. Here's an example of how to interact with a contract using ABI:

```java
// 1. Define function parameters (empty for totalSupply)
List<Type> inputParameters = Collections.emptyList();

// 2. Encode the parameters
String methodSignature = "totalSupply()";
String encodedHex = FunctionEncoder.encode(
    methodSignature,
    inputParameters
);

// 3. Call the contract
TransactionExtention txnExt = client.triggerConstantContract(
    contractAddress,    // Contract address
    methodSignature,    // Method signature
    encodedHex,        // Encoded parameters
    0,                 // Call value (amount of TRX to send)
    ownerAddress       // Caller address
);

// 4. Decode the result
String result = Numeric.toHexString(txnExt.getConstantResult(0).toByteArray());
List<Type> decodedResult = FunctionReturnDecoder.decode(
    result,
    Arrays.asList(new TypeReference<Uint256>() {})  // Expected return type
);
BigInteger totalSupply = (BigInteger) decodedResult.get(0).getValue();

// For tokens with 18 decimals (like JST), the result might be:
// 9900000000000000000000000000
```

## Type Matching

The parameter types in your code must exactly match the function definition in the smart contract. For example:

```solidity
// In smart contract
function transfer(address _to, uint _value) public returns (bool)  // uint is alias for uint256
```

```java
// In Java code - Correct ✓
List<Type> params = Arrays.asList(
    new Address("TRxxxxxxxxxxxxxxxxxxxxxxxxxxx"),  // address type
    new Uint256(1000000)                         // uint256 type
);

// In Java code - Wrong ✗
List<Type> params = Arrays.asList(
    new Utf8String("TRxxxxxxxxxxxxxxxxxxxxxxxxxxx"),  // wrong type for address
    new Int256(1000000)                             // wrong type for uint
);
```

For TRC10 token, use `Uint256` even if the contract parameter is defined as `trcToken`:

```solidity
// In smart contract
function transferToken(address _to, trcToken _id, uint256 _amount) external;
```

```java
// In Java code
List<Type> params = Arrays.asList(
    new Address("TRxxxxxxxxxxxxxxxxxxxxxxxxxxx"),  // address _to
    new Uint256(1000016),                        // trcToken _id
    new Uint256(1000000)                         // uint256 _amount
);
```

Using incorrect types will result in transaction failure or unexpected behavior. 
