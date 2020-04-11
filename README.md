<h1>Plan of Course:</h1>

<b>Week 1</b>
1. Introduce yourselves!
2. I'm Abhinav
3. Install all needed tools
4. Introduction to Blockchain and DeFi
5. Introduction to Ethereum and Solidity

<b>Week 2</b>
1. Your first Solidity program
2. Usecases for using Blockchain
3. Good coding habits and reproducibility

<b>Week 3</b>
1. Writing Smart Contracts
2. Using Web3 and Truffle
3. Working with Ganache
4. What is MetaMask



<hr>
## Footer notes from previous talks

1. **Ganache** <a href="https://www.trufflesuite.com/ganache">(Download)</a>: Tool to help you host your local blockchains. Think of it as IIS or XAMP/LAMP.
2. **Truffle**:  ```npm install truffle -g``` to install Truffle. It is used to automate development tasks like adding a block to your blockchain, migrating all changes, testing etc. Think of it as manage.py from Django.
3. **Web3** ( ```npm install web3``` ): Used to talk to your Blockchain </div>

<h1>Important Links</h1>

[The Presentation](https://docs.google.com/presentation/d/1FJQxUsBW0Z_ou41Iquwc0x27BtEsywL5zjsJxfSvRdc/edit?usp=sharing)

[The best explaination of Blockchain. Ever.](https://www.youtube.com/watch?v=SSo_EIwHSd4)

[The development introduction](https://www.youtube.com/watch?v=nvw27RCTaEw&t=7415s)

<hr>

_______________

<h1>Develop your first blockchain</h1>

Understand the basics.

Pre-requisites: Some rudimentary knowledge of OOPs in JavaScript.

We are using Node for this exercise. You could use Python or Go for the same, but I am using this since I am going to show you how to integrate this with your Node app. I take **heavy** inspiration from [Savjee](https://www.youtube.com/channel/UCnxrdFPXJMeHru_b4Q_vTPQ), I am using his series on blockchain as inspiration and putting my best understanding out there.

If you don't have Node, feel free to install it from your favorite package manager or the internet. A little bit about Node first: Node is JavaScript outside of your browser. You don't need *script* tags to run this JS, all you need to do is-

```powershell
node filename.js
```

This is because some madlad took Chrome's V8 engine out of Chrome and wrapped it in C and served us Node. Talk about [*Jugaad](https://en.wikipedia.org/wiki/Jugaad).*

The first thing you'll need to do is make a new directory and CD into that. Then you need to init an NPM package, and create an index.js file.

```powershell
mkdir blockchain-example
cd blockchain-example
npm init -y
vi index.js
```

I am using Vim because I am terribly lazy, feel free to use any text editor of choice. Now let's talk a little bit about what a blockchain *is* before we delve further into code. The best explanation I have read is [this article](https://ihodl.com/tutorials/2017-09-04/what-blockchain-laymans-terms/) by IHODL.

To quote from their website-

> Blockchain is a distributed database existing on multiple computers at the same time. It is constantly growing as new sets of recordings or blocks, are added to it.
>
> Each block contains a timestamp and a link to the previous block, so they actually form a chain.
>
> The database is not managed by any particular body; instead, everyone in the network gets a copy of the whole database.
>
> All blocks are encrypted in a special way, so everyone can have access to all the information but only a user who owns a special cryptographic key is able to add a new record to a particular chain.
>
> In addition, cryptography is used to guarantee synchronization of copies of the blockchain on each computer (or node) in the network.
>
> Read more at <a href="https://ihodl.com/tutorials/2017-09-04/what-blockchain-laymans-terms/">their website.</a>

![[https://www.researchgate.net/figure/Simple-example-of-blockchain-technology_fig4_325486515](https://www.researchgate.net/figure/Simple-example-of-blockchain-technology_fig4_325486515)](https://cdn-images-1.medium.com/max/2000/1*r6rAN-xe-bnH-ouc2EebOw.png)*[https://www.researchgate.net/figure/Simple-example-of-blockchain-technology_fig4_325486515](https://www.researchgate.net/figure/Simple-example-of-blockchain-technology_fig4_325486515)*

Look at the diagram above. All transactions are stored in Blocks, so every one knows what data is being transacted. Now before you are up in arms about security, this data is not open for everyone to see (It is in very few cases!). They are hashed, as we will see. You can only verify this transaction, verifying means that they can't be tampered with. For simplicity, these transactions are shown as currency here, but they could be any data.

Let's jump into code and from there explain a little here and there?

```javascript
const sha256 = require('sha256'); 

class Block {  
constructor(index, timestamp, data, prevHash) {    
this.index = index;    
this.timestamp = timestamp;    
this.data = data;    
this.prevHash = prevHash;    
this.thisHash = sha256(      this.index + this.timestamp + this.data + this.prevHash    );  
}}
```

Do you understand what happened? We made a goddamn block. This is the fundamental logic behind every blockchain unicorn founder that got rich. Pay attention.

First, we import SHA256, it's an encryption algorithm. As I said, it's to encrypt the data.

The class *BLOCK* is a block template we will use for every block.

Each block will have five elements: index, timestamp, data, Previous Hash and Present Hash.

For CryptoCurrencies (esp the earlier ones like Bitcoin), the data is the address of the sender, receiver of bitcoin and amount of Bitcoin transacted.

Now that you know what One block looks like, I am sure you have a better understanding. Let's move on to what would we do about the first block, right? Since our first block has no Previous Hash!

We create something called the *Genesis Block.*

All of this will come under a class called *blockChain*.

    const createGenesisBlock = () => new Block(0, Date.now(), 'Genesis Block', '0');

What it do? Simple. Create an instance of the class with 0 as the index, present date as date, 'Genesis Block' as data (You can keep this as anything as long as you remember what your initial block is named) and 0 as the previous hash.

We will need two functions: getLatestBlock() and calculateHash().

    getLatestBlock() {
    return this.chain[this.chain.length - 1];}


    calculateHash() {
    return SHA256(this.index + this.previousHash + this.timestamp + JSON.stringify(this.data)).toString();}

getLatestBlock() fetches the latest block from the chain. Out of context it might be confusing, check the GitHub, you'll get a better understanding.

calculateHash() calculates the Hash, we have talked about this before.

Now let's create an addBlock function.

    addBlock(*newBlock*) {
    
    newBlock.previousHash = this.getLatestBlock().hash;
    
    newBlock.hash = newBlock.calculateHash();
    
    this.chain.push(newBlock);
    
    }

This function takes in a parameter called newBlock, which is basically the block we will be adding. To use this function we will run it something like this:

    let bkc_app=new blockChain();
    bkc_app.addBlock(new Block(index, Date(), data))

Now addBlock will take that block we need to add as parameter, add a previousHash to it by getting the hash of the latestBlock, adds a present Hash to it by doing a calculateHash() on it and pushes it to the chain.

Yay, the simplest blockchain in the world is now ready!

Let's take it for a spin.

    let bkc_app= new Blockchain();
    
    data = {
    'story':'Quick Brown Fox'
    }
    
    bkc_app.addBlock(new Block(1, Date(), data))
    
    console.log(bkc_app, JSON.stringify(khbr, null, 4))
    

Here's the output:

```JSON
Blockchain {
  chain: [
    Block {
      index: 0,
      timestamp: '01/01/2017',
      data: 'Genesis block',
      previousHash: '0',
      hash: ''
    },
    Block {
      index: 1,
      timestamp: 'Wed Dec 18 2019 23:50:02 GMT+0530 (India Standard Time)',
      data: [Object],
      previousHash: '',
      hash: '976c639156690751d29cd04b829e8f6448cf141d064e2149adf9d08253b6d759'
    }
  ]
} {
    "chain": [
        {
            "index": 0,
            "timestamp": "01/01/2017",
            "data": "Genesis block",
            "previousHash": "0",
            "hash": ""
        },
        {
            "index": 1,
            "timestamp": "Wed Dec 18 2019 23:50:02 GMT+0530 (India Standard Time)",
            "data": {
                "link": "Quick Brown Fox"
            },
            "previousHash": "",
            "hash": "976c639156690751d29cd04b829e8f6448cf141d064e2149adf9d08253b6d759"
        }
    ]
}

```

Get the Code <a href="">here</a>.

___________

<h1>Proof of Work and how to implement it</h1>

**What is Proof of Work (PoW) ?**

A Proof of work is an easy to verify, but hard to generate data. Producing a PoW can be a random process with low probability of success. A lot of trial and error is required to a PoW. In this chapter I will go over what it is, why is it and how is it implemented. I will also go over **Proof-Of-Stake**, since it is a very important topic.

In classical computing, a PoW is a consensus mechanism. It helps deter DoS Attacks and any misuse of automation or bulk requests, such as spam. Before I explain PoW, Let me talk about Consensus in Computer Science. In Blockchain, we have tons of consensus algorithms. If a new block is to be added to the blockchain, Consensus Algorithms decides where will this block be added. 

In a centralized system, every node accepts the center as an authority. In a decentralized network, there is no centralized authority. The mutual distrust is an obstacle to consensus. This is called the Byzantine Fault, which is an abstract analogy to this problem of distrust. In Byzantine's General problem: Assume there are some few generals separated by distance, and have to coordinate on a battle plan. So they must all share the battle plans, but there can be one malicious general (traitor!) who will mess with the battle plan.

In Bitcoins, every node is transparent in their transaction history to one another. To make a transaction, a node must undergo a process to produce a proof of work. PoW was initially developed as a protocol against email spam. To send an email, you must perform some computation on your system. This is different from CAPTCHA, which are easily solvable by humans. PoW problems aren't. Blockchains are append only, so we have to be sure that we are adding the correct block to the chain.

So in Bitcoin, it works something like this:

- One node has to add a new block to get a fraction of bitcoin
- But we don't want too many nodes doing this
- So we want a node to do some work before we accept the block
- We give it a hashed output and allow the computer to do the guessing
- Once we do this, we expect all nodes to compete
- They provide a solution which is super easy to verify in Polynomial time
- This solution is passed to neighbor nodes, if verified it moves forward. 
- Kind of like a tree structure
- The winner (miner) takes the block
- More miners there are, more the difficulty of the problems

 

**The Obvious Drawbacks**

We are in struggle to save electricity, time and resources, and mining wastes a ton of these resources because 

1. The winner is paid in bitcoins but:
2. The rest lost out on bitcoins AND used up computing space that could have been used for something better
3. The computing power is used up on something intangible, trivial and useless.

**Proof of Stake**

Proof of Stake or PoS abstracts the idea of actually computing away. The process of reaching consensus remains the same, but the method changes.  Unlike PoW, in PoS you "stake" your money in the network, and possibly bad behavior results in a loss of your stake.

**Now let us implement it!**

In our code we will add a new method called mineBlock.

```javascript
mineBlock(difficulty)
    {
        while(this.hash.substring(0, difficulty)!= Array(difficulty+1).join("0"))
        {
            this.nonce++; //don't worry about this value now, I will explain
            this.hash=this.calculateHash();
        }

        console.log("Block Mined:"+this.hash);
    }

```

So what's happening here?

Bitcoin implements proof-of-work by requiring that the hash of a block starts with a specific number of zero's. This is also called the difficulty. Put that aside for now, and focus on how a block is added to the blockchain in real-life in BitCoins.

- A block is just a transaction detail
- There are certain X amount of BTC in existence
- And Y number to be mined (Where Y>X)
- That means a certain number of computational problems are provided so that you can solve them
- Give a PoW and add to a block that "I mined and this is my PoW and this is what I claim"
- Neighboring nodes verify your claims

[Rest Coming Soon]

