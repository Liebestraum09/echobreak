# EchoBreak

A simulation-based demonstration of how recommendation algorithms create filter bubbles, and how a simple structural intervention can mitigate them.

## Motivation

People are naturally inclined to seek information that confirms their existing beliefs — a cognitive tendency known as confirmation bias. Recommendation systems amplify this tendency. When a user clicks on a tech article, the system surfaces more tech content, which leads to more tech clicks, which further narrows the recommendations. Over time, the user's feed converges to a single category. The user does not perceive this narrowing, because the feed defines their visible information environment.

This is not only an ethical concern. A user locked into a single-category feed loses the opportunity for serendipity — the chance discovery of content outside their established interests. In the long run, this reduces engagement and contributes to platform attrition.

This project was motivated by a straightforward question: what happens if, instead of trying to change user behavior, we change what the system shows?

## Approach

The simulation models a user interacting with a news recommendation system over 300 steps. Two recommendation strategies run in parallel with **identical user behavior** — the same click model, the same parameters, the same initial profile:

- **Echo Chamber**: All 10 recommended articles are selected by cosine similarity to the user's interest profile.
- **EchoBreak**: 7 articles are selected by similarity, and 3 are reserved for categories the user has been under-exposed to, sampled proportionally to their cumulative exposure deficit.

At each step, the user clicks one article based on a softmax probability distribution over similarity scores. The softmax temperature (0.5) controls how sharply the user favors high-similarity articles — lower values produce near-deterministic behavior, while higher values allow more exploratory clicks. After each click, the user's interest profile shifts slightly toward the clicked article's category.

The key design choice is that we track the **exposure log** (what the system showed) rather than the profile trajectory (how the user's interests changed). This sidesteps the limitations of the simplified click model and directly measures what matters: the information diet the system created.

## Results

| Metric | Echo Chamber | EchoBreak |
|--------|-------------|-----------|
| Final Exposure Entropy | ~0.0 | ~1.6 |
| Dominant Category Share | ~99% | ~70% |
| Categories Shown (last 50 steps) | 1 | 6 |

Diversity is measured using Shannon entropy — a standard information-theoretic metric that quantifies how evenly distributed a set of categories is. An entropy of 0 means all exposure is concentrated in a single category; the theoretical maximum for 6 categories is approximately 2.58 (uniform distribution).

Echo Chamber converges to a single category within the first few dozen steps. EchoBreak maintains exposure across all 6 categories, with the user's primary interest remaining dominant but not exclusive.

## Key Takeaway

Changing user behavior is difficult; confirmation bias is a deeply rooted cognitive pattern. However, this simulation demonstrates that modifying the recommendation output alone — reserving a small number of slots for under-exposed categories — is sufficient to produce a substantially more diverse information diet. The same user, making the same choices, arrives at a fundamentally different outcome depending on whether the system incorporates a diversity mechanism.

## Limitations

- The Echo Chamber's ~99% convergence represents a worst-case scenario. Real-world platforms typically include some degree of diversity in their recommendation pipelines. This simulation illustrates the theoretical endpoint of pure similarity-based optimization with no diversity safeguards.
- The click model is a simplified abstraction. Real user behavior is influenced by article titles, thumbnails, social context, time of day, and other factors not captured here.
- All data is synthetic (1000 articles across 6 categories). The objective is to demonstrate the feedback loop mechanism, not to replicate any specific platform's behavior.

EchoBreak is a project name. The underlying concept is referred to as **diversity-aware recommendation** in the academic literature.

## Development

This project was conceptualized and implemented in collaboration with Claude (Anthropic). The simulation design, iterative debugging, and narrative development were conducted through an extended conversational workflow.

## Run

Open `echobreak.ipynb` in Google Colab and run all cells. No additional dependencies beyond NumPy and Matplotlib are required.
