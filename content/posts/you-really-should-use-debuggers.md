+++
title = "You really should use debuggers"
author = ["Seth Martin"]
date = 2025-03-25T10:21:00-07:00
tags = ["debuggers", "dap", "lsp"]
categories = ["Programming"]
draft = false
+++

Ok it is that time again where I write a blog post about something near and dear to my heart: ****Debuggers****.

In this post, we are going to explain why you should use debuggers, what they are, and how to set them up in your editor d'jour (whether that is vscode, neovim, or emacs).

Why? Because I love you and more importantly I want people to have better life experiences. Here we go.


## Why you should use a debugger {#why-you-should-use-a-debugger}

Alright, let's get right down to the chase. If you aren't using a debugger in your programming life, you are skiing with only one boot on and there isn't even snow. Do you know what we call debugging without a debugger? ****caveperson debugging**** because it is dumb, a little unsightly, and about as effective as hitting a computer with a rock and hoping for it to just start working.

Print statements and console logs are comically inept compared to running a debugger and no, there isn't a good reason to prefer them. The only reason people prefer them is because they haven't figured out how to use their debugger in the first place.

Right now, if you are relying on \`console.log("help me")\` and \`print("help me please")\` you are programming like this:

{{< figure src="baby.jpg" >}}

She isn't even holding it right!

Once you learn the art of the debugger, you will feel like this:

{{< figure src="neo.jpg" >}}

Seriously, with a debugger you get to stop your code in place and inspect (or change) anything you want to. You get to see the matrix and control it in a way that gives you god-like control over what is happening. Finding and eliminating bugs becomes incredibly efficient.

Where you would normally type some kind of print statement into your code, you can just add something called a \`breakpoint\`, run your code, and the code will pause at that point and you can inspect and even change nearly any variable frozen bullet style.


### If debuggers are so great, why doesn't everyone use them? {#if-debuggers-are-so-great-why-doesn-t-everyone-use-them}

Setting them up can be tricky and a lot of the time people want to get right into the fun of whatever they are programming. I've taught dozens of people to use their debuggers and the common pattern is a lack of understanding around what a debugger is.

I think once you understand what a debugger is doing, the setup makes a lot more sense. Once you know what they are doing and how to set them up, they become as second nature as using an LSP (that's the thing that gives you cute code completions and auto suggestions).


## What is a debugger {#what-is-a-debugger}

A debugger is a different runtime process for whatever program you working on. In normal person language, instead of running your program using "python" or "node" or whatever other command you run it using that language's debugger.

This looks like \`python -m pdb myProj.py\` instead of \`python myProj.py\` or \`node inspect myScript.js\` instead of \`node myScript.js\`.

If you're familiar with python or containerization, an analogy would be that executing code through a debugger is a lot like running the program in its own special container. That container has its own behavior and tooling that allow you to pause and inspect code.

So when you run a debugger, the debugger environment loads your program (not your normal programming run command), instruments magical hooks and monitors (giving you the ability to inspect variables normally hidden in a maze of memory), and manages its execution (giving you the capability to pause or step through code).


#### What about the debugger in my IDE? {#what-about-the-debugger-in-my-ide}

IDEs don't really have debuggers, they just have interfaces that use the underlying language's debugger.

There is no difference in the debugger whether you run it in vscode, terminal, emacs, neovim, or some other mechanism. In almost every case, your editor of choice is relying on the same underlying debug environment[^fn:1].

That's right. They just make a nice gui with some pretty buttons that run the underlying debugger. Your \`launch.json\` file in vscode just tells vscode how to run that language's debugger. That's it.

****A debugger is a part of the language, not the editor****

Keep this in mind and everything else will be easy.


### Debugger Commands {#debugger-commands}

Debuggers use a mostly standard set of commands whether you're running through the terminal or in some IDE. These commands let you navigate around your code while debugging. You don't need to remember all of them but here is a primer that will cover 90% of what you will do.


#### Navigation {#navigation}

-   ****Set/Remove Breakpoint****: Marks a line where execution should pause
-   ****Continue/Resume****: Proceeds execution until the next breakpoint
-   ****Step Over****: Executes the current line and stops at the next line
-   ****Step Into****: Moves into a function call to debug its internal code
-   ****Step Out****: Finishes executing the current function and returns to the caller


#### Inspection {#inspection}

-   ****View Variables****: Examine local and global variables in the current scope
-   ****Watch Expression****: Monitor specific variables or expressions as execution progresses
-   ****Evaluate Expression****: Execute arbitrary code in the current context
-   ****Call Stack****: View the hierarchy of function calls that led to the current point


#### Advanced {#advanced}

-   ****Conditional Breakpoints****: Pause execution only when a specified condition is true
-   ****Change Variable Value****: Modify a variable's value during execution
-   ****Restart****: Reset debugging session without restarting the debugger
-   ****Run to Cursor****: Execute until reaching the line where the cursor is placed


## Let's debug in different editors {#let-s-debug-in-different-editors}

For this next part, I'm going to setup a debugger for \`python\` and \`javascript\` in multiple editors. This should drive home the points that (A) debuggers are easy and (B) that you can use them easily.

For kicks, I'll also generate some code in both languages that has a bug in it.
Ensure that you have python and/or javascript installed.

Now, let's get things setup.

```bash
# First create the directory structure
mkdir -p debugger/python debugger/js

# Then create some empty files
touch debugger/python/main.py debugger/js/main.js
```

Let's put some simple but buggy code in each of these files. You might be able to readily identify the bugs or think of where you might insert print statements. That's fine, this is for illustration.

Go ahead and paste the following into main.py:

```python
def calculate_sum(numbers):
    """Calculate the sum of a list of numbers."""
    total = 0
    for i in range(len(numbers)):
        if i < len(numbers) - 1:
            total += numbers[i]
    return total

def main():
    # Test the function with different inputs
    test_data = [10, 20, 30, 40, 50]
    result = calculate_sum(test_data)

    # Expected sum: 150, but we'll get 100 because of the bug
    print(f"Numbers: {test_data}")
    print(f"Expected: {150}")
    print(f"Actual: {result}")

    # Another test case
    more_numbers = [5, 15, 25, 35]
    another_result = calculate_sum(more_numbers)
    print(f"More numbers: {more_numbers}")
    print(f"Expected: {80}")
    print(f"Sum: {another_result}")

if __name__ == "__main__":
    main()
```

And paste the following into main.js:

```javascript
function calculateAverage(numbers) {
    let sum = 0;
    let count = 0;

    for (let i = 1; i < numbers.length; i++) {
        sum += numbers[i];
        count++;
    }

    // Return the average
    return sum / count;
}

function main() {
    const testCase1 = [10, 20, 30, 40];
    console.log("Numbers:", testCase1);
    console.log("Expected: ", 25);
    console.log("Average:", calculateAverage(testCase1));

    const testCase2 = [100, 5, 10, 15];
    console.log("Numbers:", testCase2);
    console.log("Expected: ", 32.5);
    console.log("Average:", calculateAverage(testCase2));
}

main();
```

How would you go about debugging either of these using print statements?

"I suppose you could inject your print statement into the for loop and then watch it print."

What if the bug only occurs in some cases or the list is really large?

"I guess you could add an if-block in your code to print only when..." and so on and so forth

This isn't efficient and you potentially risk introducing new bugs by introducing even more code to something problematic.

Instead, let's use a debugger and set breakpoints to pause the code exactly where we think there is an issue.

****Bonus tip****

You can make your breakpoints conditional too so you only pause when some condition is met. Cool, right?


### Using terminal {#using-terminal}

Remember that thing I said about a debugger just being a different command to run your code? Well since you can run your code in a terminal, it means you can run your debugger in the terminal.

Let's see what that looks like. In the image below, you can see the python code opened in neovim on the right. On the left, a terminal where I've run \`python main.py\`.

Afterwards, I decided to use a debugger so I ran \`python -m pdb main.py\` which used the \`-m\` flag to run the \`pdb\` program. This started the [python debugger](https://docs.python.org/3/library/pdb.html) and took \`main.py\` as its argument.

{{< figure src="~/Desktop/Terminal1.png" >}}

How do you use the python debugger in the terminal? Well you can always type help or you can use a simple cheat sheet like [this one](https://kapeli.com/cheat_sheets/Python_Debugger.docset/Contents/Resources/Documents/index). It is basically mnemonic which is a fancy way of saying "use the first letter of the action to execute the action".

In other words, \`q\` means quit and \`b\` means break and \`c\` means continue and so on...

Notice what \`pdb\` printed

```bash
> /Users/sgoodluck/Documents/Projects/debugger/python/main.py(1)<module>()
-> def calculate_sum(numbers):
(Pdb)
```

The first line tells you what file you are in and what line you are on. The second line tells you where the debugger has paused execution.

If we type ****n**** (for next) three times, we will get to main and can then ****s**** (for step into) our main() function. We can keep going with next until we get to \`result = calculate_sum(test_data)\`. At this point we want to \`step into\` \`calculate_sum(...)\` and inspect numbers using \`p numbers\` like so:

{{< figure src="~/Desktop/Terminal2.png" >}}

So far, so good. But stepping through code manually can become tedious. Based on our test cases, we can see that the our \`total\` variable doesn't seem to be getting all the numbers. Let's add a breakpoint on line 6 where the total is summing (the screenshot contains relative numbers).

We will ****c**** (for continue) to the breakpoint. Now we want to watch some variables, so let's use \`display i, total\` to watch our index and our growing total. This instructs pdb to print these variables each time the breakpoint is hit.

{{< figure src="~/Desktop/Terminal3.png" >}}

Running continue a couple of times and we can see that we broke out of our loop at index=3 instead of 4! Hoozah, we have discovered the bug. We don't want to subtract 1 on line 5. A quick correction and we can see that the code is happy again.

{{< figure src="~/Desktop/Terminal4.png" >}}

Here is an abbreviated version doing the same thing with our javascript code. A quick \`help\` command shows almost the same mnemonic commands

{{< figure src="~/Desktop/Terminal5.png" >}}

Here, we ran the code and saw that our tests failed. We ran the code through our debugger and set a breakpoint on line 7 and used \`watch\` (which is like display) on the \`sum\` and \`count\` variables.

We can see immediately that both the sum and count are incorrect. Can you identify the issue now?

Get it? Debuggers are POWERFUL.


### Using vscode {#using-vscode}

Ah vscode. vscode is like other major IDEs in that it tries to abstract and hide away what is really going on. But now you know - your IDE is running the language's debugger under the hood and provides some visual gui niceties to make using it a bit easier (or so they say, personally I find vscode to be the least intuitive since you have to deal with this extra configuration file... but this post isn't for my complaints).

Let's see what this looks like in vscode.

First, we need to select our debugger (click the play button with a bug on it) and for the sake of explanation, click "Create a launch.json" file link:
![](~/Desktop/vscode1.png)

That will open up a file that looks like this that gives you a bunch of confusing menu options. They are each useful and basically tell the node debugger to behave differently. These are all just set comparable command line configurations in the terminal using node or nodemon... let's pick "Attach" which is the simplest.
![](~/Desktop/vscode2.png)

Next, in your code simply hover in the spot you want a breakpoint and click your mouse thing (I'm an emacs/neovim guy, rats disgust me) on the lines you want a breakpoint. Great, now click the debug play button in the top left.
![](~/Desktop/vscode3.png)

Now you'll see a beautiful debugging experience in the IDE that shows you variables, call stacks, and more. You might even recognize the debug control commands now.
![](~/Desktop/vscode4.png)

As you can see, while the setup is more cumbersome, the result is a prettier (and arguably more useful) developer experience. VSKids rejoice!

I'm too lazy to type this out myself, so here's an AI generated table of the commands and their equivalent vscode launch settings:

{{< figure src="~/Desktop/vscode5.png" >}}

Fear not ye emacs and neovim users. You can have the same prettiness in neovim and emacs. Let's take a look.


### Using emacs {#using-emacs}


### Usingneovim {#usingneovim}


## Disclaimers &amp; Extra Tidbits {#disclaimers-and-extra-tidbits}


#### Behavior can be different in debug mode {#behavior-can-be-different-in-debug-mode}

<!--list-separator-->

-  What about the debugger in my IDE

    There is no difference in the debugger whether you run it in vscode, terminal, emacs, neovim, or some other mechanising\*\* Performance often goes down


#### Some bugs "vanish" in a debugger (usually timing related) {#some-bugs-vanish-in-a-debugger--usually-timing-related}


#### The debugingnvironment variables might need setup separately {#the-debugingnvironment-variables-might-need-setup-separately}

[^fn:1]: This is not true 100% of the time, but for most purposes it is close enough
