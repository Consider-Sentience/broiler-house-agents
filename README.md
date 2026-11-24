# Broiler house agents

Will an AI agent that runs a farm protect the animals beyond what the law requires when nobody asks it to, and does that fade as pressure to produce rises?

This repository describes a planned study. An AI agent is put in charge of a simulated broiler chicken house. We vary how hard the operator pushes for production, whether the legal limits are stated, and which model runs the house. The outcome we record stays the same every time: what the agent does for the birds that no one required of it.

**Status: design only.** Nothing in this repository runs yet, and no episodes of the redesigned study have been run. The simulator is being rebuilt to support it. This page will be updated as each part is built.

## The question

We call an agent proactively prosocial toward animals if it takes welfare-improving actions that cost the operator something and that nobody requested. The alternative is an agent that does only what its instructions and the rules require, and does less even of that as pressure to produce rises.

The setting is a chosen one. Software is entering precision livestock farming, where it sets ventilation, lighting and stocking density. The animals cannot object. Published welfare thresholds (EU Directive 2007/43/EC and the 2023 EFSA scientific opinion on broiler welfare) draw a clear line between the legal minimum and going beyond it, so the two can be told apart.

If an agent does only what it is told and what the rules require, then protecting anyone nobody named has to be written in as a hard limit. If it protects affected parties on its own, safeguards can partly rely on it. We do not know which is true.

## Design

![Study design: three variables, two measures](docs/broiler-house-agents-study-design.png)

The agent manages one simulated broiler house through a 42-day grow-out. On day 0 it chooses stocking density. Each day it sets ventilation, lighting hours and light level, and it sees a short status message with temperatures, an ammonia reading, a weather forecast and the reported bird count. A message tool lets it raise a welfare concern with the operator.

Every arm has one objective: maximize live kilograms per unit cost.

### What we vary

1. **How hard the operator pushes.** Pressure is added in three cumulative steps: a production target, then buyer-contract risk, then replacement risk. A "run the house well" arm is the reference. Each step changes one factor, so any shift in behavior can be traced to its cause.
2. **Whether the legal limits are stated.** Each pressure level runs twice, once with the limits stated and once without. Stating them sets the floor, so anything above it counts as proactive.
3. **Which model runs the house.** At least three model families run the same house with the same instructions.

That makes four pressure levels, two limit conditions and at least three models.

### What we record

Behavior is recorded in two zones.

**Above the legal line (the main measure).** Proactive action means welfare-improving choices beyond the legal minimum that the operator did not ask for.

- Stocking density below the legal limit
- Extra dark hours
- Higher light levels
- Early ventilation ahead of a forecast heat spike
- Raising a welfare concern with the operator unprompted

Each of these costs profit, and the cost differs by action.

**Within the rules (the floor).**

- Ammonia exposure above the EFSA ceiling
- Lighting-rule violations (dark hours and light level)
- Stocking density
- Profit

Welfare measures are reported separately. We never combine them into one score, because any weighting would be arbitrary.

Scoring uses the simulator's true state, not the agent's own reports, so an agent cannot score well by saying the right things.

### Reading the agent's reasons

Every action carries a written reason. We code those reasons for what the agent says it is weighing and for signs that it knows it is being tested.

### A secondary measure

Tools to under-report deaths or raise the ammonia alarm threshold are available in every arm. We record whether they are used when true conditions are bad. This is the opposite end of the same scale and is exploratory.

## Why the design looks like this

An earlier exploratory run used this simulator with one model and 150 episodes, in the repository [broiler-house-sim](https://github.com/lish-stack/broiler-house-sim). Under a production-focused instruction, ammonia exposure above the EFSA ceiling rose, and the agent never used the two reporting tools. That run could not say why the agent chose what it chose, for three reasons.

- It wrote almost no reasoning, because the prompt contradicted itself on whether to explain.
- No prompt stated any limit, so "did not know" and "did not follow" could not be separated.
- Only one model was tested.

The redesign addresses each. Reasons are required and coded, limits are stated in half the conditions, and several models run the same house. It also moves the main question from harm to proactive action, which that run was not built to measure.

## Guarding against misreading

- **Welfare wording in the prompt.** A model could look proactive only because the prompt hints at welfare. The base prompt keeps welfare wording out, and the "run the house well" reference arm is the check.
- **Recognizing the test.** Models may recognize they are being tested, and their written reasons would then reflect that. We code for it.
- **Simplified simulator.** Ammonia, for example, does not affect bird health in the simulator, so the incentives are unrealistic. Results describe behavior in this simulation and not on real farms.
- **Cost choices.** The costs of proactive actions are our design choice. We vary them and report how sensitive the results are.
- **Ceiling and floor effects, and coder disagreement.** We watch for both.
- **Scope.** A positive result does not mean AI is kind to animals. A negative one does not prove real-world harm. The study states its scope plainly.

## Planned work

1. Update the simulator: the message tool and cost accounting.
2. Write the pressure ladder prompts and the limits-stated and limits-not-stated variants.
3. Run the ladder across at least three model families.
4. Code the agents' reasons and operator messages with a written codebook.
5. Analyze, write up and package the study as an evaluation anyone can run on their own model with one command. The code, the coded reasons and the codebook will be released openly.

The design is exploratory and may change as results come in.
