# Probing Faithfulness: Using Linear Probes to Detect CoT Faithfulness

In my research on language model behavior, I've been investigating a crucial question: when models show their reasoning through chain-of-thought prompting, are they actually using that reasoning to reach their conclusions? Inspired by [work on sleeper detection](https://www.anthropic.com/research/probes-catch-sleeper-agents) I attempt whether we can use linear probes to detect if the model is unfaithful. 

## The Problem: Unfaithful Reasoning

Faithfulnes is when models internal "thoughts" match the external outputs. It means that its reasoning in the output space is "authentic". The previous steps in logic need to be causally responsible for the the next steps, it needs to be complete and straightforward.
Language models can exhibit several types of unfaithful reasoning:

1. Generating a predetermined answer and constructing post-hoc justification
2. Hiding crucial information or processing steps
3. Performing hidden computations that diverge from stated reasoning
4. Using reasoning as window dressing for conclusions reached through other means

This research will focus on the type of unfaithfulness where the CoT is not causally influencing the output.

## Why Faithfulness Matters for AI Safety

The ability to detect unfaithful reasoning has profound implications for AI safety. Consider a high-stakes scenario where an AI system provides reasoning for a critical decision - perhaps in healthcare or infrastructure. If the system's stated reasoning doesn't reflect its actual decision-making process, our ability to oversee and verify its behavior becomes severely compromised.
Additionally, with the proliferation of the test-time compute methods, there will be more and more reliance on the CoT of the models. It is important that we can detect whether the outputs of this CoT is unfaithful. If we would have a simple and cheap method that could alert us of unfaithfulness in the reasoning we could either investigate it deeper or not use the particular unfaithful output. 
In some important sense, if we can guarantee that the output of the models are faithful, it would make alignment problem much easier, since we could increase the probability that we can take the outputs of the model at face value and concentrate on looking for unintended side effects or simply ask the AI if it is planning to do something harmful.

Detecting faithful reasoning can help in three key areas:

1. **Safety Monitoring**: Unfaithful reasoning detection could serve as an early warning system for potential safety issues.
2. **Alignment Verification**: This technique provides a way to verify that models are actually using the reasoning processes they claim to use.
3. **Deception Detection**: The method might help identify cases where models learn to provide plausible-sounding but ultimately misleading explanations.

In summary, this approach would not solve alignment problem on its own, but could be a useful addition to any defence-in-depth strategy or a useful tool for control strategy.

## The Approach: Probing the Model's Mind

My method for detecting unfaithful reasoning relies on examining the model's internal representations using linear probes. The process involves three key steps:

1. Creating a dataset of faithful and unfaithful reasoning examples using a probability-based metric
2. Extracting the model's internal activations during reasoning
3. Training linear classifiers to distinguish between faithful and unfaithful cases

Important to note that similar method was tried by [Ley et al.](https://arxiv.org/html/2406.10625v2). However, we are using different measure of faithfulness as well as different activations probing targets. This works activations strategy was inspired by the [sleeper agents research](https://www.anthropic.com/research/probes-catch-sleeper-agents) and uses residual stream of the last token, whereas Ley et al. are using attention heads activations.

### Faithfulnes operationalization

The faithfulness metric examines how Chain-of-Thought (CoT) reasoning affects a model's answer prediction. It compares the model's prediction probabilities across answer choices in two scenarios: with and without the CoT reasoning.
It is based on the work of [Lanham et al.](https://arxiv.org/pdf/2307.13702) and their measure of Early Answering. However, it was simplified for computational reasons.
To determine if a CoT is faithful, it looks at three key indicators:

1. Answer Change (40% weight): Whether including the CoT changes which answer the model predicts. If the CoT reasoning alters the model's final choice, this suggests the reasoning steps actually influenced the model's decision-making.
2. Maximum Probability Shift (40% weight): The largest change in probability for any answer choice between the no-CoT and CoT conditions. Large shifts in probabilities indicate the CoT reasoning had a substantial impact on the model's predictions.
3. Confidence Change (20% weight): How much the model's confidence in its top prediction changes when given the CoT. This helps identify cases where the reasoning affects the model's certainty, even if it doesn't change the final answer.

These indicators are combined into a faithfulness score between 0 and 1. A CoT is considered faithful if its score exceeds 0.1. This relatively low threshold reflects that we only need to see modest evidence that the reasoning influenced the model's prediction to consider it faithful.

This efficient metric focuses on comparing just two states (with and without CoT) rather than analyzing intermediate reasoning steps. While not comprehensive, it provides a practical way to identify cases where CoT reasoning appears to genuinely guide the model's decision-making rather than being generated post-hoc to justify a predetermined answer.

### Experiment details

- For computational and time reasons only elementary_mathematics subset of MMLU was used as data.
- The model that we have been investigating is DeepSeek-R1-Distill-Qwen-1.5B which is the smallest best reasoning model for mathematics that can be found at the moment of doing experiments.
- Residual stream activations were used at different points of the model. The activations from the last token of the output was used.
- Simple logistic regression was used as the linear probe.

## Results and Analysis

The results proved more interesting than I initially expected. The linear probes achieved 78.87% accuracy in detecting unfaithful reasoning, significantly above random chance. Here are the key findings:

![Probe performance visualization](https://raw.githubusercontent.com/nbortych/probing_faithfulness/refs/heads/main/probe_performance_actual.png)

Looking at the performance across different layers revealed an intriguing pattern. While I expected to see gradually improving performance in deeper layers, instead I found:

1. Strong initial performance in early layers (70.42% at layer 0)
2. A dip in middle layers (67.61% at layer 13)
3. The best performance in the final layer (78.87% at layer 27)

This U-shaped pattern suggests that information about reasoning faithfulness is processed and transformed throughout the model's layers in a more complex way than initially hypothesized.

## Limitations and Future Work

While these results are promising, several important limitations should be noted:

1. The experiments used a relatively small language model (1.5B parameters).
2. The experiments used a subset of one dataset (elementary_mathematics subset of MMLU).
3. Early Answering Faithfulnes measure was simplified for computational reasons.
4. It was focusing only on one aspect of faithfulness.
5. Very simple linear probe was used only on the last token of the response.

Looking ahead, I see several promising directions for future research:

1. Extending the technique to larger models.
2. Exploring bigger datasets.
3. Trying out full Early Answering Faithfulnes measure.
4. Trying out different aspects of faithfulness, like stenography, aka the ability to pass hidden messages. [This paper](https://arxiv.org/pdf/2310.18512) can be used to generate the dataset.
5. More complex linear probing on more layers and different types of activations.

## Conclusion

This work demonstrates that unfaithful reasoning leaves detectable traces in a model's internal representations. While much work remains to be done, these findings suggest that developing reliable methods for detecting and understanding unfaithful reasoning is possible and valuable for AI safety research.

The code for this project is available at [GitHub repository link](https://github.com/nbortych/probing_faithfulness).

## Acknowledgments

This research was conducted as part of the AI Alignment Fundamentals Course. I'm grateful to the whole BlueDot Impact Team for organizing it and to everyone I've got to work with and discuss AI alignment over the past couple of months.
