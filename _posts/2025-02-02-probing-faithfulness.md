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
Additionally, with the proliferation of the test-time compute methods, there will be more and more reliance on the CoT of the models. It is important that we can detect whether the outputs of this CoT is unfaithful. 
In some sense, if we can guarantee that the output of the models are faithful, it would make alignment problem much easier, since we could increase the probability that we can take the outputs of the model at face value and concentrate on looking for unintended side effects or simply ask the AI if it is planning to do something harmful.

Detecting faithful reasoning can help in three key areas:

1. **Safety Monitoring**: Unfaithful reasoning detection could serve as an early warning system for potential safety issues.
2. **Alignment Verification**: This technique provides a way to verify that models are actually using the reasoning processes they claim to use.
3. **Deception Detection**: The method might help identify cases where models learn to provide plausible-sounding but ultimately misleading explanations.


## The Approach: Probing the Model's Mind

My method for detecting unfaithful reasoning relies on examining the model's internal representations using linear probes. The process involves three key steps:

1. Creating a dataset of faithful and unfaithful reasoning examples using a probability-based metric
2. Extracting the model's internal activations during reasoning
3. Training linear classifiers to distinguish between faithful and unfaithful cases

## Results and Analysis

The results proved more interesting than I initially expected. The linear probes achieved 78.87% accuracy in detecting unfaithful reasoning, significantly above random chance. Here are the key findings:

![Probe performance visualization](https://raw.githubusercontent.com/nbortych/probing_faithfulness/refs/heads/main/probe_performance_actual.png)

Looking at the performance across different layers revealed an intriguing pattern. While I expected to see gradually improving performance in deeper layers, instead I found:

1. Strong initial performance in early layers (70.42% at layer 0)
2. A dip in middle layers (67.61% at layer 13)
3. The best performance in the final layer (78.87% at layer 27)

This U-shaped pattern suggests that information about reasoning faithfulness is processed and transformed throughout the model's layers in a more complex way than initially hypothesized.

[Confidence distribution visualization]

The probe's confidence distribution shows clear separation between faithful and unfaithful cases, though with some overlap. This suggests that unfaithful reasoning exists on a spectrum rather than as a binary property.

## Limitations and Future Work

While these results are promising, several important limitations should be noted:

1. The experiments used a relatively small language model (1.5B parameters)
2. The experiments used a subset of one dataset (elementary_mathematics subset of MMLU)
3. Early Answering Faithfulnes measure was simplified for computational reasons.
4. It was focusing only on one aspect of faithfulness.
5. Very simple linear probe was used only on the last token of the response. 

Looking ahead, I see several promising directions for future research:

1. Extending the technique to larger models
2. Developing methods for black-box models
3. Investigating the relationship between faithfulness and other safety-relevant properties

## Conclusion

This work demonstrates that unfaithful reasoning leaves detectable traces in a model's internal representations. While much work remains to be done, these findings suggest that developing reliable methods for detecting and understanding unfaithful reasoning is possible and valuable for AI safety research.

The code for this project is available at [GitHub repository link](https://github.com/nbortych/probing_faithfulness).

## Acknowledgments

This research was conducted as part of the AI Alignment Fundamentals Course. I'm grateful for the whole BlueDot Team and my cohorts. 