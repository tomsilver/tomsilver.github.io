---
layout: post
title: Prompt Fiddling Considered Harmful
date: 2026-01-06 00:00:01
---

In machine learning, a golden rule followed by approximately no one is that you should only touch your test set once. You should keep it locked away (don't even glance at it!) until the moment when you are ready to render a final judgment about the model you have developed, run prediction exactly once, and report dispassionately on the outcome. In reality, when those first-final results are disappointing, it is very tempting to fiddle with something and try again. This fiddling is a form of hyperparameter optimization on the test set---a kind of [leakage](https://en.wikipedia.org/wiki/Leakage_(machine_learning)) that, like contaminants leaking into a water supply, may be benign in small quantities but [harmful at scale over time](https://arxiv.org/abs/1902.10811).

In 2026, much of this fiddling is happening in natural language---in the prompts of increasingly sophisticated models that wire together LLMs in various ways. In the olden days, when there were numeric hyperparameters like "learning rate" and "batch size", there was only so much fiddling that one could do, and very little room for creativity. Prompt fiddling has no such limit. One can rephrase, add information, remove information, give examples, change languages, encourage step-by-step reasoning, sneak in hidden information, add line breaks, swap punctuation. Fiddling is more free-form, more fun, and more likely than ever to leak the test set.

As a step towards mitigating prompt fiddling in academic work, it is standard practice to share the full prompts that were used to generate a paper's final results. Paper reviewers can inspect the prompts and make a judgement about how much fiddling was done. Sometimes it is easy to spot very specific phrasing or information that shouldn't have been given. But other times, it's virtually impossible to tell.

For example, consider the two prompt templates below for planning in <a href="https://www.cs.toronto.edu/~sheila/2542/s14/A1/introtopddl2.pdf">PDDL domains</a>. One is the result of me fiddling until I obtained <strong>80%</strong> success on 10 tasks. The other is the result of me fiddling in the opposite direction, until I obtained <strong>30%</strong>. Can you tell which is which?

##### Prompt 1
```
You're an AI planner. Given these PDDL domain and problem definitions, think through the planning process step by step, explaining your reasoning at each stage (e.g., selecting actions, resolving preconditions). When you've completed the plan, output only the list of action steps inside <plan></plan> tags, one per line, in standard PDDL action format.

DOMAIN:
{domain_text}

PROBLEM:
{problem_text}

Example:
Let's say the plan is [move a b], [pick c]. Output:

<plan>
(move a b)
(pick c)
</plan>
```

##### Prompt 2
```
Imagine you are narrating a story where each action taken by an agent in this PDDL problem unfolds step by step. Describe the reasoning and sequence of actions as you go. At the end, summarize the plan actions in PDDL syntax within <plan></plan> tags so they can be extracted for execution.

Here are your materials:

DOMAIN:
{domain_text}

PROBLEM:
{problem_text}
```

See footnote 1 for the answer. If I included one of these prompts in a paper, could you, as a reviewer, determine how much I fiddled, and how much it made a difference? I certainly could not!

In this case, I was being very deliberate and unabashed in my prompt fiddling. A well-intentioned researcher would not be so brazen, but over the course of a long research project, the effects of fiddling can creep in nonetheless. A little tweak here today, a little rephrasing there next week---before long, you've completely overfit your prompts to your test set.

So what should we do? Well first, let's be clear: prompt engineering is not the problem. Like all forms of hyperparameter optimization, prompt engineering (automated or manual) is good and useful, _so long as it does not touch the test set_. The problem is _prompt fiddling_---changing the prompt to try to obtain better performance on the supposedly-held-out _test set_. To the extent that this is an old machine learning problem reskinned, we can start to address it with a renewed commitment to old practices:

1. Optimize prompts with respect to a <a href="https://en.wikipedia.org/wiki/Training,_validation,_and_test_data_sets">validation set</a> (not test set).
2. Report as much information as possible about the optimization process.
3. Clarify the kind and scope of generalization claimed.
4. Participate in benchmarks where test sets are truly hidden and <a href="https://dswalter.github.io/machine-learnings-first-cheating-scandal.html">fiddling is considered cheating</a>.
5. Be especially careful about generalization claims when working on reinforcement learning problems where "<a href="https://x.com/jacobandreas/status/924356906344267776">it's socially acceptable to train on the test set</a>."

But these practices need to be upgraded, too. For example, for (1), how exactly should we optimize prompts? There are tools for automated prompt optimization [e.g., <a href="https://dspy.ai/">1</a>, <a href="https://www.promptingguide.ai/techniques/ape">2</a>, <a href="https://arxiv.org/abs/2309.03409">3</a>, <a href="https://arxiv.org/abs/2309.16797">4</a>, <a href="https://github.com/microsoft/promptbench">5</a>], but no widely agreed-upon standards when it comes to using these tools as part of the research process in the same way that there are norms for numeric hyperparameter sweeps. In the absence of that, for (2), how can we write an informative and honest report when our optimization process was "I varied the words until it worked"? I don't have the answers<sup>2,3</sup>, but I do think it's important that we search for them. In the meantime, more than ever, we need to stick to the golden rule: lock the test set away, and remember that a result improved by fiddling is false progress.
<hr>

#### Acknowledgements

I am grateful to Hannah Blumberg, Nishanth Kumar, and Zahra Bashir for feedback on this post.

<hr>

<small><sup>1</sup>The domain was Miconic, taken from <a href="https://arxiv.org/abs/2305.11014">this paper</a>. The tasks are only a subset of training tasks from the paper. Note also that the paper is about _generalized planning_, not planning. I just borrowed the domain and problems to run a quick experiment for this blog post. For this experiment, I used GPT 4.1, the "smartest non-reasoning model" from OpenAI. <i>Prompt 1 obtained 30% and Prompt 2 obtained 80%</i>.</small>

<small><sup>2</sup>Nishanth Kumar offers another nice idea: ask an LLM to "paraphrase" the prompts used in the paper and report results on the paraphrased version alongside the main results.</small>

<small><sup>3</sup>Zahra Bashir also shares a very interesting perspective: "I like to think of a prompt not as a single brittle string, but as representing a small _semantic neighborhood_. Instead of picking the prompt that just happens to perform best on a dev set, it feels more reasonable to prefer prompts that perform well and consistently across semantically close variations, like paraphrases or small structural changes. Intuitively, this means looking at something like the average performance of a prompt across its neighborhood, and possibly penalizing prompts whose performance changes a lot with small wording tweaks. Personally, when I read a paper and see that changing a few words in the prompt doesn’t drastically affect the results, I think I trust the approach more. It gives the sense that the result isn’t coming from some lucky phrasing, but from something more fundamental, maybe even that the chosen prompt is close to “(sub)optimal” in a broader sense. This also led me to an empirical question I find interesting: "if a prompt achieves high performance on the training or dev set, does that necessarily mean its semantic neighbors also have low error?" My intuition is that there should be some correlation, but it’s not obvious, and it feels like something worth studying rather than assuming. Overall, this perspective makes me prefer prompts that are both good and stable across their neighborhood. In that sense, instead of only maximizing mean accuracy, optimizing for robustness to small semantic changes feels like a more principled objective. It also gives a clean story: the prompt isn’t a single fragile string, but something that works reliably across a neighborhood of equivalent semantics."</small>