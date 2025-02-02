# Hidden Patterns in Language Models' Reasoning: Using Linear Probes to Detect Chain-of-Thought Faithfulness

In my research on language model behavior, I've been investigating a crucial question: when models show their reasoning through chain-of-thought prompting, are they actually using that reasoning to reach their conclusions? This investigation led me to develop a novel approach using linear probes to detect whether a model's stated reasoning aligns with its internal processing.

## The Problem: Unfaithful Reasoning

Language models can exhibit several types of unfaithful reasoning:

1. Generating a predetermined answer and constructing post-hoc justification
2. Hiding crucial information or processing steps
3. Performing hidden computations that diverge from stated reasoning
4. Using reasoning as window dressing for conclusions reached through other means

Through my experiments, I found that detecting these behaviors is more complex than it might initially appear, but also more tractable than one might expect.

## Why This Matters for AI Safety

The ability to detect unfaithful reasoning has profound implications for AI safety. Consider a high-stakes scenario where an AI system provides reasoning for a critical decision - perhaps in healthcare or infrastructure. If the system's stated reasoning doesn't reflect its actual decision-making process, our ability to oversee and verify its behavior becomes severely compromised.

My research suggests three key implications:

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
2. The binary faithful/unfaithful classification might miss nuanced forms of reasoning
3. The current approach requires access to model internals

Looking ahead, I see several promising directions for future research:

1. Extending the technique to larger models
2. Developing methods for black-box models
3. Investigating the relationship between faithfulness and other safety-relevant properties

## Conclusion

This work demonstrates that unfaithful reasoning leaves detectable traces in a model's internal representations. While much work remains to be done, these findings suggest that developing reliable methods for detecting and understanding unfaithful reasoning is possible and valuable for AI safety research.

The code for this project is available at [GitHub repository link](https://github.com/nbortych/probing_faithfulness).

## Acknowledgments

This research was conducted as part of the AI Alignment Fundamentals Course. I'm grateful for the whole BlueDot Team and my cohorts. 