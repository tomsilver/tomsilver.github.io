---
layout: post
title: "Now What? A Recipe for After the Problem Setting (in the Agentic Age)"
date: 2026-07-20 12:00:01
description: "The research recipe I follow after defining a problem setting, with a new step for the agentic age: implement the pure agent baseline."
---

*A follow-up to [What's Your Problem? An Oft-Missing Section in AI Papers](https://lis.csail.mit.edu/whats-your-problem-an-oft-missing-section-in-ai-papers/).*

A while back, I argued that AI papers should include a Problem Setting section. My litmus test was whether a reader could implement a `Problem` abstract class, a `Method` abstract class, and a `run` function that takes both and returns metrics.

That post was nominally about writing, but really it was about thinking. What class of problems do you care about? What is a method allowed to know and do? How will you judge performance?

This post picks up where that one left off. You have defined the problem setting. Now what?

Here is the recipe I usually follow for the kind of mostly-empirical engineering (robotics) research that I do. Most of this recipe has been true for a long time. Step 2.5 is new-ish, and it's the reason I'm writing this now.

### Step 1: Implement a first concrete problem

Start by implementing one subclass of `Problem`. Make it as simple as possible, but not simpler. The essence of the problem still needs to be there.

If your setting is about long-horizon manipulation under partial observability, then the first concrete problem should be long-horizon and partially observable, even if it's a gridworld with three objects. Resist the urge to start with the environment that you hope will appear in the paper's teaser figure. You will iterate on everything downstream of this choice many times. At this stage, fast iteration matters much more than realism.

### Step 2: Implement the stupidest possible approach

Before trying anything clever, implement the stupidest possible subclass of `Method`: take random actions, predict the majority class, always return the empty plan, or whatever else "stupid" means in your setting. Then run it and look at the metrics.

If the metrics don't reveal why the approach is stupid, something is wrong. There are at least four possibilities:

1. Your metrics are wrong; they don't measure the thing you care about.
2. The approach is not as stupid as you thought.
3. Your first concrete problem is too simple.
4. There isn't much room for further research here.

All four are useful things to learn. Better now than after months of method development.

### Step 2.5 (new in 2026): Implement the pure agent

Early in the process, maybe right after Step 2, ask whether you can implement a "pure agent" approach. By this I mean a subclass of `Method` where every abstract method is implemented by handing its inputs to an agent (a Claude, a Codex, whatever) and parsing the output. I'm using "agent" very loosely here -- the point is to consider the best available general-purpose model that can consume and produce the inputs and outputs of your problem.

If you were disciplined about defining the problem setting in the interface-centric way from the original post, the pure agent should be straightforward to implement. The abstract class tells you exactly where the model goes. For a sequential decision-making setting, it might look like this:

```python
class Method(abc.ABC):
    """The form of a method, from the Problem Setting section."""

    @abc.abstractmethod
    def train(self, train_problems: list[Problem]) -> None:
        """Learn whatever you want from the training problems."""

    @abc.abstractmethod
    def reset(self, problem_spec: ProblemSpec) -> None:
        """Prepare to solve a new problem."""

    @abc.abstractmethod
    def step(self, obs: Observation) -> Action:
        """Given the current observation, return the next action."""


class PureAgentMethod(Method):
    """Every abstract method is just a call to a general-purpose agent."""

    def train(self, train_problems: list[Problem]) -> None:
        # Put the training problems in the context. That's it.
        self._train_context = "\n".join(serialize(p) for p in train_problems)

    def reset(self, problem_spec: ProblemSpec) -> None:
        self._history = []
        self._spec_context = serialize(problem_spec)

    def step(self, obs: Observation) -> Action:
        prompt = PROMPT_TEMPLATE.format(
            train=self._train_context,
            spec=self._spec_context,
            history=serialize(self._history),
            obs=serialize(obs),
        )
        response = query_agent(prompt)  # Claude, Codex, ...
        action = parse_action(response)
        self._history.append((obs, action))
        return action
```

If you can't implement the pure agent cleanly, that is useful information. Maybe the interfaces are underspecified. Or maybe the setting genuinely involves modalities or scales where current agents can't operate: say, consuming raw tactile sensor streams at 1 kHz and producing torque commands at 500 Hz.

Next, treat the pure agent exactly as you treated the stupid baseline. Think carefully about why it fails, and make sure that the reason appears in the metrics. Maybe the issue is sample efficiency. Maybe it's wall-clock time or cost per decision. Maybe it's reliability over long horizons, safety, or performance on an out-of-distribution slice of the problem distribution. Whatever the answer is, the `run` function needs to measure it. Otherwise, you will have no way to demonstrate the limitation.

One warning: the pure agent may be very hard to beat. Do not be surprised if it lands uncomfortably close to the oracle in Step 3. That result may send you back to Step 1 to find a concrete problem that better exposes what is actually hard.

This pure agent is really an instance of a broader category, the boring but possibly strong baseline. Depending on the problem, there may be others, like an off-the-shelf planner, and you should not delay implementing those either. But the choice of those baselines depends much more on the problem. Part of what makes agents special in 2026 is that they are a sensible and strong baseline for so many different problems.

When I review papers lately, I'll admit that I sometimes get bored seeing agents and LLMs do everything. But if we don't evaluate these baselines, it is hard to justify the research at all. Why should we believe that the problem is unsolved? Why are the abstractions, planners, learned models, or other structures that we are building actually needed?

### Step 3: Implement an oracle

When possible, implement an "oracle" method that cheats. Give it problem-specific knowledge: ground-truth state, hand-written abstractions, privileged information, unlimited compute, or whatever else helps it perform as well as possible. The point is to establish an upper bound on the metrics. The oracle also doubles as a sanity check on Step 1: if even the oracle can't solve your first concrete problem, the problem implementation is probably broken. For that reason, you may prefer to implement the oracle earlier; the ordering of Steps 2 through 3 is flexible.

Now you have a bracket. The hope is that there is a large gap between stupid and oracle. If the gap is small, go back to Step 1 or reconsider the project. If the gap is large, the oracle also becomes a useful debugging tool. When your real method underperforms, you can swap in pieces of the oracle to figure out what is missing.

### Step 4: Implement progressively more sophisticated methods

Finally, start implementing progressively more sophisticated methods until you're happy with the performance. This is the fun part. Along the way, it's a good idea to also expand to more complex concrete problems, especially once the first one seems saturated by the methods considered so far. Each method that falls short becomes a baseline.

### Future-proofing in the agentic age

The tricky thing about Step 2.5 is that the target is moving fast. The pure agent you beat today may not be the one that reviewers have in mind six months from now. So, at the beginning of the project, it's worth asking whether the problem setting is future-proof.

My guess is that the most future-proof projects are ones that either "embrace the agents" and contribute to improving them (e.g., data collection, model architecture, scaling infrastructure, evaluation), or "beat the agents by definition", where the pure agent can't be run at all, or where it scores zero on some metric and it's obvious why. A few examples of the latter:

- The metrics include real-time constraints on real hardware. If a method must produce high-frequency control outputs on onboard compute, then a method that round-trips to a frontier model scores zero on that metric.
- The problem requires information that can't be present in any pretraining corpus and must instead be acquired through interaction during evaluation: the preferences of this particular user, for example, or the miscalibrated dynamics of this particular robot. Physical interaction efficiency may be a useful metric here, since agents are already quite good at learning from whatever context you give them.
- The metrics require a certificate, not just behavior: a formally verified plan, a proof of safety, or a guarantee that holds over every problem in some set rather than an empirical success rate. Safety for physical robots may be an example, but it depends on the definition: expected safety above a threshold is something an agent might achieve empirically, while a guarantee that unsafe states are never reached is not.
- The problem requires collecting and/or learning from data beyond the limits of a context window, say, millions of interactions in a reinforcement learning environment.

It's hard to come up with many examples like these, and even the ones above are debatable. But this difficulty is itself informative: in 2026, figuring out where these problems live may be a large fraction of the research.

A recent post from Generalist, [Going Beyond World Models & VLAs](https://generalistai.com/blog/beyond-world-models), argues that goals are more powerful than methods, and that the right question is not which method category to choose, but how far we can go given the objectives and constraints.

I agree. I would add that framing the problem is itself a contribution, and often the hardest part of a project, especially now that so much of the method can be an agent. As a research community, we should give more credit to clear problem articulations, including when we review papers. A paper that defines a genuinely new, well-motivated, future-proof problem setting has done something at least as valuable as a paper that squeezes a few more points out of an old one.

A closing caveat: this whole recipe is only one way to do research. Another response to these concerns is to pursue other recipes entirely. For example: research that runs real-world studies with people and/or real robots, where the studies are not just validating an engineered construct, but are informative in themselves; the engineering of real physical robots and devices; research that develops methods with provable guarantees; or research that develops and tests theories to explain empirical phenomena.

<hr>

#### Acknowledgements

Thanks to Hannah Blumberg, Bowen Li, Matteo Merler, and Josh Roy for providing feedback on a draft of this post.
