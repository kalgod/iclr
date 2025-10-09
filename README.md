**1. Time Analysis**: In Appendix Fig. 2, our decoding takes only 0.95s and encoding is as low as (0.93+n*0.2)s, adjustable for speed/accuracy (ACC).

**2. Theorem Analysis**: The empirical results with $L_2$: $\alpha=0.012, \delta=0.007$, actual $\eta_2$ bound is $0.010>\alpha-\delta$, validating our assumption.

To **NpMA**:

||Rotation|Padding|PIP|Combined|Reg+Adv|Adv+Reg|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Ours|1.0|0.98|0.99|0.78|0.87|0.81|
|DWSF|0.97|0.98|0.98|0.73|0.68|0.62|
|MBRS|0.94|0.95|0.89|0.69|0.7|0.65|

1. Resize/scaling is in Table 5. We test more with a new baseline DWSF where ours can still surpass by 5%.

2. Combined distortions are in Table 1. while Regen and Adv reduce robustness at low attack PSNR~22.3.

3. Fig. 7a & 8b show the Wevade accuracy; Because training on all attacks lowers ACC (see Challenge 2).

4. All attack simulations use Wevade codes.

To **8Gbe**:

1. Distortions are quite typical while Regen attacks emerge from trending diffusion model (see Discussion Appendix); The combined attacks are stress tests adopted in many works like MBRS and also supported by reviewer **NpMA**; Please see Analysis 1.

2. The guarantee is from loss optimization process (Equ. 6+10). Each step manifests a better tradeoff between quality and ACC. No. n is the final robust one.

3. The objective loss includes robustness La, clean ACC Lw and quality Li, which are standard paradigms in watermarking.

To **awCF**:

1-2. The new PGD is to ensure the quality lower bound.

3-5. DADW is a baseline; AdvMark is the SOTA with some overhead; Please See Analysis 1 and 2.

||PSNR|JPEG|V1-4|Wevade|
|:-:|:-:|:-:|:-:|:-:|
|Ours|37.0|1.0|0.87|0.98|
|Ours w/o new PGD|36.4|1.0|0.91|1.0|
|MBRS-ori|31.8|0.99|0.53|0.49|
|PIMoG-ori|35.3|0.81|0.51|0.5|

6-7. The ablated ACC improves but quality drops more. The original baselines are worse without Regen+Wevade training.

To **zoLk**:

1-2. Please see Analysis 1.

3. We've covered Advmark w/ different settings in paper Table 3, because some methods like StegaStamp need fixed input.

To **L5QU**:

1. $L_{i_2}$ is evaluated in paper Table 3. Other params yield a similar tradeoff.

||PSNR/Clean|JPEG|V1-4|Wevade|
|:-:|:-:|:-:|:-:|:-:|
|Ours|37.0/1.0|1.0|0.87|0.98|
|EditGuard-Noise|36.9/1.0|0.83|0.52|0.48|
|VINE-R|36.7/1.0|1.0|0.66|0.51|
|Stable-S|30.8/0.99|0.79|0.64|0.76|

2-3. We test with H=W=128 where ours is the SOTA; Stable-S with EAT does improve clean ACC with stable robustness.

4-5. Please see Analysis 2.
