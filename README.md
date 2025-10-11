Dear Chairs, we'd like to raise concerns about some grave factual errors and unprofessional comments regarding reviewer 8Gbe. It is likely that the reviewer lacks a basic understanding and denies the whole watermarking community with subjective bias.

**Factual error 1**: The reviewer assumes the attack implementation does not work and denies their effectiveness (Weakness 1). However, all our attacks are the main trending topics in many digital and generative watermarking works, e.g., Wevade-CCS23 and VINE-ICLR25. They have long been adopted for practical evaluation in real world like JPEG compression and have proven solid performance. Moreover, **all other 4 reviewers unanimously acknowledge our comprehensive attack defense experiments**. This means the reviewer may hold subjective bias on the whole watermarking domain and come to an unfair and unprofessional conclusion.

**Factual error 2**: The reviewer assumes our loss optimization is contradictory without prior reference and thus denies our effectiveness (Weakness 2+3). However, all our loss paradigms strictly follow the standard training pipeline adopted in many watermarking works, e.g., the first learning-based HiDDeN-ECCV18 and the latest WOFA-CVPR25. These basic loss designs have undergone extensive evaluation and theoretical analysis, and our experiments have also proven effective and achieved the SOTA. Moreover, **all other 4 reviewers unanimously acknowledge our novel adversarial training loss pipeline**. This means the reviewer may lack a basic background on watermarking objective and learning optimization.

In conclusion, these hasty and unprofessional comments may not yield a confidence as high as 4. Moreover, the reviewer may have negative and subjective bias on the whole watermarking community which may result in an unfair and unjustified rating. We sincerely ask the SPC, AC and PC to take these concerns into consideration when making the final decision.

Thanks for your valuable help and understanding!




**1. Time Analysis**: Training: Our (EAT)=O(1/2*n), others (JAT)=O(n), n is steps. Inference: Encoder+Decoder=(0.93+0.95)s which ranks middle (see Appendix Fig. 2).

**2. Theorem Analysis**: The empirical results with $L_2$: $\alpha=0.012, \delta=0.007$, actual $\eta_2$ bound is $0.010>\alpha-\delta$, validating our assumption.

To **NpMA**:

||Rotation|Padding|PIP|Combined|Reg+Adv|Adv+Reg|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Ours|1.0|0.98|0.99|0.78|0.87|0.81|
|DWSF|0.97|0.98|0.98|0.73|0.68|0.62|
|MBRS|0.94|0.95|0.89|0.69|0.7|0.65|

1. Resize/scaling is in Table 5. We test more with a new baseline DWSF-MM23 where ours can still surpass by 5%.

2. Combined distortions are in Table 1. Regen and Adv reduce robustness at low attack PSNR~22.3.

3. Fig. 7a & 8b show the Wevade accuracy preservation; Because training on all attacks lowers ACC (see Challenge 2).

4. All attack simulations use Wevade codes.

To **8Gbe**:

1. Distortions and Regen attacks are the main focus adopted in real digital and diffusion generative watermarking (see Discussion Appendix); The combined attacks are stress tests adopted in many works like MBRS and also supported by reviewer **NpMA**; Please see Analysis 1.

2. The guarantee is from loss optimization process (Equ. 6+10). Each step yields a better tradeoff between quality and ACC. No. n is the final robust one.

3. The loss follows standard and harmonic paradigms in many works: Robustness La, Clean ACC Lw and Quality Li.

To **awCF**:

1-2. The new PGD is to ensure the quality lower bound.

3-5. DADW is a baseline; Ours is not only SOTA but provides 2 insights; Please see Analysis 1+2.

||PSNR|JPEG|V1-4|Wevade|
|:-:|:-:|:-:|:-:|:-:|
|Ours|37.0|1.0|0.87|0.98|
|Ours w/o new PGD|36.4|1.0|0.88|0.98|
|MBRS-ori|31.8|0.99|0.53|0.49|
|PIMoG-ori|35.3|0.81|0.51|0.5|

6-7. The quality degrades w/o new PGD; The original methods are worse without Regen+Wevade training.

To **zoLk**:

1-2. Please see Analysis 1.

3.  Ours w/ different settings are still SOTA in Table 3, because some methods like StegaStamp need fixed input.

To **L5QU**:

1. $L_{i_2}$ is evaluated in Table 3. Other params yield a similar tradeoff.

||PSNR/ACC|JPEG|V1-4|Wevade|
|:-:|:-:|:-:|:-:|:-:|
|Ours|37.0/1.0|1.0|0.87|0.98|
|EditGuard-Noise|36.9/1.0|0.83|0.52|0.48|
|VINE-R|36.7/1.0|1.0|0.66|0.51|
|Stable-S w/ EAT|30.8/0.99|0.79|0.64|0.76|

2-3. We test with H=W=128 where ours is still SOTA; Stable-S w/ EAT can improve clean ACC with stable robustness.

4-5. Please see Analysis 2.
