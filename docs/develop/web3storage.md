---
id: web3.storage
title: Web3.storage on Polygon 
description: Using Web3.storage and Polygon
keywords:
  - nft.storage
  - filecoin
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---
In the past, storing data on the decentralized web wasn’t always easy — but that’s where Web3.Storage comes in. Most data on the internet today is hosted by massive storage providers like Amazon, Google, and Microsoft. This makes it simpler for developers to store application data, but big corporate platforms like these create silos by locking developers and users alike into walled gardens of services. What’s more, as the market continues to consolidate, a small oligopoly of providers are essentially becoming the storage backbone of the internet.
One solution to this problem is using decentralized storage instead of big, corporate platforms to store data for apps and services. However, decentralized storage can be difficult to manage and add extra time and effort to an already crowded developer workflow — for example, most decentralized storage services need you to compile your data into a specific format, find a storage provider to host your data, buy some cryptocurrency to pay the storage provider, and then send your data across the internet. This is where Web3.Storage comes in.
With Web3.Storage, you get all the benefits of decentralized storage technologies with the frictionless experience you expect in a modern dev workflow. All you need to use Web3.Storage is an API token and your data. Under the hood, Web3.Storage is backed by the provable storage of Filecoin and makes data accessible to your users over the public IPFS network — but when it comes down to building your next application, service, or website, all you need to know is that Web3.Storage makes building on decentralized technologies simple. And best of all, Web3.Storage is free.
Web3.Storage Forum dApp example
This repository contains an example of a decentralized application that uses Web3.Storage and Ethereum to provide a simple online forum.
All post and comment content is stored on Filecoin and IPFS, while all ids and votes are stored in an Ethereum smart contract.

Install / Run
There’s no long-lived deployment of the app yet, so you’ll need to run in local development mode.
You’ll need a recent version of Node.js and npm version 7 or greater.
Clone this repo:git clone https://github.com/web3-storage/example-forum-dapp
Install dependencies: npm install
Run local blockchain and webapp server:
npm run dev
Leave the last command running and open your browser to http://localhost:3000.
Setting up MetaMask
To interact with the app, you’ll need an Ethereum wallet like MetaMask installed. You’ll also need to configure MetaMask to use the local HardHat development network.
To do that, open the MetaMask extension and go to Settings -> Network -> Localhost 8545 and change the Chain ID input to 31337.
Important: if you’ve made any write transactions, the next time you restart the local blockchain you’ll get an error about an incorrect nonce if you try to make a transaction on the new chain. This is because MetaMask still remembers the state of the old blockchain instance. To fix this, open MetaMask and go to Settings -> Advanced -> Reset Account and press the Reset Account button. This will not mess with your balances or any important state - it just clears out the stale history.
Getting play money
When running in local development mode, there will be a faucet link in the header of the app. Click the link to have a small amount of devnet ETH deposited into your MetaMask wallet. You'll need to do this before performing any write operations (e.g. posting, commenting, voting, etc).
App overview
The app consists of a smart contract and supporting TypeScript code for interacting with the blockchain, along with a single-page web app, written in React.
At a high level, the app works like this:
A Forum smart contract is deployed to an Ethereum network, which allows creating posts, commenting on them, and voting on posts and comments.
Items
Posts and comments are represented internally as Item structs:
/**
    * @notice Represents a single forum post or comment. 
    */
  struct Item {
    /// @notice what kind of item (post or comment)
    ItemKind kind;
    /// @notice Unique item id, assigned at creation time.
    uint256 id;
    /// @notice Id of parent item. Posts have parentId == 0.
    uint256 parentId;
    /// @notice address of author.
    address author;
    /// @notice block number when item was submitted
    uint256 createdAtBlock;
    /// @notice ids of all child items, with oldest items at front.
    uint256[] childIds;
    /// @notice IPFS CID of item content.
    string contentCID;
  }
Each Item has a kind field that can be either ItemKind.POST or ItemKind.COMMENT. Comments link to their parent post (or comment) using the parentId field, and parents keep a list of their children in the childIds field. When a new child commment is added, the parent's childIds is updated to include the new child.
The content of the post or comment is stored as a JSON file in IPFS and Filecoin using Web3.Storage, and the file’s CID is stored in the contentCID field.
You can retrieve a comment or post with the getItem contract function, passing in the item's id. You then need to fetch the content from IPFS and parse the JSON in order to render the complete item.
Voting
Anyone can vote on a comment or post by calling either voteForItem(itemId, voteValue) where voteValue is -1 for a downvote, +1 for an upvote, or 0 to retract a previous vote.
Each account can only vote once for each post or comment — subsequent votes replace the previous vote from that account.
You can retrive the vote total for a post or comment with getItemScore(itemId), which returns the sum of all up and downvotes.
To get the “karma” or number of votes an author has recieved for their posts and comments, call getAuthorKarma(author), passing in the author's address.
See packages/contract for details about the smart contract. See packages/webapp for details about the user interface.

Youtube Video : https://www.youtube.com/watch?v=mL20IZpVPuQ

Github repo : https://github.com/web3-storage/example-forum-dapp