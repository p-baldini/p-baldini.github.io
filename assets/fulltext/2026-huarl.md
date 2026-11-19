## Abstract

Generative AI and eXtended Reality (XR) have introduced to computer applications not only new challenges, but also the need to reconsider from a new perspective classical problems to which hardware and software systems may be subject to and their implications. For example, errors or disturbances in the communication channel between the physical and virtual worlds can lead to systems malfunctions and unintended output, on the one hand, but can also have implications on the creativity of the XR system, on the other hand. Indeed, in systems where Large Language Models (LLMs) mediate or generate responses, these processes can trigger novel and unexpected outputs, thereby representing affordances for creative interactions. In this paper, we explore this scenario by starting analyzing the robustness of LLMs to perturbations—in the form of scrambled character blocks of prompts—thus simulating a typical XR chat scenario where an avatar is driven by an LLM. As a preliminary study, we analyze the response behavior of two small LLMs using a limited sample size of 30 questions.

Keywords: LLM · XR · Noise robustness · Semantic Analysis

## 1 Introduction

In recent years we witnessed important advances in human-computer interactions. These led to major changes in the way humans interact with the environment. Specifically, the assistance of computers in everyday life creates new opportunities for both business and private life. In this respect eXtended Reality (XR) and Large Language Models (LLMs) are two major technological innovations. The former allows supporting human-environment interactions directly, providing additional information to the user and possibly guiding its actions. The latter promises to enhance the creation of the user and to instruct it with new knowledge. XR and LLM are thus two possibly complementary tools that could greatly enhance the work of humans [1].

Recent works already propose the use of LLMs to power human-XR interactions. The idea is to improve the conversation with the user, answering complex queries on-demand [11]. This allows more flexibility, as the system is now able to answer unexpected questions and perform basic reasoning. For instance, through XR the user can obtain information to perform specific tasks, such as assembling furniture [12]. Alternatively, LLMs can be used to enhance the system comprehension while controlling characteristics of a virtual scene [5]. This could be especially useful for construction workers and architects to visualize possible outcomes on the fly [1]. However, this flexibility is not limited to interactions with the environment. Indeed, some works proposed LLMs also as a way to enhance virtual avatars [3]. This consists in answers personalized on the users characteristics. For instance, the avatar could use a more inclusive language when dealing with people with specific sensibilities. This ability is especially useful when considering XR assisted teaching. Students with special needs due, for instance, to learning disorders could be assisted by a virtual instructor [7]. Some works even proposed a system to support the preparation process of underrepresented individuals by simulating work interviews [2]. The general enthusiasm in combining LLMs and XR is visible even by the increase in software for their integration. For instance, in [4] the authors present a framework to facilitate the creation of virtual characters communicating via LLM.

In this work, we explore the use of LLMs to power virtual avatars within a simulated XR environment. However, we do not focus on their plain use. Rather, we explore how possible speech-to-text conversion errors—specifically simulated as scrambled character blocks in prompts—might affect the correctness of the answers. The idea is that external environments full of noise might complicate the interaction of the user with the XR (see Fig. 1). This might lead to communication errors that affect the avatar’s answer. On the other hand, it is possible that the large amount of data used to train the LLMs enables an intrinsic resistance to noise. This would greatly facilitate outdoor deployment and might even provide a more reliable communication interface than human interlocutors in chaotic environments. While the robustness of LLMs to noise has been explored in prior literature—including ASR errors and typos [13], adversarial prompt perturbations [8], and comprehensive robustness benchmarking [15]—our study focuses specifically on small models (QWen-3-0.6B and QWen-3-1.7B) and examines how temperature parameters affect their robustness to noise. This model selection is motivated by the potential for on-device deployment in resource-constrained XR environments. Indeed, since the use of remote LLMs currently requires a good internet connection, their use in outdoors scenarios with possible connection issues is limited. On this regard, we propose validating the robustness to noise of LLMs of different sizes. The idea is that, if robust enough, small LLMs could be loaded directly on the XR device so to overcome connectivity problems.

We organize the work as follows. In Sect. 2 we discuss the experimental approach used. In Sect. 3 we present the results obtained and in Sect. 4 we discuss them. Finally, in Sect. 5 we summarize the work done, proposing future explorations.

**Figure 1:** Exemplification of the possible issues occurring while communicating with a virtual agent from a noisy environment. Images of the robot and observer are taken from <https://openclipart.org/>.

## 2 Methods

To investigate the impact of input perturbations on LLM performance, we designed a controlled experiment using a subset of the H3C dataset [6]. Specifically, we randomly sampled 30 question-answer pairs to ensure statistical validity while maintaining computational feasibility. Our methodology employs a perturbation-based approach that simulates realistic communication errors characteristic of XR environments.

We selected two models from the QWen family for comparative analysis: QWen-3-0.6B and QWen-3-1.7B. This model selection is motivated by two key considerations: first, their relatively small size makes them suitable for deployment in resource-constrained XR environments where computational efficiency is crucial; second, by choosing models from the same family (QWen-3), we can isolate and better understand the specific impact of model size on robustness to input perturbations.

### 2.1 Perturbation Methodology

Our perturbation strategy involves applying scrambled character blocks to the input text, simulating the type of communication errors that might occur in XR chat scenarios. We implemented contiguous block perturbations with a fixed block size of 5 characters, based on our analysis showing this approximates the average word length in the dataset. The rationale behind this approach stems from the tokenized processing nature of LLMs. By introducing localized perturbations, we can observe how these models handle corrupted input segments while maintaining some contextual information from unperturbed regions.

We evaluated three different perturbation intensities: 1 block perturbation (minimal disruption), 3 block perturbations (moderate disruption), and 6 block perturbations (high disruption). For example, a question such as:

`What are the main components of a computer system?`

With two blocks perturbation might become:

`What are the main compxy3zqnents of a comp9kl2mter system?`

### 2.2 Evaluation Metrics

To assess the impact of perturbations on model responses, we employed two main evaluation metrics: *answer relevancy* and *cosine semantic similarity*. We explain both in the following.

*Cosine Semantic Similarity:* We computed cosine similarity between text embeddings to quantify semantic alignment across three key relationships: original-to-perturbed questions ($Q_o \leftrightarrow Q_p$), original-to-perturbed responses ($R_o \leftrightarrow R_p$), and original questions to perturbed responses ($Q_o \leftrightarrow R_p$, termed *Counterfactual Answer*). This metric quantifies semantic content preservation by measuring the angular distance between embeddings in high-dimensional vector space, where semantically equivalent texts exhibit high cosine similarity regardless of lexical variations. Given two text embeddings $e_1$ and $e_2$, the cosine similarity is computed as:

$$\text{Cosine Similarity}(e_1, e_2) = \frac{e_1 \cdot e_2}{\|e_1\| \|e_2\|} \tag{1}$$

where $\cdot$ denotes the dot product and $\| \cdot \|$ represents the Euclidean norm.

While the emerging differences—if any—between the original and the perturbed responses give us a measure of the model-specific divergence given a combination of perturbation and temperature, the *Counterfactual Answer* similarity refers to the adherence of the response to the perturbed question with respect to the original question. This last measure better quantitatively capture the *“departure from the expectation”*, namely the deviation from the original question-answer pair induced by the specific perturbation-temperature experimental configuration.

*Answer Relevancy:* To evaluate whether perturbed responses maintain relevance to the original questions, we employed the Answer Relevancy metric from Llama Index[^1], which focuses on assessing how pertinent the generated answer is to the given prompt. A lower score is assigned to answers that are incomplete or contain redundant information, while higher scores indicate better relevancy. This metric is computed using the original question, and the answer of the perturbed question through a reverse-engineering approach. The Answer Relevancy is defined as the mean cosine similarity of the original question to a number of artificial questions, which are generated (reverse engineered) based on the answer using Gemini Flash 2.5:

$$\text{Answer Relevancy} = \frac{1}{N} \sum_{i=1}^{N} \text{Cosine Similarity}(E(q_i), E(q)) \tag{2}$$

where $E(q_i)$ is the embedding of the $i$-th generated question, $E(q)$ is the embedding of the original question, and $N = 3$ is the number of generated questions.

## 3 Results

In this section we present the statistics of the measures presented in the previous section for 30 different questions. In Fig. 2 the line chart of the trend of the mean of the relevancy score across the 30 samples is reported.

**Figure 2:** Left panel: QWen-3-0.6B; Right panel: QWen-3-1.7B.

From the plots some interesting trends emerge. The 0.6B model, in the case of no perturbation and 1-block, has a better relevancy score with lower temperatures than with higher temperatures. As predictable, with greater perturbations—i.e., with 3 and 6 blocks—the relevancy score is lower than in the other two cases, with temperature value 1 being the highest value. The trend seems to be the opposite in the case of model 1.7B, where higher relevancy values are, in general, obtained with higher temperatures, regardless of perturbations entities. Even in this case, as is to be expected, the cases with higher perturbations underperform compared to the others. Interestingly, the relevancy score of the 1-block perturbation case in correspondence to certain temperatures dominates the case with no perturbation, for both model sizes. Further experiments should be done in the future to confirm whether this may be an effect of sampling variability due to limited data or an actual positive effect of limited error entities in the prompt on LLMs’ accuracy.

Figure 3, instead, show the intra-model cosine similarity drop between the original and perturbed answers. In addition, in the figure, the similarity between original and perturbed questions is reported. This acts as baseline reference, allowing us to understand the extent of the decrease in similarity in the responses in relation to the drop observed in the questions. This provides a quantitative assessment of the extent of propagation of the perturbation from prompt to answer. Starting from no perturbation case, we can observe that while the two LLMs are fully deterministic system when temperature is set to 0, as expected, in general we observe a drop in similarity for the other temperature values used. The decrease in similarity is more pronounced for the smaller model (0.6B) than for the larger one (1.7B); while the medians are more stable in the smaller model than in the larger one, in the latter the median trend is decreasing as the temperature increases.

**Figure 3:** Figure showing the cosine similarity measure between the original and perturbed answers. From left to right, the perturbation cases are: no perturbation, 1 block, 3 blocks and 6 blocks for all the model used, i.e., QWen-3 with parameters 0.6B and 1.7B. The table on top of the plots represents the descriptive statistics (mean, standard deviation and median) of the cosine similarity between the original and perturbed questions. It plays the role of a baseline reference to better appreciate the extent of semantic changes in the responses induced by the perturbation.

When perturbations come in, we can observe a decrease in similarities when the temperature is set to 0 (boxplots with blue color) with a trend towards decreasing median values as the magnitude of the perturbation increases. Interestingly, their maximum values remains equals to one until 3-blocks perturbation for both models. This means that, for some questions, the models answer semantically “the same” to perturbed and unperturbed question, with the temperature set to 0. However, statistically, for 0.6B this is not sufficient to maintain the good average relevancy score obtained in the case of no perturbation (see the left-hand side of Fig. 2).

In general, for more intense perturbations (3-blocks and 6-blocks) the trend of the medians is not monotonic, with—depending on perturbation intensity—higher median values for temperature 1 or 1.5. Another interesting aspect worth mentioning is the fact that, excluding the case of the most intense perturbation, with a temperature of 1.5, and in some cases even 2, the relevancy score of the 1.7B model obtains higher values than those obtained with temperature 1, the standard value used in the LLMs out-of-box configurations. Perhaps, the decrease in the similarity between the responses observable in the boxplots—and thus the relative loss of intra-model coherence obtainable by using a higher temperature—helps the model to provide a more correct answer. In any case, more in-depth analyses are needed in future work to confirm this hypothesis.

With the *Counterfactual Answer* analysis, on the other hand, we try to test, using a simpler and lower level measure, whether the results presented with the relevancy score are in any way reproducible without using a third party LLM as judge (see Fig. 4).

The first result is that already in the case of no perturbation the trend of boxplot is not monotonic with higher median values at temperatures of 1, and lower values going towards the left and right extremes. This trend is slightly more pronounced for the larger model than for the smaller one. The temperature of 0 yields the poorest results in terms of similarity between the perturbed answer and the original question, thus the greatest magnitude of drift from the expected, i.e., what one would have expected as an answer from the question asked.

In general, the trend mentioned above—i.e., that the models show higher values with temperature 1 than with the other temperatures—remains valid for all perturbation, except for the 6-blocks one (case of severe disturbance), where the models respond better with the highest temperature values (1.5 and 2.0) than with 1. This, again, is more evident in the model with 1.7B parameters, in this case we also observe generally higher similarity values than in the smaller model. These results are so in accordance with the relevancy analysis presented before.

Table 1 shows an example of original and perturbed (6-blocks) questions and answers for the LLM with parameter size 1.7B and temperature value 1.

**Table 1:** Example of original and perturbed questions and answers for the 1.7B LLM with temperature = 1.

| | |
|:--|:--|
| Original Question | Why do movie companies build sets rather than film on scene? Seems like any location will do and seems like it’s cheaper to find an existing location rather than building their own. But I’m open to being wrong. |
| Original Answer | Why would the movie companies do that? Is there any reason that would be more economic? Is there something about the cost of making a movie where building sets is more economical? Yes, but I think I have to consider the cost of the film and the cost of the labor involved as well as the cost of the film equipment and the costs of the locations. I think that the movie companies do it because they want to make the film more expensive, and that is why they do not use real locations, but that seems like a stretch to me. But if the movie is not made with an actual location, and the film is shot in a made-up location, then how would you know it is real? Well, I think that the reason is that they want to make the film more exciting and to allow the actors to be more realistic. If the movie is set in an actual location, then the actors are not in costume, which would be a big expense. Also, they could be in a place that is not real and that could have a lot of natural beauty, and it would be possible to shoot the movie in the same place, and have the same look, but it would be more economic. If you can make a movie in a |
| Perturbed Question (6-blocks) | Why dohosize companies buildwuwrn rather than film on scene? Qtarg libacghy location will do and seems like it’s cheaper to find an existing location rather thanxrcsnding their own. But hexdvopen to being wrong. |
| Perturbed Answer (6-blocks) | 1000x in the past, the same question has been asked by users and the answer is: it’s not about cost - it’s about the final result. If you’re filming on scene, you have to have an expert in the field, a field specialist. If you’re shooting a commercial for a product, you need the specialist in the field for that product to be able to show the product in the best light, in the best condition. But the film on scene is not a movie. It’s a documentary, but that’s not the same as a movie. The documentary may have the same quality as a movie - but the difference is that it’s not a movie. But if you’re trying to make a movie, you need the film to be on a movie. So you need to use the best possible location, but the location may not have a person, but it does have a scene. This is the same as saying: if you want to make a movie, it’s better to find a location with a person in it. If you want to make a movie about a person, you need to film them in the field, not on a location. You can’t have a movie that’s just a place. That’s a |

## 4 Discussion

Approaches combining multimodal sensory systems, generative AI, XR and human interactions impose novel challenges. These challenges range from technological constraints, such as the need to maintain low latency in the response of XR applications involving human interactions and LLMs, to new methodological and analytical approaches, since these are full-fledged complex systems with unpredictable behaviors under certain conditions. The point of view of this paper is precisely to analyze *LLMs as dynamical systems subject to perturbations* in order to asses their robustness. We therefore chose to test the impact of different perturbation intensities on relatively small LLMs, to meet the needs of the devised XR application scenario where computational efficiency is crucial.

**Figure 4:** The figure shows the trends of the *Counterfactual Answer* measure— namely the cosine similarity between the answer to the perturbed question and the original question—for all the perturbation experimental cases {no perturbation, 1-block, 3-block, 6-blocks} and for both model sizes {0.6B, 1.7B}.

Our findings reveal that, at the current stage, model robustness is significantly challenged when perturbations are introduced, as evidenced by the clearly decreased performance from the 1-block perturbation case to the 3-block and 6-block perturbations. We also discovered that not only model size matters, but the temperature parameter plays a crucial role in model performance, particularly in conjunction with model size. Indeed, larger models demonstrate better capability in handling perturbations under high temperature regimes, while smaller models clearly become more stochastic with higher temperatures, thus leading to more unpredictable behavior.

Interestingly, our results suggest that higher temperature settings might paradoxically improve response correctness in perturbed scenarios, challenging the conventional belief that increased temperature primarily enhances creativity at the expense of accuracy. This observation opens new questions about the relationship between stochasticity, creativity, and robustness in LLMs.

Furthermore, the increase in performance observed with the larger model in the case of 1-block perturbation compared to the case without perturbation leaves room for the hypothesis that to some extent errors can improve performance, by being a source of affordances [10].

A current limitation of the work is the fact that the methodological framework used to evaluate the robustness cannot discriminate between *weak* and *strong* hallucinations. For instance, answers that are grammatically correct but without any meaning are comparable to answers formed from sequences of random characters or numbers, as happened for some rare collected samples. In the future, new methods will be needed to better discriminate between these cases and to start analyzing the topology of the attractor landscape underpinning LLM responses—where the attractors or sets of attractors responsible for hallucinations reside.

## 5 Conclusion

In this work we consider a future scenario where LLMs and XR are seamlessly integrated in an augmented world where AI agents assist humans in their daily activities. In such a scenario, we pose the following question: *“How robust are small models to perturbations in the input, and how does this affect their performance?”* To answer this question, we conducted a preliminary study on the robustness of two small LLMs (QWen-3–0.6B and QWen-3–1.7B) to input perturbations, simulating a typical XR chat scenario.

Results show that, in general, LLMs’ robustness decreases when perturbation in the form of scrambled character blocks are introduced. We observed that temperature and size jointly affect the robustness. Interestingly, in perturbed scenarios, higher temperatures increase the correctness of LLMs and not their creativity in the sense of the production of something unexpected.

In future work, we plan to examine the entire family of models to identify trends in robustness and potentially discover a “sweet spot” in the size-temperature parameter space, potentially related to *criticality* from the perspective of dynamical systems theory [9,14]. We plan to extend our analysis to larger models, such as QWen-3-7B and QWen-3-14B, to validate whether the trends observed here remain consistent across different model scales. We also intend to investigate what happens when perturbations persist throughout multi-turn conversations, affecting not only the input but also the model’s output across multiple dialogue exchanges.

## References

[1] Afzal, M., et al.: Next generation xr systems–large language models meet augmented and virtual reality. IEEE Comput. Graphics Appl. 45(1), 43–55 (2025)

[2] Ajri, S., Nguyen, D., Agarwal, S., Padala, A., Yildirim, C.: Virtual aivantage: leveraging large language models for enhanced vr interview preparation among underrepresented professionals in computing. In: Proceedings of the 22nd International Conference on Mobile and Ubiquitous Multimedia, MUM 2023, pp. 535–537. ACM, New York, NY, USA (2023)

[3] Bozkir, E., Özdel, S., Lau, K., Wang, M., Gao, H., Kasneci, E.: Embedding large language models into extended reality: opportunities and challenges for inclusion, engagement, and privacy. In: Proceedings of the 6th ACM Conference on Conversational User Interfaces, CUI 2024, pp. 1–7. ACM, New York, NY, USA (2024)

[4] Buldu, K., et al.: Cuify the xr: an open-source package to embed llm-powered conversational agents in xr. In: 2025 IEEE International Conference on Artificial Intelligence and eXtended and Virtual Reality (AIxVR), pp. 192–197. IEEE (2025)

[5] Chen, J., Grubert, J., Kristensson, P.: Analyzing multimodal interaction strategies for llm-assisted manipulation of 3d scenes (2024)

[6] Guo, B., et al.: How close is chatgpt to human experts? comparison corpus, evaluation, and detection. arXiv preprint [arxiv:2301.07597](http://arxiv.org/abs/2301.07597) (2023)

[7] Hajahmadi, S., Clementi, L., Jiménez López, M., Marfia, G.: Arele-bot: inclusive learning of spanish as a foreign language through a mobile app integrating augmented reality and chatgpt. In: 2024 IEEE Conference on Virtual Reality and 3D User Interfaces Abstracts and Workshops (VRW), pp. 335–340. IEEE (2024)

[8] Mu, L., Chu, G., Ni, L., Sang, L., Wu, Z., Jin, P., Zhang, Y.: Robustness of prompting: enhancing robustness of large language models against prompting attacks. arXiv preprint [arXiv:2506.03627](http://arxiv.org/abs/2506.03627) (2025)

[9] Nakaishi, K., Nishikawa, Y., Hukushima, K.: Critical phase transition in large language models (2024)

[10] Roli, A., Braccini, M., Stano, P.: On the positive role of noise and error in complex systems. Systems 12(9) (2024). <https://doi.org/10.3390/systems12090338>

[11] Shabanijou, M., Sharma, V., Ray, S., Lu, R., Xiong, P.: Large language model empowered spatio-visual queries for extended reality environments. In: 2024 IEEE International Conference on Big Data (BigData), pp. 5843–5846. IEEE (2024)

[12] Srinidhi, S., Lu, E., Rowe, A.: Xair: An xr platform that integrates large language models with the physical world. In: 2024 IEEE International Symposium on Mixed and Augmented Reality (ISMAR). pp. 759–767. IEEE (2024)

[13] Wang, B., Wei, C., Liu, Z., Lin, G., Chen, N.F.: Resilience of large language models for noisy instructions. In: Al-Onaizan, Y., Bansal, M., Chen, Y. (eds.) Findings of the Association for Computational Linguistics: EMNLP 2024, Miami, Florida, USA,12–16 November 2024, pp. 11939–11950. ACL (2024). <https://doi.org/10.18653/V1/2024.FINDINGS-EMNLP.697>, <https://doi.org/10.18653/v1/2024.findings-emnlp.697>

[14] Zhang, et al.: Intelligence at the edge of chaos (2024)

[15] Zhu, K., et l.: Promptrobust: Towards evaluating the robustness of large language models on adversarial prompts. In: Li, B., Xu, W., Chen, J., Zhang, Y., Xue, J., Wang, S., Bai, G., Yuan, X. (eds.) Proceedings of the 1st ACM Workshop on Large AI Systems and Models with Privacy and Safety Analysis, LAMPS 2024, Salt Lake City, UT, USA, 14–18 October 2024, pp. 57–68. ACM (2024). <https://doi.org/10.1145/3689217.3690621>, <https://doi.org/10.1145/3689217.3690621>

[^1]: <https://github.com/run-llama/llama_index>.
