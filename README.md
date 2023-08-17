# Block query contract for zkSync Era

## Background: zkSync Era block.number

Till the time this readme is written, zkSync Era is returning the L1 Batch number for the contract method `block.number`. According to zkSync, this is due to the expensive Merkle Tree prove for L2 Blocks. (See https://era.zksync.io/docs/reference/concepts/blocks.html#block-number-and-timestamp-considerations)

This, however, is going to change soon. zkSync announced that they will return L2 Block number for the contract method `block.number`. Since L1 Batch is much less than L2 Block, zkSync will produce some virtual blocks which has shorter block time than the L2 Blocks. By returning these virtual blocks values, the method `block.number` would eventually be able to catch-up from the L1 Batch number to L2 Block number. (See https://github.com/zkSync-Community-Hub/zkync-developers/discussions/87)

## Main issue: no RPC method to get virtual block info

This catch-up period might affect pre-deployed contracts which used the `block.number` method. For example, some DEX might calculate yield by block/timestamp, or some contracts might need an expiration timelock. 

During the catch-up period, if one would like to perform calculation with current on-chain block information, there is no RPC method for him/her to retrive the virtual block information. Thus, the simplest way would be deploying a contract which return these values in a `view` function. 

## How to use

#### Addresses
```
zkSync Era Testnet (coupled with Goerli)
0x5f4dA089753b18728eFa2c5298780681C49bB52B

zkSync Era Mainnet
0xD2E4AFb120be9e2B51C0F95bf47604623F28C3bb
```

#### ABI


#### Get functions

Pre-requisite: Understanding how to use zkSync Era and other web3 connectors (https://era.zksync.io/docs/api/js/providers.html)

Javascript example:

```
// Connect to network
const provider = new Provider(...);
const signer = new ethers.Wallet(...);

// Connect to query contract
const queryContract = new ethers.Contract(address, abi, signer);

// Query block number
const blockNo = await queryContract.getBlockNumber();
console.log(`Current block.number on zkSync Era is ${blockNo}`);

// Query block timestamp
const blockTimestamp = await queryContract.getBlockTimestamp();
console.log(`Current block.timestamp on zkSync Era is ${blockTimestamp}`);

// Query block hash (the hash of the last mined block)
const blockHash = await queryContract.getBlockHash();
console.log(`Current blockhash() on zkSync Era is ${blockHash}`);

// Get everything in 1 query
const blockInfo = await queryContract.getBlockInfo();
console.log(`Block number: ${blockInfo[0]});
console.log(`Block timestamp: ${blockInfo[1]}`);
console.log(`Block hash: ${blockInfo[2]}`);
```
