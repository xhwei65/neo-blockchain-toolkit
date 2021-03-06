<!-- markdownlint-enable -->
# Neo Blockchain Toolkit for .NET Quickstart

> Note, this quickstart has only been tested on Windows 10 and Ubuntu 18.04.

## Prerequisites

- [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.2)
- [Visual Studio Code (v1.37 or later)](https://code.visualstudio.com/Download)

Ubuntu users must also install libsnappy-dev and libc6-dev via apt-get in order
to use Neo-Express.

## Installation

For this quickstart, there are two [.NET Core global tools](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools)
and a template pack to install via the dotnet command line tool. There is also
a VSCode extension that needs to be installed manually. In addition to this
quickstart, there is also a 
[command reference document](https://github.com/neo-project/neo-debugger/blob/master/command-reference.md)
for Neo-Express.

Open a terminal window and execute the following commands to install the
tools via dotnet command line:

``` shell
dotnet tool install -g Neo.Express
dotnet tool install -g Neo.neon-de
dotnet new -i Neo.Contract.Templates
```

The VSCode smart contract debugger can be downloaded from the
[GitHub repo release page](https://github.com/neo-project/neo-debugger/releases).
As of this writing, the current release is
[v0.5.15](https://github.com/neo-project/neo-debugger/releases/tag/v0.5.15).
Download the associated .VSIX file for the latest release and follow the
[official VSCode documentation](https://code.visualstudio.com/docs/editor/extension-gallery#_install-from-a-vsix)
to install the .VSIX file.

> Note, this manual VSIX install process is specific to the early access preview.
> Eventually, this  packages will be distributed via the VSCode marketplace.

If this is the first time you've used
[.NET Core global tools](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools),
you will need to shutdown your terminal windows and reopen it. You may need to
log out completely and log back in again. Make sure the tools are correctly installed
and your path is correctly set up by running `neo-express --version` and `neon-de`.
The tools  should echo back the version number. It will look similar to this:

``` shell
$ neo-express --version
0.8.11+92c8779687

$ neon-de
Neo.Compiler.MSIL console app v2.4.1.1
need one param for DLL filename.
[--compatible] disable nep8 function and disable SyscallInteropHash
Example:neon abc.dll --compatible
```

## Create a new Smart Contract

In your terminal window, create an empty directory called "HelloWorld".
Change to that directory and invoke the `dotnet new neo-contract -de` command.
This will create a simple Neo smart contract that writes Hello World to
blockchain storage.

> Note, by default the generated neo-contract project compiles the smart contract
> with the official [neon compiler](https://github.com/neo-project/neo-devpack-dotnet).
> The `-de` option configures the generated project to use the "debugger enhancements"
> branch of the neon compiler instead. Eventually, the debugger enhancements will
> be merged into the official neon release. In the meantime, this branch of the
> neon compiler is needed in order to enable smart contracts to be debugged in VSCode.

## Build the Smart Contract

You can immediately build the smart contract via the `dotnet publish` command.
The result should look something like this:

``` shell
$ dotnet publish
Microsoft (R) Build Engine version 16.2.32702+c4012a063 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 294.77 ms for /home/harry/Source/HelloWorld/HelloWorld.csproj.
  HelloWorld -> /home/harry/Source/HelloWorld/bin/Debug/netstandard2.0/HelloWorld.dll
  HelloWorld -> /home/harry/Source/HelloWorld/bin/Debug/netstandard2.0/publish/
  Neo.Compiler.MSIL console app v2.4.1.1
  Find entrypoint:System.Void HelloWorld::Main()
  convert succ
  gen debug succ
  gen abi succ
  write:HelloWorld.avm
  write:HelloWorld.debug.json
  write:HelloWorld.abi.json
  SUCC
```

> Note, you have to use `dotnet publish` instead of `dotnet build`
> because of a [bug](https://github.com/neo-project/neo-devpack-dotnet/issues/78)
> in the NeoN compiler. Once this bug has been fixed, the templates
> will be updated to run as part of the build target instead of the
> publish target.

## Debug the Smart Contract

From the terminal window in your HelloWorld project directory, you can
launch VSCode by executing `code .`. This will bring an instance of
VSCode in the folder you created the smart contract in.

Before you can run the contract in the debugger, you need to create a
launch configuration. The Neo Contract Debugger makes this very easy.

- From the top-level Debug menu, select "Add Configuration"
- From the Select Environment input box, select "Neo Contract"

The Neo Contract Debugger will automatically create a launch profile
for every .AVM file it locates in the workspace. This launch
profile allows you to specify arguments for the entry-point method,
key/value pairs for emulated storage and the emulated behavior of
[Runtime.CheckWitness](https://docs.neo.org/docs/en-us/reference/scapi/fw/dotnet/neo/Runtime/CheckWitness.html).
You don't need to change anything to run the HelloWorld contract,
so simply hit "F5" or select Debug -> Start Debugging from the menu.
This will launch the HelloWorld contact for debugging. From here, the
following debug experiences work:

- You can Continue, Step Into, Step Over and Step In
- You can set breakpoints
- You can inspect the contents of emulated storage
- You can inspect the value of local parameters and variables.
  - Note, the Hello World contract doesn't have any local parameters
    or variables.

## Create and Run a Neo-Express blockchain

Now that we have a smart contract, we need a blockchain to deploy it to.
Neo-Express is a developer-focused Neo blockchain client. Create a new
Neo-Express blockchain with the `create` command.

``` shell
$ neo-express create
Created 1 node privatenet at /home/harry/Source/HelloWorld/default.neo-express.json
    Note: The private keys for the accounts in this file are are *not* encrypted.
          Do not use these accounts on MainNet or in any other system where security is a concern.
```

> Note the warning - Neo-Express does not encrypt private keys.

By default, Neo-Express creates a single node blockchain. For development purposes,
a single node blockchain is often preferred. Furthermore, the checkpoint features
of Neo-Express only work on a single node blockchain. You can create a four or
seven node blockchain with the --count option.

Neo-Express stores all the information about the blockchain instance in the
default.neo-express.json file. If you look in this file right now, you'll see
information about the single consensus node for this blockchain we created.

Now that you have created the Neo-Express blockchain instance, you can run it.
Since this is a single-node blockchain, you don't need to specify which node of
the blockchain to run. The run command will start the blockchain and continue
logging information about new blocks to the console until you shut down the node
via Ctrl-C.  

``` shell
$ neo-express run --seconds-per-block 1 
09:49:37.99 ConsensusService Info OnStart
09:49:38.08 ConsensusService Info initialize: height=1 view=0 index=0 role=Primary
09:49:38.15 ConsensusService Info timeout: height=1 view=0
09:49:38.15 ConsensusService Info send prepare request: height=1 view=0
09:49:38.23 ConsensusService Info send commit
09:49:38.32 ConsensusService Info relay block: height=1 hash=0x096aaa25191b8601856a0a1744b7f19b06807fd9888e247366eec3d212a507b6 tx=1
09:49:41.32 ConsensusService Info persist block: height=1 hash=0x096aaa25191b8601856a0a1744b7f19b06807fd9888e247366eec3d212a507b6 tx=1
09:49:41.32 ConsensusService Info initialize: height=2 view=0 index=0 role=Primary
09:49:42.33 ConsensusService Info timeout: height=2 view=0
09:49:42.33 ConsensusService Info send prepare request: height=2 view=0
09:49:42.34 ConsensusService Info send commit
09:49:42.35 ConsensusService Info relay block: height=2 hash=0x195f354baa79d5cf4f476748fadc82b0fa0ed5e584ec9bd9762e8948a799dbd0 tx=1
09:49:42.35 ConsensusService Info persist block: height=2 hash=0x195f354baa79d5cf4f476748fadc82b0fa0ed5e584ec9bd9762e8948a799dbd0 tx=1
...
```

You can control the block generation period via the --seconds-per-block option
(-s for short) of the run command. By default, Neo-Express generates a new block
every fifteen seconds, just like MainNet. However, for development purposes, it's
often desirable to run the blockchain faster than that. The block generation
period affects how quickly developers can view results of operations like transfer
and it affects how quickly accounts accumulate GAS.

## Manage Neo in the Neo-Express blockchain

Since this terminal window is running the blockchain, open another terminal
window in the same directory so you can interact with the running blockchain.
In the new terminal window, we will use Neo-Express to create a standard wallet
and transfer the genesis Neo tokens to that wallet.

``` shell
$ neo-express wallet create testWallet
testWallet
        AZDRabBcW4eESVfh9ssLd6udza9xpPn1P6
    Note: The private keys for the accounts in this wallet are *not* encrypted.
          Do not use these accounts on MainNet or in any other system where security is a concern.

$ neo-express transfer neo 100000000 genesis testWallet
{
  "contract-context": {
    "type": "Neo.Network.P2P.Payloads.ContractTransaction",
    "hex": "8000000149ea779c8d6e079781e0caf7d93fbf17091be411222ed01e2b196f95e4f6c0450000019b7cffdaa674beae0f930ebe6085af9093e5fe56b34a5c220ccdcf6efc336fc50000c16ff28623007ffaa84760679e63bf907612d36dd2be527a67f6",
    "items": {}
  },
  "script-hashes": [
    "Ab6ENUi6UpWg6cQGUE3DEh3zam9yTJir6s"
  ],
  "hash-data": "8000000149ea779c8d6e079781e0caf7d93fbf17091be411222ed01e2b196f95e4f6c0450000019b7cffdaa674beae0f930ebe6085af9093e5fe56b34a5c220ccdcf6efc336fc50000c16ff28623007ffaa84760679e63bf907612d36dd2be527a67f6"
}
{
  "txid": "0xc12e0fa17aafce2a00bc28d5680bd1624a76cba31bd8b3a67925dc103d546425"
}
```

> Note, currently Neo-Express dumps JSON information about operations to the
> console. A future update will display results in a more user-friendly fashion.

Neo-Express allows you to refer to wallet account by an easy-to-remember names
instead of by Base58 encoded addresses like `Ab6ENUi6UpWg6cQGUE3DEh3zam9yTJir6s`.
There are a few reserved names, such as 'genesis'. Genesis refers to the multi-
signature account that receives the genesis Neo created for every new blockchain.

We can see the result of our transfer via the show account command

``` shell
$ neo-express show account testWallet
{
  "version": 0,
  "script_hash": "0x1bc70eb0de305fc5f95f987023fc13677f12c3fa",
  "frozen": false,
  "votes": [],
  "balances": [
    {
      "asset": "0xc56f33fc6ecfcd0c225c4ab356fee59390af8560be0e930faebe74a6daff7c9b",
      "value": "100000000"
    }
  ]
}
```

> Note, you must wait for the next block to relay before you can see the results
> of the transfer. Running the blockchain faster than the fifteen second default
> means you don't have to wait as long for operations to complete!

## Claim GAS in the Neo-Express blockchain

Because we transferred all the genesis Neo and the blockchain is creating a new
block every second, GAS will build up pretty quickly in the testWallet account.
We can see how much GAS is available with the `show gas` command.

> Note, even at one block every second, it will take a few minutes to build up
> enough GAS to complete this quickstart. Now is a good time for a coffee break.

``` shell
$ neo-express show gas testWallet
{
  "unavailable": 1112,
  "available": 0
}
```

Wait until there is over 1000 GAS in the unavailable bucket - that will be plenty
to experiment with smart contract deployment and invocation. However, in order to
access the GAS, we need to execute another transfer, this time from and to the
testWallet account.

``` shell
$ neo-express transfer neo 100000000 testWallet testWallet
< transfer output omitted for clarity >

$ neo-express show gas testWallet
{
  "unavailable": 96,
  "available": 1408
}
```

The available GAS can be claimed with the `claim gas` command and we can see the
result with the `show account` command.

``` shell
$ neo-express claim gas testWallet
< claim gas output omitted for clarity >

$ neo-express show account testWallet
{
  "version": 0,
  "script_hash": "0xf6677a52bed26dd3127690bf639e676047a8fa7f",
  "frozen": false,
  "votes": [],
  "balances": [
    {
      "asset": "0xc56f33fc6ecfcd0c225c4ab356fee59390af8560be0e930faebe74a6daff7c9b",
      "value": "100000000"
    },
    {
      "asset": "0x602c79718b16e442de58778e148d0b1084e3b2dffd5de6b7b16cee7969282de7",
      "value": "1408"
    }
  ]
}
```

## Deploy a Smart Contract to Neo-Express blockchain

With a running Neo-Express blockchain and a standard wallet account with plenty
of GAS, we can deploy our smart contract to the blockchain. We start by importing
the contract into Neo-Express.

``` shell
$ neo-express contract import bin/Debug/netstandard2.0/publish/
Does this contract use storage? [y/N] y
Does this contract use dynamic invoke? [y/N] n
Is this contract payable? [y/N] n
```

Neo-Express needs to know if the contract uses storage, dynamic invoke or if
the contract is payable in order to deploy the contract.

> For Neo 3, this information will be in the smart contract manifest file.

The imported contract can now be deployed via the `contract deploy` command.
You must specify a wallet account to pay the deployment GAS price. You can get
information about deployed contracts via the `contract get` command. 

``` shell
$ neo-express contract deploy HelloWorld testWallet
{
  "contract-context": < omitted for clarity >
  "script-hashes": < omitted for clarity >
  "hash-data": < omitted for clarity >
  "engine-state": {
    "state": 1,
    "gas-consumed": "500",
    "result-stack": [
      {
        "type": "InteropInterface"
      }
    ]
  }
}
{
  "txid": "0x9211b9fbe258c1a5f00ef7db225fc8f00ae601fb953d522e26d9a6d8867b684b"
}

$ neo-express contract get HelloWorld
{
  "version": 0,
  "hash": "0x505d2e13ff4a4cf1c368ed2af7f7aad5720f4c38",
  "script": "00c56b616168048418d60d0548656c6c6f05576f726c64615272680452a141f561616c7566",
  "parameters": [],
  "returntype": "Void",
  "name": "Contract",
  "code_version": "0.0.0",
  "author": "No author provided",
  "email": "nobody@fake.email",
  "description": "No description provided",
  "properties": {
    "storage": true,
    "dynamic_invoke": false
  }
}
```

## Invoke a Smart Contract on the Neo-Express blockchain

The last step is to invoke the deployed contract. We use the `contract invoke`
command for that.

``` shell
$ neo-express contract invoke HelloWorld
{
  "engine-state": {
    "state": 1,
    "gas-consumed": "1.017",
    "result-stack": []
  }
}
```

> Note, the Hello World contract has no return value. If it did, we would see
> it in the result-stack property.

Invoking a smart contract without specifying a wallet account to pay the invocation
GAS cost will not modify the state of the blockchain. For the HelloWorld contract,
that means that nothing will be written to blockchain storage. If you wish for a
contract invocation to make durable changes, you can specify a wallet account to
pay the GAS cost via the --account argument.

``` shell
$ neo-express contract invoke HelloWorld --account testWallet
{
  "contract-context": < omitted for clarity >
  "script-hashes": < omitted for clarity >
  "hash-data": < omitted for clarity >
  "engine-state": {
    "state": 1,
    "gas-consumed": "1.017",
    "result-stack": []
  }
}
{
  "txid": "0x785346a3a338d70dd5bee6a70e1fc807a891d23a8d12d138b6a151b5eeae771e"
}
```

The HelloWorld contract takes no parameter and it returns no values. However,
it does modify contract storage in the blockchain. You can dump the storage
state for a given contract with the `contract storage` command. This command
lists all the key/value pairs in the blockchain, showing both keys and values
as both a hex-encoded byte array as well as a UTF-8 encoded string.

``` shell
$ neo-express contract storage helloWorld
0x48656c6c6f
  key (as string)   : Hello
  value (as bytes)  : 0x576f726c64
        (as string) : World
  constant value    : False
```
