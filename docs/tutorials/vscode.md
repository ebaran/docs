
---
title: Using VS Code
---

Time Estimate: 15 Minutes

Level: Beginner

# Summary
Being able to debug is a great tool when learning new programming content. This tutorial will facilitate how to debug using Visual Studio (VS) Code, which can be used with all the code samples on the [Algorand Developer Docs Portal](https://developer.algorand.org/docs/). Other debuggers can also be used. If you are new to debugging, VS Code is the most popular tool. It supports many programming languages, is quick to load and has a [wealth of documentation and extensions](https://code.visualstudio.com/Docs).

# Requirements

For JavaScript debugging:

1) Install [Node.js](https://nodejs.org/en/) 

2) The Chrome debugger extension requires you to be serving your web application from a local web server, which is started from either a VS Code task or your command-line. This extension is not required if just debugging a stand alone JavaScript file. 

# Background

VS Code is available on all major platforms. VS Code can take on most of the tasks of an Integrated Development Environment (IDE) with the right configuration and plugin library extension. [VS Code is open source](https://github.com/Microsoft/vscode).

# Main Languages

Extensions are available in most development languages for VS Code. Specific install instuctions are in Step 2 below. For general information see: [Java](https://code.visualstudio.com/docs/languages/java), [JavaScript](https://code.visualstudio.com/docs/languages/javascript), [Go](https://code.visualstudio.com/docs/languages/go), [Python](https://code.visualstudio.com/docs/languages/python) and [C#](https://code.visualstudio.com/docs/languages/csharp). This tutorial walks through JavaScript examples.

# Additional Languages

See extensions [for VS Code languages here ](https://code.visualstudio.com/docs/languages/overview).

For documentation, several other extensions are available including [Grammarly](https://marketplace.visualstudio.com/items?itemName=znck.grammarly) and [MarkDown (MD)](https://code.visualstudio.com/docs/languages/markdown). 


# VS Code
Visual Studio Code is a lightweight but powerful source code editor which runs on your desktop and is available for Windows, macOS and Linux. It comes with built-in support for JavaScript, TypeScript and Node.js and has a rich ecosystem of extensions for other languages (such as C++, C#, Java, Python, PHP, Go) and runtimes (such as .NET and Unity). 

## 1. Download VS Code
Download VS Code from this site https://code.visualstudio.com/Download

## 2. Install Extensions

Open VS Code. Go to the Extensions view.
Filter the extensions list by typing your programming language of choice, such as Java,  and you will see all related extensions.

![Filter Extensions](../imgs/VSCode-00.png)

Figure 2-1 Java Extension Pack

Java:

To help set up Java on VS Code, search for [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack). This contains the most popular extensions for most Java developers:

* Language Support for Java(TM) by Red Hat
* Debugger for Java
* Java Test Runner
* Maven for Java
* Java Dependency Viewer
* Visual Studio IntelliCode

JavaScript:

Visual Studio Code includes built-in JavaScript IntelliSense, debugging, formatting, code navigation, refactorings, and many other advanced language features.

Python: Install [Microsoft's Python Extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

Go: Install [Microsoft's Go Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.Go)

C#: Install [Microsoft's C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)


## 3. Debugging with VS Code

If you are interested in setting up debugging in VS Code read this:
<https://code.visualstudio.com/docs/editor/debugging>

Keyboard shortcuts and Keymap extensions, which match other editors, are available here:
<https://code.visualstudio.com/docs/getstarted/keybindings>

## 4. Debugging JavaScript with VS Code samples
 
In this step, setting up a debug session for a JavaScript code is covered for both a standalone code file as well as through a browser. The steps are similar for all languages. 

**Task 4-1 Standalone**

A) Select the Debug icon on the left 

B) Select from the drop-down menu to Add Config (for current folder)...

![Select Debug and Add Config](../imgs/VSCode-01.png)

Figure 4-1 - Select Debug and Add Config

Select the desired debug method. Here the section is set to Node.js: Launch Program. 

![Node.js: Launch Program](../imgs/VSCode-02.png)

Figure 4-2 - Node.js: Launch Program

VS Code stores debug configurations in a file called launch.json inside of a folder .vscode. The editor will pop up with the launch.json file.
Change the name and program to match your code file to debug.

![Configure launch.json](../imgs/VSCode-03.png)

Figure 4-3 - Configure launch.json

Here is a sample JavaScript file to test with which uses the Algorand Smart Contract Layer 1 (ASC1). Name this file ASC1.js

```JavaScript
const algosdk = require('algosdk');

//Retrieve the token, server and port values for your installation in the algod.net
//and algod.token files within the data directory or use a standup instance if available

//Hackathon TestNet
const token = "ef920e2e7e002953f4b29a8af720efe8e4ecc75ff102b165e0472834b25832c1";
const server = "http://hackathon.algodev.network";
const port = 9100;

//Recover the account

var mnemonic = "awake used crawl list cruel harvest useful flag essay speed glad salmon camp sudden ride symptom test kind version together project inquiry diet abandon budget";

var recoveredAccount = algosdk.mnemonicToSecretKey(mnemonic);
console.log(recoveredAccount.addr);
//check to see if account is valid
var isValid = algosdk.isValidAddress(recoveredAccount.addr);
console.log("Is this a valid address: " + isValid);

//instantiate the algod wrapper
let algodclient = new algosdk.Algod(token, server, port);
//submit the transaction
(async() => {
    //Get the relevant params from the algod
    let params = await algodclient.getTransactionParams();
    console.log("here" + params);
    let endRound = params.lastRound + parseInt(1000);
    let fee = await algodclient.suggestedFee();

    // create LogicSig object and sign with our secret key
    // let program = Uint8Array.from([1, 32, 1, 0, 34]);  
    // For int 1 use ASABICI=
    // int 0 => never transfer money use ASABACI=
    let program = new Uint8Array(Buffer.from("ASABICI=", "base64"));
    // makeLogicSig method takes the program and parameters
    // in this example we have no parameters
    // If we did have parameters you would add them like
    // let args = [
    //    Uint8Array.from("123"),
    //    Uint8Array.from("456")
    // ];
    // And remember TEAL parameters are order specfic
    console.log("program " + program);
    let lsig = algosdk.makeLogicSig(program);
    // sign the logic with your accounts secret
    // key. This is essentially giving your
    // key authority to anyone with the lsig
    // and if the logic returns true
    // exercise extreme care
    // If this were a escrow account usage
    // you would not do this sign operation
    lsig.sign(recoveredAccount.sk);

    // At this point you can save the lsig off and share
    // as your delegated signature.
    // The LogicSig class supports serialization and
    // provides the lsig.toByte and fromByte methods
    // to easily convert for file saving and 
    // reconstituting and LogicSig object

    //create a transaction
    let txn = {
        "from": recoveredAccount.addr,
        "to": "SOEI4UA72A7ZL5P25GNISSVWW724YABSGZ7GHW5ERV4QKK2XSXLXGXPG5Y",
        "fee": params.fee,
        "amount": 200000,
        "firstRound": params.lastRound,
        "lastRound": endRound,
        "genesisID": params.genesisID,
        "genesisHash": params.genesishashb64
    };

    // create logic signed transaction.
    // Had this been an escrow the lsig would not contain the
    // signature but would be submitted the same way
    let rawSignedTxn = algosdk.signLogicSigTransaction(txn, lsig);

    //Submit the lsig signed transaction
    let tx = (await algodclient.sendRawTransaction(rawSignedTxn.blob));
    console.log("Transaction : " + tx.txId);

})().catch(e => {
    console.log(e);
});
```

Now the debugging can start.
Select your new launch configuration form the list.

![Select the desired configuration](../imgs/VSCode-04.png)

Figure 4-4 - Select the desired configuration.

Before pressing the run button, place a breakpoint in the code.

To set a breakpoint, simply click on the left margin, sometimes referred to as the gutter, on the line to break. 

![Select the desired configuration](../imgs/VSCode-05.png)

Figure 4-5 - Set breakpoint.

Select the run icon in the debugger pane.

![Start Run and Debug](../imgs/VSCode-06.png)

Figure 4-6 - Start Run and Debug.

Your breakpoint should be hit, and the program execution paused on the line of your breakpoint. It is highlighted.

![Debugging](../imgs/VSCode-07.png)

Figure 4-7 - Debugging.

We are now debugging! See Figure 4-7 above.

A) Breakpoint 

B) Hover over a variable with the cursor

C) See the Data Tip with the contents

D) Navigate by selecting run (to next breakpoint), step over, step into, step out of or stop 

**Task 4-2 Debug JavaScript from Chrome**

Use this task to setup debugging in a JavaScript file referenced in HTML similar to this:

```html
...
   </body>
   <script src="test.js"></script>
</html>
```
A JavaScript Webapp sample can be found here to test with: <https://github.com/algorand/js-algorand-sdk/tree/master/examples/webapp> 


Install the VS Code Extension for Debugger for Chrome. There are also VS Code Extensions for other browsers. 

![Debugging](../imgs/VSCode-08.png)

Figure 4-8 - Add a Chrome debugger extension.

Note: The extension operates in two modes - it can launch an instance of Chrome navigated to your app, or it can attach to a running instance of Chrome. Using the URL parameter you simply tell VS Code which URL to either open or launch in Chrome.

Create another debug configuration. This time select the Chrome: Launch option from the list. 

![Add configuration for Chrome: Launch](../imgs/VSCode-09.png)

Figure 4-9 - Add configuration for Chrome: Launch.

![Modify the name](../imgs/VSCode-10.png)

Figure 4-10 - Modify the name to clarify of desired and URL to include the page.

From Terminal, start your localhost in the folder where you are debugging locally.

Set a breakpoint in the JavaScript code. Hit the run icon or press F5 to Launch Chrome.

![Launch Chrome](../imgs/VSCode-11.png)

 
Figure 4-11 - Launch Chrome

You should see your page open. When you hit a breakpoint you should see the same options to debug as listed in Figure 4-6:


# Resource Links 

[Node.js](https://nodejs.org/en/) 

[Introductory VS Code Videos](https://code.visualstudio.com/docs/getstarted/introvideos)

Keyboard Shortcuts: 

[Windows](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)

[MacOS](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)

[Linux](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)



