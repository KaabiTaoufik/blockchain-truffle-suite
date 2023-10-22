<p align="center">
  <a href="" rel="noopener">
 <img width=200px height=200px src="https://trufflesuite.com/img/tutorials/pet-shop/petshop.png" alt="Project logo"></a>
</p>

<h3 align="center">Pet Shop tracking</h3>

<p align="center"> Few lines describing your project.
    <br> 
</p>

## 📝 Table of Contents

- [Introduction](#introduction)
- [Getting Started](#getting_started)
- [Setting up the development environment](#setup)
- [Writing the smart contract](#writing_smart_contract)
- [Compiling and migrating the smart contract](#compile_migrate)
- [Testing](#testing)
- [Creating a user interface to interact with the smart contract](#create_GUI)
- [Interacting with the dapp in a browser](#interact_dapp)

## 🧐 Introduction <a name = "introduction"></a>

This project is to build a dapp using Ethereum for the first time following documentation provided here : https://trufflesuite.com/guides/pet-shop/.

## 🏁 Getting Started <a name = "getting_started"></a>

<h3> What you need to have installed :</h3>
<ul>
<li>Node.js v8+ LTS and npm (comes with Node)</li>
<li>Git</li>
<li ><a href="https://trufflesuite.com/ganache/"> Ganache </a></li>
<li ><a href="https://metamask.io"> MetaMask </a></li>
</ul>

Then we proceed with

```
npm install -g truffle
```

<p>Once truffle is installed (use <code>"truffle version"</code> to verify) we create a new folder where we will unbox the pet shop. </p>

## ✏️ Setup <a name = "setup"></a>

We will start by creating a new folder

```
mkdir pet-shop

cd pet-shop
```

Then inside the pet-shop folder

```
truffle unbox pet-shop
```

Result should be like this
![Alt text](/screenshots/truffle%20inbox%20result.png)
The default Truffle directory structure contains the following:

<ul> 
<li><code>contracts/</code>: Contains the 
<a href="https://solidity.readthedocs.io/">Solidity</a> 
source files for our smart contracts. There is an important contract in here called <code>Migrations.sol</code>, which we'll talk about later.
</li> 
<li><code>migrations/</code>: Truffle uses a migration system to handle smart contract deployments. A migration is an additional special smart contract that keeps track of changes.
</li> 
<li><code>test/</code>: Contains both JavaScript and Solidity tests for our smart contracts
</li> 
<li><code>truffle-config.js</code>: Truffle configuration file
</li> 
</ul>

## 📑 Writing the smart contract <a name = "writing_smart_contract"></a>

Explain how to run the automated tests for this system.

### Break down into end to end tests

We'll start our dapp by writing the smart contract that acts as the back-end logic and storage.

<ol> 
<li> <p>Create a new file named <code>Adoption.sol</code> in the <code>contracts/</code> directory.</p> </li> 
<li> 
<p>Add the following content to the file:</p></li> 
</ol>

```
pragma solidity ^0.5.0;

contract Adoption {
address[16] public adopters;
}
```

Notice that we are creating an adress which is Solidty's unique type.
Addresses are Ethereum addresses, stored as 20 byte values. Every account and smart contract on the Ethereum blockchain has an address and can send and receive Ether to and from this address.
This is an array of Ethereum addresses. Arrays contain one type and can have a fixed or variable length. In this case the type is address and the length is 16.
Public variables have automatic getter methods, but in the case of arrays a key is required and will only return a single value. Later, we'll write a function to return the whole array for use in our UI.

### Adopting a pet function

Add the following function to the smart contract after the variable declaration we set up above.

```
// Adopting a pet
function adopt(uint petId) public returns (uint) {
  require(petId >= 0 && petId <= 15);

  adopters[petId] = msg.sender;

  return petId;
}
```

![Alt Text](/screenshots/adding%20function%20to%20adopt.png)

### Retrieving the adopters function

Add the following function to the smart contract after the adopt() declaration we set up above.

```
// Retrieving the adopters
function getAdopters() public view returns (address[16] memory) {
  return adopters;
}
```

![Alt Text](/screenshots/adding%20to%20retrieve%20adopters.png)

## 🏗️ Compiling and migrating the smart contract <a name="compile_migrate"></a>

## Compilation

Solidity is a compiled language, meaning we need to compile our Solidity to bytecode for the Ethereum Virtual Machine (EVM) to execute. Think of it as translating our human-readable Solidity into something the EVM understands.
Run this code in the root directory

```
truffle compile
```

![Alt text](/screenshots/truffle%20compile%20result.png)

## Migration

Now that we've successfully compiled our contracts, it's time to migrate them to the blockchain!

<ol>
<li>Create a new file named 2_deploy_contracts.js in the migrations/ directory.</li>
<li> add the following content to it </li>
</ol>

```
var Adoption = artifacts.require("Adoption");

module.exports = function(deployer) {
  deployer.deploy(Adoption);
};
```

Before we can migrate our contract to the blockchain, we need to have a blockchain running. For this tutorial, we're going to use <a href="/ganache">Ganache</a>, a personal blockchain for Ethereum development you can use to deploy contracts, develop applications, and run tests. If you haven't already, <a href="/ganache">download Ganache</a> and double click the icon to launch the application. This will generate a blockchain running locally on port 7545.

You should see a GUI similar to this:
![Alt Text](/screenshots/garnache%20GUI.png)

now we can run

```
truffle migrate
```

Similar output expected:

![Alt Text](/screenshots/truffle%20mgirate%201.png)
Deploying first migration:
![Alt Text](/screenshots/truffle%20migrate%202.png)
Deploying second migration we created:
![Alt Text](/screenshots/truffle%20migrate%203.png)

We should see that now we have 4 current blocks and the first adresse has 4 transactions:

![Alt Text](/screenshots/block%20after%20migration.png)
You've now written your first smart contract and deployed it to a locally running blockchain. It's time to interact with our smart contract now to make sure it does what we want.

## 🚀 Testing <a name = "testing"></a>

- Create a new file named TestAdoption.sol in the test/ directory.

- Add the following content to the TestAdoption.sol file:

```
pragma solidity ^0.5.0;

import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/Adoption.sol";

contract TestAdoption {
  // The address of the adoption contract to be tested
  Adoption adoption = Adoption(DeployedAddresses.Adoption());

  // The id of the pet that will be used for testing
  uint expectedPetId = 8;

  //The expected owner of adopted pet is this contract
  address expectedAdopter = address(this);

}
```

- we will add these tests:

```

    // Testing the adopt() function
    function testUserCanAdoptPet() public {
      uint returnedId = adoption.adopt(expectedPetId);

      Assert.equal(returnedId, expectedPetId, "Adoption of the expected pet should match what is returned.");
    }
    // Testing retrieval of a single pet's owner
    function testGetAdopterAddressByPetId() public {
      address adopter = adoption.adopters(expectedPetId);

      Assert.equal(adopter, expectedAdopter, "Owner of the expected pet should be this contract");
    }
    // Testing retrieval of all pet owners
    function testGetAdopterAddressByPetIdInArray() public {
      // Store adopters in memory rather than contract's storage
      address[16] memory adopters = adoption.getAdopters();

      Assert.equal(adopters[expectedPetId], expectedAdopter, "Owner of the expected pet should be this contract");
    }
```

## To run the tests

- Create a new file named testAdoption.test.js in the test/ directory.
- Add the following content to the testAdoption.test.js file:

```
const Adoption = artifacts.require("Adoption");

contract("Adoption", (accounts) => {
 let adoption;
 let expectedPetId;

 before(async () => {
     adoption = await Adoption.deployed();
 });

 describe("adopting a pet and retrieving account addresses", async () => {
   before("adopt a pet using accounts[0]", async () => {
     await adoption.adopt(8, { from: accounts[0] });
     expectedAdopter = accounts[0];
   });
 });

 describe("adopting a pet and retrieving account addresses", async () => {
   before("adopt a pet using accounts[0]", async () => {
     await adoption.adopt(8, { from: accounts[0] });
     expectedAdopter = accounts[0];
   });

   it("can fetch the address of an owner by pet id", async () => {
     const adopter = await adoption.adopters(8);
     assert.equal(adopter, expectedAdopter, "The owner of the adopted pet should be the first account.");
   });

   it("can fetch the collection of all pet owners' addresses", async () => {
       const adopters = await adoption.getAdopters();
       assert.equal(adopters[8], expectedAdopter, "The owner of the adopted pet should be in the collection.");
     });

 });
});
```

- now we use:

```
truffle test
```

If everything is correct you should see:
![Alt text](/screenshots/truffle%20test%20results.png)

## 👨🏿‍💻✍️ Creating a user interface to interact with the smart contract <a name = "create_gui"></a>

- first modify the Replace me in initWeb3 inside /src/js/app.js
  with

```
// Modern dapp browsers...
if (window.ethereum) {
  App.web3Provider = window.ethereum;
  try {
    // Request account access
    await window.ethereum.enable();
  } catch (error) {
    // User denied account access...
    console.error("User denied account access")
  }
}
// Legacy dapp browsers...
else if (window.web3) {
  App.web3Provider = window.web3.currentProvider;
}
// If no injected web3 instance is detected, fall back to Ganache
else {
  App.web3Provider = new Web3.providers.HttpProvider('http://localhost:7545');
}
web3 = new Web3(App.web3Provider);
```

- And also initContract with :

```
$.getJSON('Adoption.json', function(data) {
  // Get the necessary contract artifact file and instantiate it with @truffle/contract
  var AdoptionArtifact = data;
  App.contracts.Adoption = TruffleContract(AdoptionArtifact);

  // Set the provider for our contract
  App.contracts.Adoption.setProvider(App.web3Provider);

  // Use our contract to retrieve and mark the adopted pets
  return App.markAdopted();
});
```

- markAdopted  with :
```
var adoptionInstance;

App.contracts.Adoption.deployed().then(function(instance) {
  adoptionInstance = instance;

  return adoptionInstance.getAdopters.call();
}).then(function(adopters) {
  for (i = 0; i < adopters.length; i++) {
    if (adopters[i] !== '0x0000000000000000000000000000000000000000') {
      $('.panel-pet').eq(i).find('button').text('Success').attr('disabled', true);
    }
  }
}).catch(function(err) {
  console.log(err.message);
});
```
- handleAdopt with:

```
var adoptionInstance;

web3.eth.getAccounts(function(error, accounts) {
  if (error) {
    console.log(error);
  }

  var account = accounts[0];

  App.contracts.Adoption.deployed().then(function(instance) {
    adoptionInstance = instance;

    // Execute adopt as a transaction by sending account
    return adoptionInstance.adopt(petId, {from: account});
  }).then(function(result) {
    return App.markAdopted();
  }).catch(function(err) {
    console.log(err.message);
  });
});
```
## 🎉 Interacting with the dapp in a browser <a name = "interact_dapp"></a>

- Setup your MetaMask wallet
- Adding Garnache as a network:
![Alt text](/screenshots/adding%20garnache%20to%20metamask.png)

## Using the dapp

- Run the command
```
npm run dev
```
You'll be met with this web app:
![Alt Text](/screenshots/web3%20web.png)

Also you should be connected:
![Alt Text](/screenshots/account%20connected.png)