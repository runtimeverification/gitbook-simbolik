# Troubleshooting

## VSCode extension installation

The VSCode extension lets you configure the paths to Simbolik, Anvil and Forge in the extension settings. It can be the case that the execution environment of the VSCode task and the command line shell are different, causing some `command not found` errors.  These extension settings help VSCode locate the binaries if it cannot find them on the `PATH`.

### Anvil terminated unexpectedly

If you are getting the following error:&#x20;

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

There are two main reasons for this error message: First, an Anvil node is already running on the default port, for example because it was launched by another VSCode window. In this case, debugging still works and you can ignore the error message. Second, VSCode cannot find `anvil` on the `PATH`. Then you can take action. Please, first make sure you have `anvil` installed:

```
$ which anvil 
/home/user/.foundry/bin/anvil
```

Copy the anvil location. Then `Open Settings` and paste the anvil location into the anvil-path setting.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Finally, `Try Again`, or reload the VSCode window, and it should be working!



### Failed to build project

If VSCode is not able to find `forge` and you get a `command not found` error when trying to debug a function:

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Please make sure you have forge installed and copy the path of the forge binary:

```
$ which forge 
/home/user/.foundry/bin/forge
```

Then, `Open Settings` and paste the forge location into the forge-path setting.&#x20;

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Then try to Debug the function again and it should be working.&#x20;
