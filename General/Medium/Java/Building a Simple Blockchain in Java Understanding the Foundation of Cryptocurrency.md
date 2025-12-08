
**By Full Stack Developer**  
*Aug 18, 2025*  
*4 min read*  
*Member-only story*

I hope this article sparks an interest if you are new to Blockchain! :) Blockchain technology has revolutionized how we think about data integrity and decentralized systems.

![Photo by Jasmin Schreiber on Unsplash](https://miro.medium.com/v2/resize:fit:1200/0*wPKGQhFiF-wH4Mhu)

> Let’s build a simplified blockchain from scratch in Java to understand its core concepts.

## What Makes a Blockchain Special?

A blockchain is essentially a linked list of blocks, where each block contains:

- Data (transactions, messages, etc.)
- A timestamp
- A cryptographic hash of the previous block
- Its own hash

This creates an immutable chain where tampering with any block would break the entire chain!!

## Implementation

## 1. The Block Class

```java
import java.security.MessageDigest;
import java.util.Date;

public class Block {

 private String data;
 private String previousHash;
 private String hash;
 private long timestamp;
 private int nonce; // For proof-of-work mining


 public Block(String data, String previousHash) {
 this.data = data;
 this.previousHash = previousHash;
 this.timestamp = new Date().getTime();
 this.nonce = 0;
 this.hash = calculateHash();
 }

 public String calculateHash() {
 String input = previousHash + timestamp + nonce + data;
 return applySha256(input);
 }

 public static String applySha256(String input) {

 try {
 MessageDigest digest = MessageDigest.getInstance(\SHA-256\);
 byte[] hash = digest.digest(input.getBytes(\UTF-8\));
 StringBuilder hexString = new StringBuilder();
 
 for (byte b : hash) {
 String hex = Integer.toHexString(0xff & b);
 if (hex.length() == 1) {
 hexString.append('0');
 }
 hexString.append(hex);
 }
 return hexString.toString();
 } catch (Exception e) {
 throw new RuntimeException(e);
 }
 }


 // Mining method - proof of work
 public void mineBlock(int difficulty) {
 String target = new String(new char[difficulty]).replace('\\0', '0');
 
 while (!hash.substring(0, difficulty).equals(target)) {
 nonce++;
 hash = calculateHash();
 }
 
 System.out.println(\Block mined: \ + hash);
 }

 // Getters
 public String getHash() { return hash; }
 public String getPreviousHash() { return previousHash; }
 public String getData() { return data; }
 public long getTimestamp() { return timestamp; }
}
```

## 2. The Blockchain Class

```java
import java.util.ArrayList;
import java.util.List;

public class SimpleBlockchain {

 private List<Block> chain;
 private int difficulty;

 public SimpleBlockchain() {
 this.chain = new ArrayList<>();
 this.difficulty = 4; // Number of leading zeros required
 createGenesisBlock();
 }

 private void createGenesisBlock() {
 Block genesis = new Block(\Genesis Block\, \0\);
 genesis.mineBlock(difficulty);
 chain.add(genesis);
 }

 public Block getLatestBlock() {
 return chain.get(chain.size() - 1);
 }

 public void addBlock(Block newBlock) {
 newBlock.mineBlock(difficulty);
 chain.add(newBlock);
 }

 public boolean isChainValid() {
 for (int i = 1; i < chain.size(); i++) {
 Block currentBlock = chain.get(i);
 Block previousBlock = chain.get(i - 1);
 // Check if current block's hash is valid
 if (!currentBlock.getHash().equals(currentBlock.calculateHash())) {
 System.out.println(\Current hashes not equal\);
 return false;
 }
 // Check if previous hash matches
 if (!currentBlock.getPreviousHash().equals(previousBlock.getHash())) {
 System.out.println(\Previous hashes not equal\);
 return false;
 }
 }
 return true;
 }

 public void printBlockchain() {
 for (int i = 0; i < chain.size(); i++) {
 Block block = chain.get(i);
 System.out.println(\Block #\ + i);
 System.out.println(\Data: \ + block.getData());
 System.out.println(\Hash: \ + block.getHash());
 System.out.println(\Previous Hash: \ + block.getPreviousHash());
 System.out.println(\Timestamp: \ + new Date(block.getTimestamp()));
 System.out.println();
 }
 }

 public List<Block> getChain() {
 return chain;
 }
}
```

## 3. Demo Application

```java
public class BlockchainDemo {

 public static void main(String[] args) {

 SimpleBlockchain blockchain = new SimpleBlockchain();

 System.out.println(\Mining block 1...\);

 Block block1 = new Block(\Alice sends 10 coins to Bob\, 
 blockchain.getLatestBlock().getHash());
 blockchain.addBlock(block1);

 System.out.println(\Mining block 2...\);

 Block block2 = new Block(\Bob sends 5 coins to Charlie\, 
 blockchain.getLatestBlock().getHash());
 blockchain.addBlock(block2);

 System.out.println(\Mining block 3...\);

 Block block3 = new Block(\Charlie sends 2 coins to Alice\, 
 blockchain.getLatestBlock().getHash());
 blockchain.addBlock(block3);

 System.out.println(\\\nBlockchain:\);

 blockchain.printBlockchain();

 System.out.println(\Is blockchain valid? \ + blockchain.isChainValid());
 
 // Demonstrate tampering detection
 System.out.println(\\\nAttempting to tamper with block 1...\);
 blockchain.getChain().get(1).data = \Alice sends 1000 coins to Bob\;

 System.out.println(\Is blockchain valid after tampering? \ + blockchain.isChainValid());
 }
}
```

## Key Concepts -

## Cryptographic Hashing

Each block’s hash is calculated using SHA-256, creating a unique fingerprint. Any change to the block’s data completely changes its hash, making tampering detectable.

## Proof of Work

The mining process requires finding a hash that starts with a certain number of zeros. This computational work makes the blockchain secure against attacks, as an attacker would need enormous computational power to rewrite the chain.

## Chain Integrity

Each block references the previous block’s hash, creating an unbreakable chain. If someone tries to modify a block, they’d need to recalculate all subsequent blocks, which becomes computationally infeasible.

## Real-World Applications

This simple blockchain demonstrates the core principles used in:

- Cryptocurrencies (Bitcoin, Ethereum)
- Supply chain tracking
- Digital identity verification
- Smart contracts
- Decentralized voting systems

## Performance Considerations

In production systems, you’d want to add:

- Merkle trees for efficient transaction verification
- Network protocols for distributed consensus
- Database persistence
- Transaction pools and validation
- More sophisticated mining algorithms

This implementation shows how a few hundred lines of Java can create the foundation of revolutionary technology that’s reshaping finance, governance, and data management worldwide.

I hope you learnt something new from this!! Thank you. More articles on learning using simple Java —\n;