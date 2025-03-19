+++
title = "10,000 Monkeys"
author = ["Seth Martin"]
date = 2025-03-19T09:25:00-07:00
tags = ["ai", "philosophy"]
categories = ["opinion"]
draft = false
+++

Artificial intelligence is an incredibly powerful tool that has been building in capability for decades. Only in the last couple of years has it moved into the mainstream and brings with it an incredible set of changes the likes of which we haven't seen since the dawn of the internet.


### 10,000 Monkeys {#10-000-monkeys}

There's an old adage that goes something like "If an infinite number of monkeys typed on an infinite number of typewriters for an infinite time, they would eventually produce the Bible".

That is exactly how modern AI tools work today, no matter how posh and sophisticated. Only this time we are feeding the monkeys an occasional banana when something they type resembles a word we like and depending on how you count it, there are only several hundred million monkeys.

If you reward them enough times, you'll eventually have a room full of monkeys who produce somewhat convincing writing and you might make the mistake of thinking "alas, this room full of monkeys has become intelligent!" and depending on your definition of intelligence, this might be true.

But, to call the monkeys intelligent would be a misnomer. They might produce words and even legible sentences, but they are still just monkeys and have no sense of what they are writing. They just want a banana.

You need to remember this when working with "AI" tools or reading anything that resembles "hype" around the subject.
No matter how sophisticated, it is still just a room of unconscious monkeys going after bananas.


### The pitfall of modern AI {#the-pitfall-of-modern-ai}

AI (at least at current levels) cannot think. It cannot reason about what it is doing because it doesn't have the right hardware. Even the most advanced models on the planet only compare to the number of synapses in a housecat and arguably an AI synapse is much simpler and clumsier than its biological counterpart.

Would you trust your housecat to write perfect code or to interpret multiple factual articles? Unless you're my feline-phillic father, I doubt it.

And yet, we find ourselves today in a world full of people who don't understand that their AI tools are at best digital housecats filled with dumb monkeys blindly chasing bananas. If you spend any amount of time on the internet you get inundated by slop written by dumb digital housecats and have to deal with an endless slew of grifters, tech-bros, and wannabes whose intelligence approaches that of their pets telling you how "AI is going to take every job" and that "AI codes better than people" and that "AI is the cure for cancer, misery, and your loneliness".

But even a cursory understanding of the systems reveals the utter falseness in these statements.

I've worked with AI tools, I've worked with AI tools a lot. I've trained my own models that made my GPU beg for mercy. I've met some of world's best researchers leading the charge on the subject. And you know what? Most who know these tools inside and out (and who aren't financially incentivized to say otherwise) would readily tell you about their limitations and the overhype of the modern era.


### The promise of modern AI {#the-promise-of-modern-ai}

Now it would be incorrect to say that AI isn't important. As someone who uses AI tools daily in professional and personal endeavors, I will be one of the first to tell you that you ****should**** be using them. AI tools are to our collective ability comparable to the mouse, keyboard, and the modern GUI. They don't fundamentally change what gets done, but they forever change how we get those things done.

With the right prompts, an AI tool like claude can produce a complex multi-file python data pipeline that would have taken me (a professional Software Engineer with a decade of experience) the better part of a week in mere minutes (ok hours and days if you count the re-prompting, rate limits, bug fixing, and so on). A few decades ago, the same program would be have taken months and a team of researchers violently punching holes in cardstock.

In this way, I believe AI tooling is a serious advance in our capability and velocity. Someone with no programming experience and enough stubborn determination can produce ai-generated artifacts that rival and even exceed what a lone engineer could produce. Isn't that alone worth the hype?

It depends


### How to use modern AI {#how-to-use-modern-ai}

As someone who works with and writes technical systems for a living, I've had a front-row seat to the modern era of AI and I think it is cool. I also think it will be used by just about every person in every organization everywhere in the world and that this is a great thing.

My gripe stems from the fallacy that using AI is a substitute for good thinking or good business fundamentals; it isn't (AI can't fix stupid).

Your cat full of dumb monkeys wants to make you happy but like our flesh and blood relationships, it can only do that if you communicate your needs with clarity and intention (go to therapy). If you can remember that these tools are inherently dumb but highly capable, you will maximize your use of them while warding off dread and grift.


#### Be precise with your prompts {#be-precise-with-your-prompts}

Simplifying greatly, AI tools use fancy word associations (hello multi-dimensional vector databases) to identify the next thing. The sentences they build and actions they take depend on how this vector space (fancy association graph) is traversed. Your job as a prompter is to be intentional about the words you use to maximize clarity (narrow potential associations).

For example, instead of writing "I need a program that visits a bunch of websites and reads them to find people I can sell my widget for lion taming to. Can you do this" you would be better served with "Please create a python program that (1) takes in a spreadsheet where the first column contains profile URLs (2) opens each profile (3) scores them from 1-5 for likelihood they would benefit from my lion taming service, and (4) writes this score in the second column of the spreadsheet".

The more precise you can be, especially about things like inputs and outputs, the more useful your result. This is true if you're writing code or generating a body of text or interpreting something.


#### If you can afford to, train your own model {#if-you-can-afford-to-train-your-own-model}

You probably won't do this and don't need to but we will touch on it anyway. If you can, get your AI kitten at birth and fill it with monkeys you specifically know about. Do this from birth and you'll end up with a model that is explicitly suited to your task at hand.

When you choose to use "GPT3.5" or "Claude Sonnet" you are using a model trained from the start to behave a certain way.

This is non-trivial and if you don't start with a base model, is more like assembling the genetic code for your cat, growing it in a test tube, and then training it until it is ready for action. This isn't necessary for most cases, is overkill, and YMMV.


#### If you can afford to, use fine-tuning {#if-you-can-afford-to-use-fine-tuning}

Do you know what ChatGPT stands for? It stands for Chat Generative ****Pre-Trained**** Transformer. In other words, your housecat has already been trained to be a cat but it doesn't know where your litterbox is yet and will almost certainly ruin your fancy sofa if you aren't careful.

I believe Abraham Lincoln said something along the lines of: "You can please some of the people all of the time, you can please all of the people some of the time, but you can't please all of the people all of the time"

This is exactly the case with your AI tooling and especially true if you are using AI for anything non-trivial. Fine-tuning is actually a simple process and can have outsized impacts on your results. Remember how I said that AI tools use fancy word associations to produce an output? Fine tuning changes the weights of those fancy word associations, narrowing the overall space and increasing the likelihood that your AI kitten stays on track and learns a fancy trick like how to use the litter box.

Not using fine-tuning is fine, but you need to then accept that the AI is much more likely to stray and hallucinate and that no amount of prompting will prevent this.


#### If you can afford to, use RAG {#if-you-can-afford-to-use-rag}

One of the best ways to increase your results is to use a fancy thing called "Retrieval Augmented Generation" or more simply "use some explicitly given sources to generate an answer please". ChatGPT, Perplexity and anytime you upload a file to your AI d'jour is already making use of this under the hood.

Essentially, you're giving the tool additional data that makes it more likely to generate output that aligns with what you are after. Your cat is much more likely to use the litterbox if it knows about it.

Ideally, you would use both. Train a model and ensure that it has direct access to information you want it to use. But fine-tuning with RAG is perfectly acceptable.


#### Use a HITL (human in the loop) when building complex systems {#use-a-hitl--human-in-the-loop--when-building-complex-systems}

Remember, AI cannot think and has no idea what it is doing. Fancy multi-agent systems try to get around this with limited success. Ideally, you want a conscious human interacting with or at least overseeing AI operations to make sure things are behaving as you expect.

Whatever process you create, putting a human into the loop can actually speed things as it can help keep your cat full of brainless monkeys going after the right bananas. HITLs are much like shepherds and can guide the flock to greener pastures and keep the wolves away.

I'm getting tired, so these analogies are getting out of hand. Basically put a human in charge of your minion of AI housecats and you'll have much better results because the human can reason at least 1000x better than even the fanciest AI model. Those AI tools, like your mouse and keyboard, make a human much more capable than they are alone.


#### Accept a degree of slop {#accept-a-degree-of-slop}

AI systems are probabilistic at their heart. It is a room full of monkeys randomly slamming on keyboards. That means the same prompt, same model, same data will yield different results when run multiple times. Hallucinations are unavoidable and can only be minimized. This non-determinism (randomness) is what gives the systems such profound capability in the first place. You can create systems that are very close to deterministic by turning down their temperature and limiting the information they rely on but they will still only ever be "mostly deterministic".

Your job as a wielder of AI is to do whatever you can to minimize the noise going into and coming out of the systems through training, fine tuning, smarter approaches like RAG, and thoughtful prompting.

Do not try to seek perfection because you won't attain it. Instead, create a process that can tolerate the inevitable mistakes and missteps. Ideally, add corrections to your fine-tuning.

If you do these things, you'll still just have something dumber than housecat, filled with brainless banana chasing monkeys. But at least these monkeys will go after the right bananas and your housecat might just impress someone.


## Good luck and happy prompting. {#good-luck-and-happy-prompting-dot}
