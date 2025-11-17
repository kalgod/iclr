We thank the reviewer for the valuable and insightful suggestions. We have updated the main paper and will address the concerns as follows:

>**Weakness 1**:  The proposed method is based on proprietary LLMs

We would like to clarify that our method is essentially a new application paradigm of LLMs for subjective tasks. The pretrained LLMs can be replaced by any open-source multimodal language models. We apply HiVid on some widely adopted LLMs for deterministic inference in Table 1.

Table 1. Saliency accuracy of HiVid w/ open-source multi-modal LLMs.
| Metric | HiVid w/ GPT-4o | Llama-3.2-11B-Vision-Instruct | Qwen3-VL-8B-Instruct | InternVL3-14B | gemma-3-12b-it |
|:---:|:---:|:---:|:---:|:---:|:---:|
| PLCC$\uparrow$ | **0.66** | 0.58 | 0.60 | 0.64 | 0.61 |
| SRCC$\uparrow$ | **0.67** | 0.60 | 0.61 | 0.64 | 0.62 |
| mAP50$\uparrow$ | **0.86** | 0.80 | 0.81 | 0.84 | 0.82 |
| mAP15$\uparrow$ | **0.53** | 0.45 | 0.50 | 0.52 | 0.50 |

We can find that proprietary GPT-4o still outperforms with up to 13.7\% PLCC improvement. However, local inference can guarantee reproducibility and consistency across runs and can be further fine-tuned for specific needs. In general, larger models like InternVL yield better performance but require more computation, which is a controllable tradeoff.

To further demonstrate the robustness, we also report the performance across 5 runs for each proprietary LLM in Table 2.

Table 2. Accuracy consistency across runs.
| Metric | HiVid w/ GPT-4o | HiVid w/ Gemini-2-flash | HiVid w/ Grok2 | HiVid w/ Claude-3-haiku |
|:---:|:---:|:---:|:---:|:---:|
| PLCC$\uparrow$ | **0.66$\pm$0.03** | 0.60$\pm$0.03 | 0.62$\pm$0.02 | 0.53$\pm$0.05 |
| SRCC$\uparrow$ | **0.67$\pm$0.03** | 0.59$\pm$0.04 | 0.61$\pm$0.02 | 0.55$\pm$0.04 |
| mAP50$\uparrow$ | **0.86$\pm$0.02** | 0.81$\pm$0.05 | 0.82$\pm$0.02 | 0.81$\pm$0.03 |
| mAP15$\uparrow$ | **0.53$\pm$0.01** | 0.50$\pm$0.02 | 0.51$\pm$0.01 | 0.48$\pm$0.01 |

The low standard variance proves the stable correlation accuracy for each model. This means that LLMs generate most of the chunk-level scores via confident zero-shot subjective reasoning rather than random guess. This can be proved by the justified reason and score distribution in Fig. 10. Even when the absolute rating is slightly different across each run, e.g. due to model update, the variance can be alleviated by our final ranking and Gaussian Smoothing, as demonstrated by the degraded accuracy for HiVid w/o GS in Table 5 in main paper.

Main paper changes: lines 477-487

>**Question 1**: What compute environment is used for this inference, and how intense the runtime compute is?

All our experiments are conducted on GPU RTX 4090. The forecasting is only needed in live streaming scenario, and the execution frequency is every $m=10$ video chunk length, i.e. 10 seconds. The reason is that we only perform forecasting upon new LLM response, which is based on sliding window length $m$, as shown in Algorithm 1 in main paper. We stress that all LLM inference and prediction are executed asynchronously with video playback. No matter how long the LLM or forecasting delay is, we can adjust the prediction output length by Equ. 7 to ensure available future weights for ABRs. Therefore, the extra latency is near zero because we do not wait for the LLM or forecasting sequentially, as shown in Table 9 in main paper.

>**Question 2**: The table on page 15 appears disproportionately large

Thanks for the detailed suggestion, we have adjusted the figure size and updated the paper.

Main paper changes: lines 756-761, 785-794

>**Question 3**: Are MLPs also used for the QKV projections? Are CLIP weights updated? Would clarity improve if the CLIP vision encoder and text encoder were depicted separately?

No, we follow the standard QKV projections via matrices $W_Q, W_K, W_V$. No, we froze the CLIP during training. Yes, we have updated the forecasting model to depict the encoder separately in Fig. 6. We apologize for any confusion.

To better understand the forecasting procedure, we have added a pipeline workflow with Algorithm 1 as reference in lines 346-356. The key is the adaptive prediction length in Equ. 7. That said, no matter how long the LLM inference delay is, we only asynchronously perform forecasting upon new responses. Therefore, we can compute the LLM interval $\Delta t$ and derive the required output length in Equ. 7. For example, assume the LLM delay is 5 chunk length (5 seconds) and that forecasting takes 2 chunk length. We upload $m=10$ chunks to LLMs at Chunk 10, then at Chunk 15 we submit the time series, i.e. Rated Chunks 1-10 w/ frames, etc. We will receive the prediction at Chunk 17, and the prediction length is (5+2)+(10+N), the 5+2 is to compensate the elapsed time while the 10+N is the future weights required by ABRs. Because LLM is executed only every $m=10$ chunks, we can ensure that Chunk 17-17+10 all have future N weights for QoE optimization. If LLM interval is longer like 6 chunk length, then the forecasting happens at Chunk 16 with output length adjusted to (6+2)+(10+N).

Main paper changes: lines 281-282, 290-297, 320-322, 346-356, Fig. 6

>**Question 4**: It seems the positions of Table 3 and Table 4 swapped.

We apologize for this confusion. We confirm that they are correctly placed. The possible ambiguity is that we cited Table 4 before Table 3 to demonstrate that higher $m$ means more input tokens but higher performance. We have deleted the early citation to avoid any confusion.

Main paper changes: line 409
