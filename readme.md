# Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]

Dissecting the Full-Link of 5G Physical Layer: Coverage, Modulation, MIMO, OFDM, and AI Scheduling Performance Reality
In today's era of large-scale deployment of 5G networks, we often discuss peak rates, end-to-end latency, and continuous coverage, yet rarely delve into the underlying logic behind these metrics—how far can base station signals travel in urban environments? How significant is the error rate difference between different modulation methods? What is the multiple rate gain that MIMO can bring? How can we balance performance and cost with the challenge of peak signal-to-noise ratio (PSNR) in OFDM?
A recent MATLAB and Python-based simulation report on LTE/5G metropolitan broadband systems has fully constructed an end-to-end transmission link from channel propagation, modulation and demodulation, MIMO equalization, to OFDM transmission and machine learning adaptive scheduling, completing a full-process performance quantitative assessment based on the 3GPP standard specifications.
In this article, we dissect the core conclusions to clearly understand the real performance boundaries of the five core modules of the 5G physical layer through actual measurement data.
1. Urban macrocell coverage boundary: The truth about signal propagation under 2.6GHz
To talk about system performance, the first step is always channel. In urban environments, buildings' shadowing, reflection, and diffraction superimpose, leading to a rapid signal strength decay with distance, directly determining the base station's coverage radius and service quality.
This simulation adopts the COST 231-Hata empirical path loss model commonly used in the telecommunications industry, setting the carrier frequency at 2.6GHz (the mainstream Sub-6GHz band for 5G), the base station antenna height at 30m, the terminal antenna height at 1.5m, the transmit power at 43dBm (20W), and the channel bandwidth at 10MHz, covering a city scene from 0.1 to 5km.
The core conclusion is very intuitive:
Path loss grows logarithmically with distance: approximately 120dB at 200m, 143dB at 1km, and 169dB at 5km;
Received SNR from 100m with a rapid drop of 38dB, dropped below 20dB (64-QAM threshold) at 400m, and fell below 10dB (16-QAM threshold) at 700m;
Based on modulation capability, coverage zones are divided: within 250m, high-order modulation services such as 16/64-QAM can be provided; between 250~600m, only QPSK basic connection is available; beyond 3.3km, signals completely drown in noise.
This result also explains the core pain point of 5G deployment: the path loss in the 2.6GHz frequency band is significant, and the effective high-order modulation coverage radius of a single macro station is only hundreds of meters. Therefore, dense urban areas' 5G networks must ensure continuous high-speed experiences through methods such as reducing cell site spacing, stacking small cells, and large-scale MIMO beamforming.
QAM modulation performance: The theoretical and measured gap of 10-15dB high-order QAM is core for 5G high spectral efficiency. However, there is often a significant gap between ideal formulas and actual channel performance.
Simulation tests for 16-QAM modulation under AWGN channels, comparing theoretical bit error rate with Monte Carlo simulation results, and measuring the industrially important metric error vector magnitude (EVM):
At 30dB SNR, system EVM can reach 8.06%, showing a typical right-skewed distribution of symbol errors - most symbols have very little error, but in deep fading scenarios, there are significant errors;
The simulation bit error rate has a performance gap of 10~15dB compared to theory: theoretically, 13dB SNR can achieve a bit error rate of 10⁻⁴, but in simulations, 30dB SNR can only reach a level of 10⁻².
This gap is not a simulation error but rather reflects the real constraints of the actual system: carrier synchronization deviation, timing error, residual inter-symbol interference, and digital quantization precision, all of which can degrade the demodulation performance. It is precisely this gap that highlights the value of channel coding - the Turbo codes in LTE, LDPC and Polar codes in 5G NR, are precisely through encoding gains of 5~8dB to bridge the gap between theory and reality, achieving reliable transmission.
III. MIMO Rate Doubling: ZF vs. MMSE Equalization Performance Showdown for Multi-antenna MIMO is the core technology for 5G capacity multiplication, transmitting multiple data streams over the same frequency through spatial multiplexing. However, different receiving equalization algorithms ultimately exhibit vastly different performance.
A simulation setup for a 2x2 spatial multiplexing MIMO system, comparing \\ Zero Forcing (ZF) with Minimum Mean Square Error (MMSE)\\ two classic linear equalization schemes:
EVM performance: under a 20dB SNR, ZF equalization EVM is 39.4%, while MMSE equalization EVM is 24.8%, showing a significant advantage of MMSE;
Error rate performance: at a target BER of 10⁻², MMSE has a 2~4dB SNR gain compared to ZF;
Capacity gain: under a 20dB SNR, the throughput capacity of 2x2 MIMO reaches 1.87 times that of SISO, approaching the theoretical limit of 2 times; when expanded to 4x4 MIMO, the capacity can reach 3.5 times that of SISO at a 30dB SNR.
The difference lies in the trade-off between design philosophy: ZF forces complete elimination of inter-stream interference, but it can significantly amplify noise when faced with poor channel conditions or matrix instability; MMSE sacrifices residual interference for noise suppression, offering stronger stability in practical fading channels and thus becoming the mainstream choice in commercial systems.
IV. OFDM Peak-to-Average Power Ratio (PAPR) Challenge: A Comparative Analysis of Three Suppression Strategies
OFDM is the core transmission waveform for 4G/5G, combating frequency selective fading through a large number of orthogonal subcarriers. However, it has an inherent defect: Peak-to-Average Power Ratio (PAPR) — when multiple subcarriers are in phase addition, the instantaneous power can be much higher than the average power, forcing power amplifiers to reserve a significant amount of backoff, which not only increases costs but also reduces energy efficiency.
Simulation comparisons were conducted on the performance of three mainstream PAPR suppression strategies: 3dB clipping, 6dB clipping, clipping followed by filtering, and Selective Mapping (SLM, with 8 candidate sequences). The evaluation was comprehensively assessed from four dimensions: PAPR reduction magnitude, spectral proliferation, bit error rate (BER), and EVM.
The final conclusion is very clear:
1. Clipping methods have the most direct effect in reducing peak levels (3dB clipping can reduce about 47%), but they introduce severe out-of-band spectrum radiation and intra-band distortion, significantly degrading EVM and are only suitable for low-cost scenarios with low spectral requirements.
2. Filtering methods can suppress the out-of-band radiation caused by clipping, but they introduce inter-symbol interference, slightly increasing the bit error rate, representing a compromise solution.
3.
Selective Mapping (SLM)
has the best overall performance: it can achieve approximately 3.2dB of PAPR reduction, completely avoiding spectrum distortion, without worsening the BER or EVM, making it the only option that slightly increases the computational complexity at the transmitter end.
This aligns with the standard practice in 5G NR: SLM-type schemes, due to their advantages of no distortion and pure spectrum, have become the mainstream PAPR suppression technology approach for downlink links.
V. AI-Driven Scheduling: Real-Emission Performance of Machine Learning Adaptive Modulation
Traditional adaptive modulation (AMC) relies on fixed SNR threshold tables, which show significant degradation in decision robustness under conditions of noisy channel estimation, hardware distortion, and non-stationary environmental conditions. In contrast, machine learning classifiers can directly learn decision boundaries from data, adapting to more complex real-world scenarios.
This simulation is based on 2000 synthetic channel samples (SNR, distance, noise floor) and trained three models: random forest, logistic regression, and support vector machine. It completed three-class modulation identification (QPSK/16-QAM/64-QAM):
The random forest classifier performed the best, with an accuracy of 91.5% on the test set;
Feature weights show that SNR is the absolute dominant feature (importance over 85%), while distance and noise floor provide auxiliary corrections at the threshold boundary;
Compared to hard threshold rules, ML classifiers are more robust to label noise and measurement errors, especially at the transition boundaries between 10dB and 20dB modulation switching points, where decisions are smoother and more reliable.
This result also confirms the application value of AI in physical layer: moving from fixed rules to data-driven, which is an important direction for future 6G smart communication.
In conclusion, physical layer design is never a competition of single-point technologies but a linkage and balancing of an entire chain: path loss determines coverage range, coverage decides received SNR, SNR decides the number of modulation levels that can be supported, MIMO achieves capacity multiplication in frequency dimension, OFDM solves the complexity of multipath transmission, and finally, AI scheduling continuously approaches optimal performance in dynamic channels.
Although this simulation is a course-level project, it follows 3GPP specifications throughout, and its core conclusions are highly consistent with industrial measurements, clearly demonstrating the design logic and performance boundaries of modern cellular communication at the physical layer. Looking ahead, introducing LDPC coding, large-scale MIMO beamforming, end-to-end joint simulation and hardware-in-the-loop verification will also be essential steps for further engineering implementation of this system.

## Images

![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](01.jpg)
![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](02.jpg)
![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](03.jpg)
![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](04.jpg)
![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](05.jpg)
![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](06.jpg)
![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](07.jpg)
![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](08.jpg)
![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](09.jpg)
![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](10.jpg)
![Dissecting the full-link 5G physical layer: coverage, modulation, MIMO, OFDM, and AI scheduling performance truths [with MATLAB code]](11.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
