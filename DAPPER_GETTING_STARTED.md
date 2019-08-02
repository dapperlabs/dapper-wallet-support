# Dapper - Getting Started

This page is an overview of important information you'll need to know to integrate Dapper into your hackathon project.

### Download the Sandbox Wallet Chrome Extension

We developed a special version of our wallet for developers to use.
The Dapper wallet takes care of Gas fees for you, so you can develop dApps without worrying about Gas!

The Sandbox version of Dapper is available for download here:
[https://chrome.google.com/webstore/detail/dapper-sandbox/ebcnjlgcglfhaoekkfideokgbemajipa?authuser=1]()

**Important**: The sandbox extension connects to the _*Rinkeby*_ Testnet only.

### Create an Infura Project

To access Rinkeby to deploy and test your Smart Contracts create a new Infura project: [https://infura.io/](https://infura.io/)

### Configure Truffle

Run this command from the root of your project to create Truffle configuration files.

```
npx truffle init && npm install truffle-hdwallet-provider
```

Be sure you set your new Infura project's secret key as an environment variable.
(Obtain the key from your Infura project dashboard).

Add the following config to the newly created `truffle.js`, assuming you've set `process.env.INFURA_SECRET` to be your Infura secret key:

```
const HDWalletProvider = require('truffle-hdwallet-provider');
// DOCS: https://github.com/trufflesuite/truffle-hdwallet-provider

module.exports = {
    networks: {
        rinkeby: {
            provider: () => new HDWalletProvider([process.env.INFURA_SECRET], `https://rinkeby.infura.io/v3/[INFURA_PROJECT_ID]`, 0, 1),
            network_id: 4,
            gas: 5500000,        // Rinkeby has a lower block limit than mainnet
            confirmations: 2,    // # of confs to wait between deployments. (default: 0)
            timeoutBlocks: 200,  // # of blocks before a deployment times out  (minimum/default: 50)
            skipDryRun: true     // Skip dry run before migrations? (default: false for public nets )
        }
    }
}

```

You're all set! To access Rinkeby run:

```
npx truffle console --network rinkeby
```

### Getting ETH from an ETH Faucet

The sandbox extension cannot recieve ETH directly from an Ether faucet. If you need ETH in your Dapper wallet (Dapper pays for your gas, so you may not), you'll need to send ETH to an Ethereum wallet account, and transfer it from that account into Dapper.

If you don't already have an Ethereum account that can recieve ETH from a faucet, create one using the Truffle console, first run:

```
npx truffle console --network rinkeby
```

And then:

```
> web3.eth.accounts.create()
```

Which will output something like:

```
{ address: '0x1dDe3e7b9Ad7C75837Bf9BFa63f3A9BDc16B8B39',
  privateKey:
   '0x974a8a4b5cd3ebff57fe04c0b8336823ab40cd4cda24d84b4c3943424cd51f07',
  signTransaction: [Function: signTransaction],
  sign: [Function: sign],
  encrypt: [Function: encrypt] }
```

Great! You've created a new Ethereum account. Now you can give the `address` from the above account to an Ether faucet.

After providing this `address` to a faucet, you'll have to wait a bit for the transfer transaction to complete. To check the balance of your new account use:

```
 > web3.eth.getBalance('0x...')
```

### Send ETH to your Dapper Wallet

If you haven't already, install the Dapper extension and sign up for a Dapper sandbox account.
Once you've installed and signed up for Dapper, you can find your wallet's address at the top of the extension window!

If you got ETH using the instructions above you can run the following command to transfer it into your Dapper wallet:

```
> web3.eth.sendTransaction({
    from: '0x... The account address created above',
    to: '0x... Your new Dapper address',
    value: '1000000000000000' // 1 ETH
})
```

Super, you're funded!

### Connecting your dApp to Dapper

The Dapper wallet was built to be backwards compatible with leagacy Web3 versions.

Not to worry, You can still use the latest Web3 features if you need to.

There are many ways you can use Web3 in your dApp. Here is one suggested method for using Dapper's legacy Web3 provider and the newest Web3 APIs together.

**Important**: look at this example carefully. You'll want to make sure that all transactions are carried out via the Dapper provider.

```
// Transactions will be signed and sent via your Dapper account.
const web3 = new Web3(window.ethereum);
const Contract = new web3.eth.Contract(contractJSON.abi, "0x... Deployed contract address");
const accounts = await window.ethereum.enable();

// Use Infura provider to listen for events from your contract!
const newestWeb3 = new Web3(new Web3.providers.WebsocketProvider("wss://rinkeby.infura.io/ws/v3/[INFURA_PROJECT_ID]"));
newestWeb3.eth.defaultAccount = accounts[0];
const eventSource = new newestWeb3.eth.Contract(contractJSON.abi, "0x... Deployed contract address");


return { web3, accounts, Contract, eventSource }
```

### TOKEN Support

Here's a list of all the tokens currently supported by the Dapper wallet.<br/>
https://github.com/dapperlabs/dapper-api/wiki/Tokens
