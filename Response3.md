>**Weakness 1**: specify which LLM(s) were used (e.g., GPT-4, open-source models like LLaMA) or how LLM inference latency was managed for live streaming

We apologize for the confusion. We have clearly denoted the LLMs used in experimental setup in line 367. The default LLM is GPT-4o while we also conducted ablation study with other proprietary LLMs like Gemini in Table 5 in main paper. In addition, we have also added extra experiments with open-source LLMs like Qwen3-VL-8B-Instruct as below:

Table 1. Saliency accuracy of HiVid w/ open-source multi-modal LLMs.
| Metric | HiVid w/ GPT-4o | Llama-3.2-11B-Vision-Instruct | Qwen3-VL-8B-Instruct | InternVL3-14B | gemma-3-12b-it |
|:---:|:---:|:---:|:---:|:---:|:---:|
| PLCC$\uparrow$ | **0.66** | 0.58 | 0.60 | 0.64 | 0.61 |
| SRCC$\uparrow$ | **0.67** | 0.60 | 0.61 | 0.64 | 0.62 |
| mAP50$\uparrow$ | **0.86** | 0.80 | 0.81 | 0.84 | 0.82 |
| mAP15$\uparrow$ | **0.53** | 0.45 | 0.50 | 0.52 | 0.50 |

We can find that proprietary GPT-4o still outperforms with up to 13.7\% PLCC improvement. However, local inference can guarantee reproducibility and consistency across runs and can be further fine-tuned for specific needs. In general, larger models like InternVL yield better performance but require more computation.

Regarding the LLM inference in live streaming, we have added a pipeline workflow with Algorithm 1 as reference in lines 346-356. The key is the asynchronous LLM inference and forecasting alongside video playback. To enable real-time future weights under significant delay, we proposed the adaptive prediction length in Equ. 7. That said, no matter how long the LLM inference delay is, we only asynchronously perform forecasting upon new responses. Therefore, we can compute the LLM interval $\Delta t$ and derive the required output length in Equ. 7. For example, assume the LLM delay is 5 chunk length (5 seconds) and that forecasting takes 2 chunk length. We upload $m=10$ chunks to LLMs at Chunk 10, then at Chunk 15 we submit the time series, i.e. Rated Chunks 1-10 w/ frames, etc. We will receive the prediction at Chunk 17, and the prediction length is (5+2)+(10+N), the 5+2 is to compensate the elapsed time while the 10+N is the future weights required by ABRs. Because LLM is executed only every $m=10$ chunks, we can ensure that Chunk 17-17+10 all have future N weights for QoE optimization. If LLM interval is longer like 6 chunk length, then the forecasting happens at Chunk 16 with output length adjusted to (6+2)+(10+N).

Main paper changes: lines 281-282, 290-297, 320-322, 346-356, 477-487, Fig. 6

>**Weakness 2**: how removing the LLM-guided merge-sort affects VOD accuracy, or the impact of content-aware attention on live prediction

We would like to clarify that we have presented the module ablation in main paper Table 5. We summarize the results as below:

Table 2. Ablation of HiVid module on VOD streaming
| Model |  | Performance |  |  | Per Video |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|  | PLCC$\uparrow$ | SRCC$\uparrow$ | mAP50$\uparrow$ | mAP15$\uparrow$ | Cost/\\$$\downarrow$ | Time Cost/h$\downarrow$ |
| HiVid | **0.660** | **0.674** | **0.860** | **0.526** | 1.35 | 0.54 |
| HiVid w/o Perception | 0.632 | 0.653 | 0.852 | 0.520 | 1.22 | 0.49 |
| HiVid w/o Ranking | 0.611 | 0.617 | 0.820 | 0.498 | **0.13** | **0.054** |

The results demonstrate that our ranking module proves most useful, i.e. accuracy drops from 0.67 to 0.61, thanks to our fine-grained merge-sort. 

As for the forecasting, we have added new ablation baseline HiVid-M w/o Attention as below: 

Table 3. Ablation of HiVid module on forecasting
| Metric | HiVid-M | HiVid-M w/o Attention | HiVid-U |
|:---:|:---:|:---:|:---:|
| MAE $\downarrow$ | **0.08** | **0.08** | **0.08** |
| RMSE $\downarrow$ | **0.12** | **0.12** | **0.12** |
| PLCC $\uparrow$ | **0.29** | 0.26 | 0.24 |
| SRCC $\uparrow$ | **0.27** | 0.25 | 0.22 |

We can find that the content attention effectively captures the interdependent relationship among 3 modalities, while only uni-modal prediction degrades the most without the guidance from specific video content.

Main paper changes: lines 852-859

>**Weakness 3**: specify the types of videos evaluated

We apologize for the missing details. We have added the dataset scale in line 361-362, i.e. 6416, 50, 25 for 3 datasets respectively. The videos are evenly selected from over 30 categories on Youtube, covering a wide spectrum like sports, game and education. 

Regarding the generalization and robustness, the main results in Table 1 represent the overall accuracy on the diverse videos. We add more details on the model consistency across 5 runs as below:

Table 4. Accuracy consistency across runs.
| Metric | HiVid w/ GPT-4o | HiVid w/ Gemini-2-flash | HiVid w/ Grok2 | HiVid w/ Claude-3-haiku |
|:---:|:---:|:---:|:---:|:---:|
| PLCC$\uparrow$ | **0.66$\pm$0.03** | 0.60$\pm$0.03 | 0.62$\pm$0.02 | 0.53$\pm$0.05 |
| SRCC$\uparrow$ | **0.67$\pm$0.03** | 0.59$\pm$0.04 | 0.61$\pm$0.02 | 0.55$\pm$0.04 |
| mAP50$\uparrow$ | **0.86$\pm$0.02** | 0.81$\pm$0.05 | 0.82$\pm$0.02 | 0.81$\pm$0.03 |
| mAP15$\uparrow$ | **0.53$\pm$0.01** | 0.50$\pm$0.02 | 0.51$\pm$0.01 | 0.48$\pm$0.01 |

The low standard variance proves the stable correlation accuracy for each model. This means that LLMs generate most of the chunk-level scores via confident zero-shot subjective reasoning rather than random guess, even for different types of videos. This can be proved by the justified reason and score distribution in Fig. 10.

 We have presented the performance of HiVid on ambiguous videos across different models and runs in Table 7. We summarize the results as below:

Table 5. Performance of HiVid across different videos
| Category | PLCC |  | SRCC |  | mAP50 |  | mAP15 |  |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|  | Mean $\uparrow$ | Std $\downarrow$ | Mean $\uparrow$ | Std $\downarrow$ | Mean $\uparrow$ | Std $\downarrow$ | Mean $\uparrow$ | Std $\downarrow$ |
| Politics | 0.73 | 0.05 | 0.74 | 0.05 | 0.92 | 0.03 | 0.63 | 0.04 |
| People | 0.69 | 0.03 | 0.69 | 0.04 | 0.88 | 0.03 | 0.60 | 0.02 |
| Education | 0.63 | 0.04 | 0.62 | 0.06 | 0.83 | 0.02 | 0.55 | 0.02 |

It shows that LLMs exhibit strong reasoning ability even on potentially biased videos, thanks to our ranking module which eliminates the score discrepancy between different video content.
