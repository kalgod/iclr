We thank the reviewer for the detailed and thoughtful feedback. Below, we will address the key concerns and clarify aspects of our work. We have updated the main paper for reference.

>**Weakness 1**: This broader use of the "saliency" term may be misleading, especially readers familiar with classical video saliency prediction tasks

We acknowledge the widely used term in previous works. We mainly followed the terminology "video saliency score" defined in highlight detection domain like DETR [1] and UVCOM [2] to align with the baseline metrics. We have clearly denoted the intended meaning as content importance score in Introduction section to avoid ambiguity. 

Main paper changes: lines 47-50

[1] CVPR23-Query-Dependent Video Representation for Moment Retrieval and Highlight Detection

[2] CVPR24-Bridging the Gap: A Unified Video Comprehension Framework for Moment Retrieval and Highlight Detection

>**Weakness 2 & Question 1**: ...more thorough evaluation or fallback mechanism...How do you ensure that LLM-generated scores are consistent across runs and models?

We address the consistency issue in 3 aspects:

(1) **Fallback mechanism for invalid LLM outputs**. We have cached the previous (Question, Answer) pairs during merge-sort ranking, where Question is $m$ frame sequence numbers and Answer is the extracted rating. When the next answer fails to induce an order, e.g. empty json or sequence mismatch, we switch to another available LLM thread. In this way, we can resume last sorting checkpoint by loading cached answers if the ranking procedure is interrupted. The cached results will be updated when current video rating is complete. We have already provided a json example and merge-sort function in Supplementary Material for reference. 

(2) **Open-source multi-modal LLMs**. We can also apply HiVid on local LLMs for deterministic ranking in Table 1.

Table 1. Saliency accuracy of HiVid w/ open-source multi-modal LLMs.
| Metric | HiVid w/ GPT-4o | Llama-3.2-11B-Vision-Instruct | Qwen3-VL-8B-Instruct | InternVL3-14B | gemma-3-12b-it |
|:---:|:---:|:---:|:---:|:---:|:---:|
| PLCC$\uparrow$ | **0.66** | 0.58 | 0.60 | 0.64 | 0.61 |
| SRCC$\uparrow$ | **0.67** | 0.60 | 0.61 | 0.64 | 0.62 |
| mAP50$\uparrow$ | **0.86** | 0.80 | 0.81 | 0.84 | 0.82 |
| mAP15$\uparrow$ | **0.53** | 0.45 | 0.50 | 0.52 | 0.50 |

We can find that proprietary GPT-4o still outperforms with up to 13.7\% PLCC improvement. However, local inference can guarantee reproducibility and consistency across runs and can be further fine-tuned for specific needs. In general, larger models like InternVL yield better performance but require more computation.

(3) **Thorough evaluation**. We directly report the performance across 5 runs for each proprietary LLM in Table 2.

Table 2. Accuracy consistency across runs.
| Metric | HiVid w/ GPT-4o | HiVid w/ Gemini-2-flash | HiVid w/ Grok2 | HiVid w/ Claude-3-haiku |
|:---:|:---:|:---:|:---:|:---:|
| PLCC$\uparrow$ | **0.66$\pm$0.03** | 0.60$\pm$0.03 | 0.62$\pm$0.02 | 0.53$\pm$0.05 |
| SRCC$\uparrow$ | **0.67$\pm$0.03** | 0.59$\pm$0.04 | 0.61$\pm$0.02 | 0.55$\pm$0.04 |
| mAP50$\uparrow$ | **0.86$\pm$0.02** | 0.81$\pm$0.05 | 0.82$\pm$0.02 | 0.81$\pm$0.03 |
| mAP15$\uparrow$ | **0.53$\pm$0.01** | 0.50$\pm$0.02 | 0.51$\pm$0.01 | 0.48$\pm$0.01 |

The low standard variance proves the stable correlation accuracy for each model. This means that LLMs generate most of the chunk-level scores via confident zero-shot subjective reasoning rather than random guess. This can be proved by the justified reason and score distribution in Fig. 10. Even when the absolute rating is slightly different across each run, the variance can be alleviated by our final ranking and Gaussian Smoothing, as demonstrated by the degraded accuracy for HiVid w/o GS in Table 5 in main paper.

Table 3. Accuracy consistency across models.
| Metric | GPT-4o \& Gemini2 | Gemini2 \& Grok2 | GPT-4o \& Grok2 |
|:---:|:---:|:---:|:---:|
| PLCC | 0.87$\pm$0.06 | 0.91$\pm$0.04 | 0.89$\pm$0.04 |
| SRCC | 0.89$\pm$0.05 | 0.90$\pm$0.04 | 0.90$\pm$0.02 |

In addition, we also present the score correlation between models in Table 3. The high PLCC implies that LLMs yield similar and consistent reasoning on video content, although higher model correlation does not necessarily imply better performance on video saliency accuracy, i.e. GPT-4o still outperforms other LLMs in main Table 1.

In general, we can deploy local LLMs for absolute consistency across runs or we can leverage proprietary LLMs for better performance and negligible variance.

Main paper changes: lines 477-487, 823-837

>**Weakness 3**: the technical novelty in terms of learning methodology remains limited

Our major contribution is a new generalized application of LLMs as human proxy. For example, the ranking module can be applied to other subjective tasks like image or video quality assessment from UGC like Fine-VQ [1] or AIGC. We can then rank and select high-quality (prompt and image/video) pairs by our merge-sort function that favors human-centric metrics. This constructed dataset can be further used to fine-tune fundamental generation models like HQ-Edit [2]. 

In addition, our forecasting model is also the first that combines 3 modalities effectively via content attention in time series domain, which can be combined with ranking module to achieve real-time LLM inference.

[1] CVPR25- FineVQ: Fine-Grained User Generated Content Video Quality Assessment

[2] ICLR25-HQ-Edit: A High-Quality Dataset for Instruction-based Image Editing

>**Weakness 4 & Question 4**: ...involves only 10 participants, which is a small sample size...inter-rater consistency or statistical significance of the reported PLCC improvements?

We have recruited 20 more participants (30 in total) and repeat the user study defined in line 414. We first present the rating statistics in Table 4.

Table 4. Inter-rater consistency.
| Metric | Coefficient of Variation$\downarrow$ | ICC(2,k)$\uparrow$ | ICC(A,k)$\uparrow$ |
|:---:|:---:|:---:|:---:|
| Result | 19.76\% | 0.82 | 0.73 |

Coefficient of Variation (CV) equals to (std/mean)*100, and we derive CV for each video and then compute the averaged CV.
We also leverage Intraclass Correlation Coefficient (ICC) to analyze the robustness. ICC(2,k) evaluates the relative rating consistency while ICC(A,k) is more strict that includes the inter-rater variance.
We can find that CV<20% and ICC(2,k) of 0.82>0.75 are considered "good" according to [1].
While ICC(A,k) is also "moderately" good, demonstrating valid user study.

Table 5. Statistical Significance of MOS correlation.
| Metric |  | GT | HiVid | DETR | VideoLLaMA3 |
|:---:|:---:|:---:|:---:|:---:|:---:|
| PLCC | Value $\uparrow$ | **0.88** | 0.76 | 0.61 | 0.63 |
|  | p value $\downarrow$ | **$10^{-38}$** | $10^{-25}$ | $10^{-14}$ | $10^{-18}$ |
|  | CI | **[0.83,0.92]** | [0.67,0.82] | [0.48,0.73] | [0.51,0.71] |
| SRCC | Value $\uparrow$ | **0.91** | 0.77 | 0.65 | 0.65 |
|  | p value $\downarrow$ | **$10^{-46}$** | $10^{-31}$ | $10^{-18}$ | $10^{-20}$ |

We also test the MOS correlation from 30 participants in Table 5. The results are similar to that in Fig. 7, and our p values for both PLCC and SRCC across different baselines are near zero, demonstrating convincing user rating and our model QoE.

Main paper changes: lines 862-887

[1] JCM16-A guideline of selecting and reporting intraclass correlation coefficients for reliability research

>**Weakness 5**: a more self-contained description in the main paper—especially of how the adaptive decoding works

We have updated the model structure figure in Fig. 6 that includes different CLIP encoders and the trainable parameters. Specifically, we also include a pipeline workflow with Algorithm 1 as reference in lines 346-356. The key of adaptive decoding is that, no matter how long the LLM inference delay is, we only asynchronously perform forecasting upon new responses. Therefore, we can compute the LLM interval $\Delta t$ and derive the required output length in Equ. 7. For example, assume the LLM delay is 5 chunk length (5 seconds) and that forecasting takes 2 chunk length. We upload $m=10$ chunks to LLMs at Chunk 10, then at Chunk 15 we submit the time series, i.e. Rated Chunks 1-10 w/ frames, etc. We will receive the prediction at Chunk 17, and the prediction length is (5+2)+(10+N), the 5+2 is to compensate the elapsed time while the 10+N is the future weights required by ABRs. Because LLM is executed only every $m=10$ chunks, we can ensure that Chunk 17-17+10 all have future N weights for QoE optimization. If LLM interval is longer like 6 chunk length, then the forecasting happens at Chunk 16 with output length adjusted to (6+2)+(10+N).

Main paper changes: lines 281-282, 290-297, 320-322, 346-356, Fig. 6

>**Weakness 6**: explicit analysis of how each module affects overall QoE

We did conduct module ablation study in main paper Table 5. We summarize the results as below:

Table 6. Ablation of HiVid module on VOD streaming
| Model |  | Performance |  |  | Per Video |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|  | PLCC$\uparrow$ | SRCC$\uparrow$ | mAP50$\uparrow$ | mAP15$\uparrow$ | Cost/\\$$\downarrow$ | Time Cost/h$\downarrow$ |
| HiVid | **0.660** | **0.674** | **0.860** | **0.526** | 1.35 | 0.54 |
| HiVid w/o Perception | 0.632 | 0.653 | 0.852 | 0.520 | 1.22 | 0.49 |
| HiVid w/o Ranking | 0.611 | 0.617 | 0.820 | 0.498 | **0.13** | **0.054** |

Table 7. Ablation of HiVid module on forecasting
| Metric | HiVid-M | HiVid-M w/o Attention | HiVid-U |
|:---:|:---:|:---:|:---:|
| MAE $\downarrow$ | **0.08** | **0.08** | **0.08** |
| RMSE $\downarrow$ | **0.12** | **0.12** | **0.12** |
| PLCC $\uparrow$ | **0.29** | 0.26 | 0.24 |
| SRCC $\uparrow$ | **0.27** | 0.25 | 0.22 |

The results demonstrate that our ranking module proves most useful, i.e. accuracy drops from 0.67 to 0.61, thanks to our fine-grained merge-sort. As for the forecasting, the content attention effectively captures the interdependent relationship among 3 modalities, while only uni-modal prediction degrades the most without the guidance from specific video content.

Main paper changes: lines 852-859

>**Weakness 7 & Question 3**: Evaluation of forecasting autonomy...results for a “forecast-only” setting

We would like to clarify that Table 3 in main paper is indeed clean forecasting performance without LLM ratings. We clearly present the accuracy comparison below in Table 8.

Table 8. Forecasting performance ablation.
| Metric | Forecast-only |  | Forecast w/ LLM output |  |
|:---:|:---:|:---:|:---:|:---:|
|  | HiVid-M | Crossformer | HiVid-M | Crossformer |
| MAE $\downarrow$ | **0.08** | 0.09 | **0.13** | 0.16 |
| RMSE $\downarrow$ | **0.12** | **0.12** | **0.18** | 0.22 |
| PLCC $\uparrow$ | **0.29** | 0.23 | **0.20** | 0.15 |
| SRCC $\uparrow$ | **0.27** | 0.22 | **0.19** | 0.13 |

The performance bottleneck is the forecasting itself, i.e. PLCC of 0.29, this incurs an overall lower PLCC of 0.20 when combined with LLM output, despite excellent MAE and RMSE. The reason for poor PLCC is that most models fail to foresee the content variation without future video frames, even for existing SOTA forecasting like Crossformer. We will explore how to combine LLMs to  the predict future highlights based on semantic analysis in future work. 

Main paper changes: 842-851

>**Weakness 8 & Question 5**: The overhead analysis is performed on a single 201-second video...cost and latency would scale for longer or continuous live streams?

We would like to clarify that the overhead results are based on 293 test videos from Youtube-8M with varying lengths from seconds to 10 minutes (we have clarified in experimental setup). The averaged length is 201 seconds. Nonetheless, we present the results for longer videos below in Table 9. Due to time and monetary limits, we test with an example video of 2 hours.

Table 9. Performance of longer live streaming scenario.
| Window Length | Metric | 2 min | 30 min | 2 hour | 6 hour | 24 hour |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| m=10 | Cost/\\$ $\downarrow$ | 0.08 | 1.20 | 4.81 | 14.43 | 57.74 |
|  | PLCC $\uparrow$ | **0.21** | **0.26** | **0.24** | / | / |
|  | Latency $\downarrow$ |  |  |$\sim$8ms  |  |  |
| m=2 (mini) | Cost/\\$ $\downarrow$ | **0.02** | **0.27** | **1.09** | **3.26** | **13.03** |
|  | PLCC $\uparrow$ | 0.13 | 0.16 | 0.15 | / | / |
|  | Latency $\downarrow$ |  |  |$\sim$8ms  |  |  |

Since HiVid applies sliding window for live streaming without future chunks, the cost increases linearly with video length, i.e. LLM rating and forecasting per $m$ video chunks. For longer window lengths $m=10$, the performance is better but with higher cost (57.74\\\$ per 24 hours), yielding a controllable tradeoff. However, we argue that we only need to process the source video in real-world one-to-many streaming, therefore the cost is negligible. The extra latency is near zero because the heavy inference runs asynchronously from the video playback, longer $m$ only affects the forecasting output length and accuracy in Equ. 7, as demonstrated in main paper Table 9.

Main paper changes: lines 361-362, 521-530

>**Question 2**: exact nature of the “ground truth” used for correlation computation?

We adopt 3 datasets in this paper, i.e. Mr.Hisum from Youtube-8M [1], TVSum, SumMe. For Mr.Hisum, the GroundTruth is the aggregated "Most Replayed" statistics from users all over the world (>50,000). According to [1], the "rewatched" clips are what people find interesting and engaging, a perfect proxy for importance score in video streaming. For TVSum and SumMe, the GroundTruth are direct human ratings on each video chunks, but the participants number are small, e.g. 20.

[1] NeurIPS23-Mr. HiSum: A Large-scale Dataset for Video Highlight Detection and Summarization

>**Question 6**: how the LLM-based ranking is implemented in practice?...any measure of ranking consistency across runs or window pairs evaluated?

Our ranking module provides a merge-sort template specifically for multi-frame LLM sorting (see merge-sort-llm.py in Supplementary Material). The comparator can be proprietary LLMs with better performance but slightly different ratings across runs. The measured ranking consistency is overall stable, as demonstrated in Weakness 2 & Question 1 above.
Alternatively, we can choose local LLMs like Qwen3-VL-8B-Instruct for deterministic inference by setting "do_sample=False", but it requires more computation.
