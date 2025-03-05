# KeyPair Management

TRON's signature algorithm is ECDSA, and the selected curve is SECP256K1.

From version 0.1.3, trident-java has implemented a wrapper class for SECP256K1.KeyPair in Core to facilitate the generation of private keys and the conversion of private keys to addresses. This classpath is: `org.tron.trident.core.key.KeyPair`

## Generate KeyPair

```java
KeyPair keyPair = KeyPair.generate();
```

## Import KeyPair with Private Key

```java
KeyPair keyPair = new KeyPair("your private key");
```

## Get Private & Public Key

```java
keyPair.toPrivateKey(); // String private key
keyPair.toPublicKey();  // String public key
```

!!! note
    The public key does not equal to address.

## Get Address

```java
keyPair.toBase58CheckAddress(); // Get Base58Check address
keyPair.toHexAddress();        // Get Hex address
```

## Public Key to Address

Use a specific public key to convert to byte[], Base58Check or Hex address:

```java
// The parent function, returns byte[]
KeyPair.publicKeyToAddress(SECP256K1.PublicKey pubKey); 

KeyPair.publicKeyToBase58CheckAddress(SECP256K1.PublicKey pubKey);

KeyPair.publicKeyToHexAddress(SECP256K1.PublicKey pubKey);
```

## Sign Transaction

```java
// This function returns the signature message in byte[]
KeyPair.signTransaction(byte[] txid, KeyPair keyPair);
```

!!! warning "Private Key Security"
    Private key is the only credential to access and control your assets. Never share it with others and always keep a secure backup.
