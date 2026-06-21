---
layout: post
title: "How I Keep My AI Biology Models Honest"
description: "Baselines, held-out datasets, fixed metrics, and the checks I use to avoid believing results that are not real."
date: 2026-06-05
author: "Yash Raj"
categories: [machine-learning, biology]
tags: [AI, biology, machine-learning, single-cell, validation, baselines, research]
image: /assets/images/how-i-keep-my-ai-biology-models-honest/hero.jpg
---

<figure>
  <picture>
    <source srcset="/assets/images/how-i-keep-my-ai-biology-models-honest/hero.webp" type="image/webp">
    <img src="/assets/images/how-i-keep-my-ai-biology-models-honest/hero.jpg" alt="Scientific workflow showing biological data, models, validation gates, held-out data, and a run log." width="1400" height="788" loading="eager">
  </picture>
  <figcaption>Trust the process before trusting the number.</figcaption>
</figure>

When people write about AI for biology, they usually write about the model: the new architecture, the new embedding, the score on some benchmark. I did that too for a while.

What I slowly figured out is that the model is the easy part. The actual work is making sure I am not fooling myself.

Single-cell data is messy, high dimensional, and full of structure I did not put there on purpose. A model can produce a great number that means almost nothing because it learned the batch, the lineage, the patient, the protocol, or the experiment instead of the biology. So most of what I do is not about building a more complicated model. It is about setting things up so that it is hard for me to believe a result that is not real.

I wanted to write down how I actually work, because this part almost never shows up clearly in papers. It is also the part that took me the longest to learn.

## Start with a baseline and protect it

Before I train anything, I fit the simplest thing that could work.

For a perturbation or transition task, that is usually a linear model: take the starting cell state and the action, then predict the future state. I call this the floor.

I keep that floor around the whole time. I do not let later experiments quietly change the split, the preprocessing, the metric, or the baseline code underneath it. The reason is simple: a lot of AI-for-biology results look strong until you fit a linear baseline on the same split and the baseline is just as good.

If the simple model gets the same number, then the deep model did not really learn anything new. It learned the same easy signal in a slower and more expensive way.

I have been burned by this enough times that now the baseline comes first, and every model has to beat it.

I also try to keep more than one baseline when it is cheap. A linear floor, a nearest neighbor in embedding space, and sometimes a model that predicts only the mean change for that perturbation. Each baseline fails in its own way, and the model I am building has to beat the strongest one, not the weakest one.

It is easy to pick a weak baseline on purpose without admitting that is what you are doing. So I try to keep the strong ones in front of me.

## Make the model start at the floor

This is a trick I use everywhere now.

I build the model so its output starts exactly at the linear floor, and the neural part gets added on top with a weight that starts at zero. At the beginning of training, the model is literally just the baseline. To move away from it, the model has to learn something that actually helps.

I like this because it changes how I read the result later.

If the model ends up far from the floor and the metric improves, then the extra capacity did real work. If it barely moves off the floor, the model is telling me that the linear answer was already most of the story. That is useful too.

Either way, I do not have to guess.

There is a smaller reason I like this setup. A big model left on its own can wander away and end up worse than the baseline while the training curve still looks fine. When the model starts at the floor, getting worse than the floor means actively learning a residual weight that hurts. The optimizer usually does not want to do that.

So I get a model that is at least anchored to the simple answer almost for free, and then I can watch how far past it the model can actually get.

## Write down what counts as a pass first

For each project, I write the checks down before I see the final numbers.

Not just whether the number went up, but whether it went up by enough. Not just whether the main score improved, but whether the controls that should not move stayed put.

For a single-cell model, I usually have several of these gates running at once:

- Does it beat the floor on the transition?
- Does it move the right gene programs, not just random directions?
- If I claim the model has memory, does that memory break when I corrupt the thing it is supposed to remember?
- If I feed the wrong action or condition, does the prediction get worse?

The last one is the swap test, and it is one of the checks I trust most. If I scramble the input and the output does not care, the model was never using the input in the first place.

I write the gates and margins down ahead of time so I cannot move them later to match a result I happen to like. It is very easy to talk yourself into a number after you have seen it. I have caught myself doing it: deciding after the fact that a smaller margin was fine because the idea was nice.

Writing the margin down before the run is the only thing that reliably stops me.

<figure>
  <picture>
    <source srcset="/assets/images/how-i-keep-my-ai-biology-models-honest/rules.webp" type="image/webp">
    <img src="/assets/images/how-i-keep-my-ai-biology-models-honest/rules.jpg" alt="Six rules before believing a machine learning result in biology: beat the baseline, start at the floor, fix pass criteria first, assume the metric is lying, hold whole datasets out, and check biology not just loss." width="1100" height="1374" loading="lazy">
  </picture>
  <figcaption>The rules I try to keep visible before I let myself believe a result.</figcaption>
</figure>

## Assume the metric is lying

The worst bug in this kind of work is a metric that secretly looks at the data's own structure.

If I measure how well the model predicts change by projecting onto directions I learned from that same dataset, the score can come out high and still mean nothing. I once watched a plain ridge baseline hit 0.97 on a metric like that. The number was completely real and completely useless.

So now I try to score against a fixed set of gene programs and marker panels that I defined ahead of time, and that do not come from the dataset being tested. The model cannot reshape the test to fit what it learned.

When the score is good on a basis I did not pull out of my own data, that is when I start to believe it.

This sounds obvious written down, but it is one of the easiest mistakes to make. Building the metric from the data is convenient, and it gives you nice numbers. Any time a result looks too good early on, the first thing I check is whether the metric and the model are quietly trained on the same structure.

## Hold whole datasets out

Beating the baseline on a random split is not very impressive.

A random split often leaves cells from the same experiment on both sides. The model can lean on that structure and still look good. The test I care about more is holding a whole dataset out: train on the rest and predict the dataset I held out.

A lot of my wins vanish when I do this. That is the useful part.

If the only thing that helped was a dataset that looked almost like one in training, then the model memorized and did not generalize. I would much rather find that out myself than have a reviewer, collaborator, or wet-lab experiment find it for me.

When a result survives a leave-one-dataset-out test, I trust it much more than the same number on a random split, even if the leave-one-out number is lower.

A smaller honest number beats a bigger number I cannot believe.

## The confound problem is harder than it looks

Everyone knows you are supposed to deal with batch effects.

What took me a while to really understand is that you often cannot just remove the batch, because the batch and the signal you want may live in the same directions of the data. The differences between experiments, the differences between lineages, and the real biological program you care about can sit on top of each other in the same part of the space.

I tried the usual ways to subtract the batch out: centering by batch, adversarial networks that try to predict batch so you can train against them, and projections that are supposed to null out batch directions.

On my data, they usually did one of two things. They either left the batch in, or they removed the batch and took the signal with it. There was no clean direction that was only batch.

That was a hard result to accept, but it was honest. It changed how I think about the problem.

Now I treat robustness to batch as something the model has to learn during training, not something I clean up at the end with a diagnostic. If batch is tangled with signal, then the real test is whether the model still works on a batch or dataset it has never seen.

That loops right back to holding whole datasets out.

The deconfounding step is not a box I tick before training. It is part of what the held-out test is actually measuring.

## Look inside the model, not just at the score

A single number at the end does not tell me why the model is right.

In biology, the why matters because someone may act on it. They may choose a perturbation, design a validation experiment, or trust a predicted direction. So I spend real time looking inside the model, not only at the output.

I look at the latent space and ask whether the structure lines up with biology I already know. Do cell types separate where they should? Do inner layers carry gene-program information, or mostly dataset identity? Where does the useful signal appear? Where does it get washed out?

Sometimes the model is right for a reason I can point to, like using marker genes or programs I would expect. Sometimes it is right for a reason I cannot explain. That is not automatically a victory. It is a flag.

A model that is right by accident can be wrong by accident later.

This part is slower and does not produce a clean benchmark number, so it is easy to skip. I try not to skip it because interpretability is where I think much of the real value is for biology.

A prediction tells you what might happen. Being able to see why the model thinks so is what lets a biologist decide whether it is worth running the experiment.

## Check that the biology did not get worse

A model can improve the prediction metric and break the biology at the same time.

The cell-type markers stop separating. Known programs stop showing up. The batch effect gets baked in instead of removed. The model improves the headline number while making the biological representation less useful.

So I keep checks that have nothing to do with the loss:

- Do marker genes still recover?
- Do clusters still line up with known cell types?
- Did I reduce batch structure, or did I remove biological signal along with it?
- Do known programs behave in the expected direction?

If the model gets a better main number by turning the biology into nonsense, I count that as a fail.

The headline going up does not save it.

These checks are cheap to run, and they have caught problems for me that the main metric was perfectly happy with.

## Keep everything traceable

This one is boring, and I learned it the painful way.

At one point, I had a stretch of results I could not line up against my older runs because I had not kept careful enough track of which code and which data produced which number. I could not tell whether a new model was better than an old one, or whether I had changed something underneath without noticing.

So I had to throw out the comparison, rebuild a clean baseline, and start again.

Now every run gets logged with enough information that I can take any number months later and trace it back to the exact code, exact data, and exact settings that produced it.

When the chain breaks and I cannot trust the link back to an old result, I do not try to patch it. I rerun a clean baseline and compare against that instead.

It feels slow in the moment, but it is much faster than arguing with yourself about whether a number from three weeks ago is even real.

## Let the agent run the loop, but keep the rails on

A lot of this runs on its own now.

I give a coding agent a bounded research prompt, and it goes off and runs experiments, proposes variants, trains models, and reports back. It is fast, and I can try many more ideas than I could by hand.

The thing is, fast is exactly when you fool yourself.

So the loop carries everything above, plus a few more rules. The baseline files and data are read-only. The agent can only write inside its own output folder. Every run is logged with enough lineage that I can trace any number back to the exact code and data that produced it. And the gates still decide pass or fail.

The agent runs the experiments. It does not get to grade its own homework.

I also try to keep the part that decides what to try next separate from the part that runs the experiment. One side proposes the next thing to test and writes down why. The other side executes it cleanly.

Keeping those apart stops the loop from drifting into running whatever is easy and calling it progress.

The volume comes from the agent. The protected baseline, fixed metrics, held-out datasets, swap tests, biology checks, and traceable logs are what keep the volume honest.

<figure>
  <picture>
    <source srcset="/assets/images/how-i-keep-my-ai-biology-models-honest/agent-loop.webp" type="image/webp">
    <img src="/assets/images/how-i-keep-my-ai-biology-models-honest/agent-loop.jpg" alt="Autonomous biology machine learning research loop with proposal lane, execution lane, fixed metrics, held-out datasets, swap tests, biology checks, traceable logs, and pass/fail gates." width="1400" height="788" loading="lazy">
  </picture>
  <figcaption>An autonomous loop is useful only if the guardrails are stronger than the speed.</figcaption>
</figure>

## Honest results over exciting ones

The short version is that I spend most of my effort building reasons not to believe my own results, and then I keep the ones that survive.

This makes for slower progress and a log full of entries that just say: this did not beat the baseline.

I am okay with that.

In biology, a false positive is expensive. Someone reads the result, runs a real experiment from it, spends real time and money, and then finds out the model was wrong.

The whole reason I do this is to help people choose which experiments are worth running. That only works if the model is right when it says it is confident.

So I say the failures plainly. I only claim the wins that cleared a margin on a metric I could not bend.

Most of my best work this year was finding out that something I was excited about did not actually beat a simple baseline, and being glad I checked before I told anyone it did.
