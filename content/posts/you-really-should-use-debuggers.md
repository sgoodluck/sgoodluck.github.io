+++
title = "You really should use debuggers"
author = ["Seth Martin"]
date = 2025-03-25T10:21:00-07:00
tags = ["debuggers", "dap", "lsp"]
categories = ["Programming"]
draft = false
+++

This a post about something near and dear to my heart: ****Debuggers****.

In this post, we are going to explain why you should use a debugger, what they are, and how to set them up in your favorite editor d'jour (whether that is vscode, neovim, or emacs).

Why? Because I believe that quality matters and that by understanding this incredible tool you'll not only have a better time programming, you'll produce better code which is good for everyone.


## Why use a debugger? {#why-use-a-debugger}

If you aren't using a debugger in your programming life, you are skiing with only one boot on and there isn't even snow.

Debugging without a debugger is often referred to as ****caveperson debugging**** because it is dumb, a little unsightly, and about as effective as hitting a computer with a club and expecting a meal.

Print statements and console logs are comically inept compared to running a debugger. If you are relying on `console.log("help me")` and `print("help me please")` you are programming like this:

![](/ox-hugo/baby.jpg)
She isn't even holding it right!

Once you learn to use your debugger, you will feel like this:
![](/ox-hugo/neo.jpg)


### Better for debugging {#better-for-debugging}

Seriously, with a debugger you get to stop your code in place and inspect (or change) anything you want. This gives you incredible levels of insight and control over your programs. Bug finding and correcting become massively more efficient.


### Better for learning a new codebase {#better-for-learning-a-new-codebase}

If you are jumping into an existing project at work or contributing to an open source project, using the debugger is one of the most efficient ways to become familiar with a new codebase. You can simply run the debugger and watch the code execute step-by-step.


### If debuggers are so great, why doesn't everyone use them? {#if-debuggers-are-so-great-why-doesn-t-everyone-use-them}

Setting them up can seem tricky and be frustrating. This stems from a lack of understanding about what a debugger is and how it works.
Once you understand them in a fundamental way, using them becomes easy.


### Is learning to use a debugger worth it in the age of AI and Vibe Code? {#is-learning-to-use-a-debugger-worth-it-in-the-age-of-ai-and-vibe-code}

Absolutely, in fact I would say learning to use a debugger is even more important in the age of AI and Vibe Code. It is the most efficient way to understand your vibe code and to pinpoint issues that you or your AI minions can fix.


## What exactly is a debugger? {#what-exactly-is-a-debugger}

A debugger is a different runtime process for whatever program you working on. In normal person language, instead of running your program using "python" or "node" or whatever other command you're using, you run your program using that language's debugger. Running a debugger is often as simple as doing `python -m pdb myProj.py` instead of `python myProj.py` or `node inspect myScript.js` instead of `node myScript.js`.

****Key Point****: A debugger is usually a part of the language package installed on your computer

Analogously, executing code using the debugger is sort of like running your program in its own special container. This container has its own behavior and tooling that allow you to pause and inspect code while the program runs.

Thus when you run a debugger, the debugger loads your program, instruments magical hooks and monitors that give you the ability to inspect variables normally hidden in a maze of memory, and manages your program's execution which allows you to pause and step through code.


#### What about the debugger in my IDE? {#what-about-the-debugger-in-my-ide}

Your IDE does not have a debugger, it just has a UI that uses the underlying language's debugger.

That's right - there is no difference in the debugger whether you run it in `vscode`, `terminal`, `emacs`, `neovim`,
or some other editor. In almost every case, all editors rely on the same underlying debug environments[^fn:1].

Your `launch.json` file in `vscode` just instructs vscode on how to run the language's builtin debugger.

****A debugger is a part of (or an add-on) to the language, not a part of your editor****


### Debugger Commands {#debugger-commands}

Debuggers use a standard set of commands. These commands let you navigate around your code while debugging. You don't need to remember all of them but here is a primer that will cover 90% of what you will do.


#### Navigation {#navigation}

-   ****Set/Remove Breakpoint****: Marks a line where execution should pause
-   ****Continue/Resume****: Proceeds until the next breakpoint
-   ****Step Over****: Executes the current line and stops at the next line
-   ****Step Into****: Moves into a function to let you debug its internal code
-   ****Step Out****: Finishes executing the current function and returns to the calling function


#### Inspection {#inspection}

-   ****View Variables****: Examine local and global variables in the current scope
-   ****Watch Expression****: Monitor specific variables or expressions as execution progresses
-   ****Evaluate Expression****: Execute arbitrary code in the current context
-   ****Call Stack****: View the hierarchy of function calls that led to the current point


#### Advanced {#advanced}

-   ****Conditional Breakpoints****: Pause execution only when a specified condition is true
-   ****Change Variable Value****: Modify a variable's value during execution
-   ****Restart****: Reset debugging session without restarting the debugger
-   ****Run to Cursor****: Execute until reaching the line where the cursor is


## Debugging in different editors {#debugging-in-different-editors}

We are going to setup and use a debugger in `python` and `javascript` using the `terminal`, `vscode`, `emacs`, and `neovim`. This should (A) drive home the point that debuggers are easy to use/setup and (B) that you can use them in most contexts

For this next part ensure that you have `python` and/or `javascript` installed as these come with built-in debuggers. Some languages, like `Go` require that you install a separate package such as `delve` to debug.


### Setup {#setup}

We are going to create a buggy `python` and `javascript` file and use their respective debuggers in different editors.

To start, let's create our little testing area:

```bash
# First create the directory structure
mkdir -p debugger/python debugger/js

# Then create some empty files
touch debugger/python/main.py debugger/js/main.js
```

Let's `cd debugger/python`, open `main.py` and paste in the following code.

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

You can run this via `python main.py`. You should see some test results and an obvious problem.

Next, `cd ../js/`, open `main.js` and paste the following in:

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

Similarly, you can this via `node main.js`. As before, you will see test results and a problem.


#### How would you debug these using print statements? {#how-would-you-debug-these-using-print-statements}

> "I suppose you could inject your print statement into the for loop and then watch it print."

What if the bug only occurs in some cases or the list is really large?

> "I guess you could add an if-block in your code to print only when..."

This is not efficient and might introduce undesirable side effects.
Instead, let's use a debugger and set breakpoints to pause the code exactly where we think there is an issue.


### Debugging in the terminal {#debugging-in-the-terminal}

Remember: Debugging is just executing your code with a different command. Since you can run your code in a terminal, you can run the debugger in the terminal as well.

In the image below, I have our python code opened in `neovim` on the right and on the left, I've run `python main.py` followed by `python -m pdb main.py`. This started the python debugger which took `main.py` as its argument.
![](/ox-hugo/Terminal1.png)

How do you use the python debugger in the terminal? You can always type `help` or you can use a simple cheat sheet like [this one](https://kapeli.com/cheat_sheets/Python_Debugger.docset/Contents/Resources/Documents/index). Most debuggers have mnemonic command system which is a fancy way of saying "use the first letter of an action to execute that action". In other words, `q` means `quit` and `b` means `breakpoint` and `c` means `continue` and so on.

Inspecting the terminal, we can see what the python debugger printed:

```bash
> /Users/sgoodluck/Documents/Projects/debugger/python/main.py(1)<module>()
-> def calculate_sum(numbers):
(Pdb)
```

Line 1 tells us what file we are in and what line we are on. Line 2 has printed the line of code at this point. Line 3 is awaiting our next command.


#### Navigating code with the debugger {#navigating-code-with-the-debugger}

If we type `n` (for next) three times, we will get to `main` and can use `s` (for step into) to step-into the `main()` function. We can keep using `n` until we get to `result = calculate_sum(test_data)`.

At this point we want to `s` (step into) `calculate_sum(...)` and inspect the variable `numbers` using `p` (print) like so:
![](/ox-hugo/Terminal2.png)


#### Using breakpoints {#using-breakpoints}

So far, things look good. However, stepping through code manually this way is tedious. Let's use a breakpoint to pause code execution exactly when we want to. Looking briefly at our test cases, we can see that our `total` variable seems to not be adding everything in the array. Let's add a breakpoint on `line 6` where we are calculating this variable by using `b 6` (breakpoint at line 6).

Now, `c` (for continue). This will run the code until it hits the breakpoint we just set. At this point we the program is paused inside our for loop.We want to watch the value of `i` and `total`, so let's use `display i, total` which instructs pdb to print these variables each time it pauses on our breakpoint.

{{< figure src="/ox-hugo/Terminal3.png" >}}

After `c` (continuuing) a couple of times and we can see that we broke out of our loop at `index = 3` instead of `index =4`.
Congratulations, we have discovered the bug!

We don't want to subtract 1 on `line 5`. A quick correction and we can see that the code is behaving as we expect.
![](/ox-hugo/Terminal4.png)


#### Terminal debugger with node {#terminal-debugger-with-node}

Here is an abbreviated version doing the same thing with our javascript code. A quick `help` command will show similar mnemonic commands. In this case, setting a breakpoint uses `sb(line #)` and watching variables is achieved by `watch('variableName')`
![](/ox-hugo/Terminal5.png)

As before, we ran the code via node and saw something unexpected. So, we ran the code using the debugger via `node inspect main.js`. We set a breakpoint on `line 7` and set a `watch` on the `sum` and `count` variables. One `c` (continue to next breakpoint) later and we finish our first test case can see the value of those variables (which are incorrect). Can you see the issue now?

Debuggers are ****POWERFUL**** but using a debugger in the terminal is a bit of a drag.


### Debugging in vscode {#debugging-in-vscode}

`vscode`, like all major IDEs, tries to hide what is really going on. But now you know - your IDE is running the language's debugger under the hood and simply provides a visual UI to make it easier to use (or so they say). `vscode` and other editors use a configuration file that tells `vscode` how to interact with the underlying debugger.


#### Creating a launch.json file {#creating-a-launch-dot-json-file}

Let's open `vscode` to the same working directory as before. This time, click the "Debug" icon on the left panel. For illustrative purposes, instead of selecting a debugger, click the `create a launch.json` link on the left side panel.
![](/ox-hugo/vscode1.png)

This will create a file called `launch.json` and prompt you through some confusing menu options. Click the `Node.js: Attach` option for now for simplicity.  All of these adjust equivalent command line configurations (more on that later)
![](/ox-hugo/vscode2.png)


#### Setting breakpoints {#setting-breakpoints}

Next, open your `main.js` file and click your mouse thing (I'm an emacs/neovim guy, rats gross me out) to the left of the line number where you want to set a breakpoint. We add a breakpoin to `line 11` in this case. Finally click the green run arrow in the top left that says "Launch Program".
![](/ox-hugo/vscode3.png)


#### Using the debugger UI {#using-the-debugger-ui}

If all is well, the program will execute and then pause on line 11. The UI shows you all of the variables at the current point, the call stack, and more. You might even recognize the control button actions now. This is where IDEs can really shine when it comes to debugging.
![](/ox-hugo/vscode4.png)
While the setup is more cumbersome (launch.json configuration files require that you read documentation to use them properly), the result is a prettier (and arguably more useful) developer experience. VSKids rejoice!


#### launch.json vs command line arguments {#launch-dot-json-vs-command-line-arguments}

I'm too lazy to type this out myself, so here's an AI generated table of various debug node commands and their equivalent vscode launch settings:

{{< figure src="/ox-hugo/vscode5.png" >}}

There are plenty more, but you should generally get the idea that most parts of a `launch.json` correspond to some CLI equivalent.


### Debugging in neovim {#debugging-in-neovim}

Fear not ye neovim fanatic. You can have very similar functionality in your neovim environment. To save me some keystrokes, I recommend you check out the following video by `typecraft`. He does a stellar explanation and demo of setting up and using a UI-forward debugging experience in `neovim`.

[Youtube: Typecraft - Configuring Neovim Debugger](https://www.youtube.com/watch?v=oYzZxi3SSnM)


#### DAP (debugger adapter protocol) {#dap--debugger-adapter-protocol}

Remember how the terminal debugger looked different for `python` and `javascript` (e.g. `b 6` in python vs `sb(6)` in javascript?)

That's because they are different debuggers and have different APIs. This is where `DAP` (debugger adapter protocol) comes in. DAP acts as a unified API for most language debuggers meaning that IDEs like `neovim` and `vscode` only need to have a DAP integration to be able to use the different debuggers.


#### Basic Instructions {#basic-instructions}

Genuinely, follow that typecraft video!

1.  Install `nvim-dap` and `nvim-dap-ui`
2.  Configure some basic key bindings for common debug commands (e.g. `continue` and `set breakpoint`)
3.  Configure some listeners to open and close helpful UI screens
4.  Setup and install additional packages as required by your language of choice


### Debugging in emacs {#debugging-in-emacs}

Emacs is my favorite editor. It is more configurable than neovim and more capable and usable than every other IDE on the market. It is also a massive PITA sometimes. Getting the debuggers working in emacs was a real hassle. Luckily, I suffered so you don't have to!

I will be using `doom emacs` for this because that's just how I'm rolling these days. But you can adapt this for any flavor of emacs.


#### Installing DAPE {#installing-dape}

1.  Comment out anything in your `init.el` file to do with debugging (if on Doom)
2.  Add this line to your `packages.el` file to install dape:

    `(package! dape) ;; debugging in emacs`
3.  Add these lines to your `config.el` file to configure a basic debugging setup
    ```emacs-lisp
    ;;;; DAPE Debugger Configuration
    (use-package! dape
      :config

      ;; Optional: Global keybindings
      (map!
       :leader
       :desc "Debug start/continue" "d d" #'dape
       :desc "Debug stop" "d k" #'dape-stop
       :desc "Debug restart" "d r" #'dape-restart
       :desc "Debug next" "d n" #'dape-next
       :desc "Debug step in" "d i" #'dape-step-in
       :desc "Debug step out" "d o" #'dape-step-out
       :desc "Debug continue" "d c" #'dape-continue
       :desc "Toggle Breakpoint" "d b" #'dape-breakpoint-toggle))
    ```
4.  Following [the directions](https://github.com/svaante/dape?tab=readme-ov-file), install any relevant debugger software you need on your system.


#### Using DAPE {#using-dape}

After configuring dape and installing I can simply open my target file and use `space d` to see my options. I'll set a breakpoint on line 7 and run the debugger with `space d d`
![](/ox-hugo/emacs1.png)

This will show the following transient buffer which lets you set different options for the debug adapter you've chosen. In my case, I chose to use `debugpy`. If I just want to run the program, I can just hit `enter`. Alternatively, I could do `debugpy :stopOnEntry t` to have the program pause immediately when starting.
![](/ox-hugo/emacs2.png)

Once you run the program, we get a familiar looking UI that allows us to debug our program effectively.
![](/ox-hugo/emacs3.png)


## Summary {#summary}

I hope you now have a clearer sense of what a debugger is and how it works. Python and javascript are the easiest to setup. Your actual projects or environments may be more complicated and present any number of challenges. Hopefully, this little bit of knowledge helps you find a path forward. Cheers


## Disclaimers &amp; Extra Tidbits {#disclaimers-and-extra-tidbits}


#### Programs can behave differntly when running via debugger {#programs-can-behave-differntly-when-running-via-debugger}


#### Some bugs "vanish" in a debugger (usually timing related ones) {#some-bugs-vanish-in-a-debugger--usually-timing-related-ones}


#### Environment variables might need to be set separately for debugging {#environment-variables-might-need-to-be-set-separately-for-debugging}

[^fn:1]: This is not true 100% of the time, but for most purposes it is close enough
