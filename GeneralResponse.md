We are very grateful to all reviewers for their critical and very constructive evaluation of our work. We summarize the main concerns and our updates in the main paper.

Common questions:

>**1. The underlying LLMs of HiVid like GPT-4o are proprietary, these may raise concerns about model consistency and robustness across runs.**

We address this issue by combining HiVid with open-source multimodal LLMs in Table 4 in main paper. The results prove that local inference can guarantee absolute consistency while achieving competitive accuracy, e.g. InterVL incurs PLCC of 0.64 compared with 0.66 from GPT-4o.

>**2. The detailed forecasting procedure in live streaming is hard to understand without consulting the appendix, especially about how the adaptive prediction coordinates with the LLM inference.**

We address this issue by adding a more detailed live streaming pipeline workflow in lines 345-356. We clarify that the key to real-time is the asynchronous LLM inference and forecasting, and adaptive decoding in Equ. 7 ensures that the predicted future weights have covered the inference delay. We further provide an example to depict the detailed timeline of each module.

>**3. The detailed ablation of each module of HiVid.**

We address this issue by clarifying the experiments in main paper Table 5. The degraded accuracy for HiVid without either perception or ranking module proves the effectiveness. We further add ablation on our content attention design in forecasting model in Table 15. The accuracy drops from plain modality combination or uni-modal demonstrate our design novelty.

Specific concerns from Reviewer 44Re:

- Potential terminology ambiguity: We address by clearly denoting saliency score as subjective chunk-level importance score.

- Inherent instability or errors from LLMs: We address by clarifying our cache-based fallback mechanism and thorough robustness evaluation across runs and models.

- Limited learning contribution: We clarify our general application across subjective domains like video quality assessment to construct high-quality datasets, which facilitate fundamental model learning.

- Limited user study scale: We address by extending our participants pool and provide inter-rater consistency and statistical significance.

- Forecasting performance with or without LLM ratings: We address by clarifying the clean prediction in main Table 3 and providing clear accuracy comparison.

- Overhead analysis over longer live streaming: We address by adding experiments on a 2-hour example video. We show the linearly increased costs from sliding window, stable accuracy and near-zero latency, which proves our effective prediction module.

- Question about the ground truth: We clarify the datasets construction process, especially for Youtube-8M that collects the "Most Replayed" statistics for chunk-level importance score.

Specific concerns from Reviewer y7h1:

- Computation intensity for forecasting: We clarify that forecasting is executed along with each LLM response, therefore the computation is sparse, e.g. 10 seconds interval.

- Table size presentation: We address by adjusting each table and figure size.

- Forecasting model structure: We clarify the QKV projection and CLIP usage. We also update Fig. 6 to highlight the trainable parameters.

Specific concerns from Reviewer zB8q:

- Which underlying LLMs used in HiVid: We clarify the proprietary LLMs like GPT-4o as default model. However, we also include new experiments with open-source LLMs and demonstrate the generalization of our design.

- The detailed video types for evaluation: We clarify the dataset scale that includes >6000 videos spanning different categories. We also clarify the robustness on specifically ambiguous videos in Table 7 in main paper.

In general, we have carefully addressed each issue by conducting new experiments or providing more detailed explanation on methodology design.

**Summary of changes in main paper:**

- New experiments: We have added new Table 4, 8, 12, 13, 14, 15, accompanied by explanation and analysis marked in blue.

- Adjusted content: We have moved the forecasting model structure and algorithm details to methodology section to improve clarity, as suggested by Reviewer 44Re.

- More detailed explanation: We add some clarification about saliency terminology, dataset scale, LLMs usage and a new live streaming pipeline in the main sections.

The final main text is 10 pages with most additional experiments presented in the appendices.
