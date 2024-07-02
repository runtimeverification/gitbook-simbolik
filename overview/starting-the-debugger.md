# Starting the Debugger

This guide walks you through the steps to start your first debugging session. We assume you have already set up Simbolik according to the [Getting Started](getting-started.md) guide.

First, you need a Solidity project to debug. We recommend starting with our [example project](https://github.com/runtimeverification/simbolik-examples), which is well-tested and known to work with Simbolik.

Open the `test/DebugERC20.sol` file in the VSCode code. A `▷ Debug` button will appear over all [debuggable functions](starting-the-debugger.md#debuggable-functions):

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

Click the button to start debugging:

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Hooray 🥳🥳🥳 You can now set breakpoints, step through the code, and inspect your variables!

### Debuggable Functions

Simbolik has certain restrictions about the functions you can debug. By default, it only allows you to debug parameterless public and external functions of contracts without constructor parameters. This is a soft limitation and does not mean you cannot debug internal functions or functions with parameters - but it requires you to write a little extra code. Here are some examples of how to debug functions that don't fall under the aforementioned restrictions:

#### How to debug functions with parameters?

Let's assume you want to debug a function `Greeter.greet(string memory phrase)`. This function takes a parameter, so the `▷ Debug` button will not appear. Simbolik does not know the value you will pass to the greet function. The simplest way to tell Simbolik is by defining a new entry point smart contract and a new debuggable function that calls the original greet function, passing down the parameters:

```
contract DebugGreeter is Greeter {
    function debug_greet() external {
        greet("Hello World!");
    }
}
```

The wrapping function takes no parameters, so the `▷ Debug` will show up and can jump right into it.

Notice that our example project uses the same pattern to make the `BuggyERC20._mint(address,uint256)` function debuggable: We cannot debug it directly because it has some parameters. Instead, the example project defines a new contract `DebugERC20` inheriting from `BuggyERC20` and a new function `debug_mint` that calls `_mint(ALICE, 1000)`.&#x20;

#### How to debug internal functions?

Internal functions cannot be called directly from a transaction, but we can use the same pattern as above to make them debuggable. We define a new entry point smart contract and a new external function calling the internal function:

```
contract DebugContract is MyContract {

   function debug_my_internal_function() external {
       my_internal_function();
   }
}
```

The `DebugERC20.debug_mint()` function of the example project is another good example of this pattern.

#### How to debug private functions?

We're afraid debugging private functions is currently not supported.

#### How to debug contracts with constructor parameters?

When your smart contract takes constructor arguments, Simbolik must know the values used to deploy it. The pattern is the same as above: We define a new entry-point debugging contract that inherits from the original contract, fixing all deployment parameters of the inherited contract.

```
contract DebugContact is MyContract("Hello, World") {

   function debug_my_function() external {
       my_function();
   }

}
```

Instead of inheriting, you can also deploy the original contract inside the constructor of the entry point smart contract.

```
contract DebugContract {

    MyContract myContract;

    constructor() {
        myContract = new MyContract("Hello, World!");    
    }

    function debug_my_function() external {
        myContract.my_function();
    }

}
```

#### How to debug multi-contract systems?

Most real-world smart contracts do not operate in isolation but depend on other smart contracts to operate. Let's assume you have two contracts `ContractA` and `ContractB,` where `ContractB` depends on `ContractA`.

The pattern is the same as above: Define a new entry point `DebugContract` and a new debuggable function:

```
contract DebugContract {

    MyContractA myContractA;
    MyContractB myContractB;

    constructor() {
        myContractA = new MyContractA();
        myContractB = new MyContractB(address(myContractA));    
    }
    
    function debug_my_function() {
        myContractB.my_function();
    }

}
```

#### Discussion

Writing extra Solidity code to debug your more complicated functions seems to be a bit inconvenient. However, we considered some alternatives and found this was the most convenient approach with some unexpected benefits.

First, we could have built a user interface to prompt the user for all inputs before deploying the smart contract and executing the function. The problem with this approach is that users would need to re-enter all the inputs after re-starting the debugger, and it wouldn't be possible to share your debugging sessions with your colleagues. Meanwhile, with the current approach, you can commit your debugging contracts, replay every session as often as you want, get the same deterministic behavior, and share them with your teammates. This is particularly useful, for instance, when writing proof-of-concepts for potential security vulnerabilities. Another problem with the graphical approach is the overhead it takes to set up more complex smart contract systems. With our current approach, we accept some additional costs upfront for writing the debugging smart contracts, but the time savings from one-click replays largely outweigh this cost.

Second, we could have built a custom format to set up debugging configurations instead of writing Solidity code. We decided against this approach because we think that Solidity's semantics are clear to Solidity devs, and a custom format would introduce another layer of complexity.&#x20;
