



---
title: "Building a Virtual Cell Lab: Practical AI for Biotech and CDMO Workflows"
date: 2025-04-01
categories: [biotech, machine-learning, virtual-cell,single-cell, perturb-seq, foundation-models]
---

# Building a Virtual Cell Lab: Practical AI for Biotech and CDMO Workflows



Walk into almost any biotech boardroom today and you will hear the same pitch:

*"Just fine-tune an LLM on our omics data."*

*"Treat the genome like a sentence."*

*"Scale the model, the biology will follow."*

It sounds attractive. LLMs transformed language, so why not genomics? The problem is that biology is not text, and pretending it is has already burned a lot of time, money, and GPU hours.

If we want AI that actually helps cell therapy and CDMO programs, we need to start from biology, not from hype. That is where the idea of a **virtual cell lab** comes in.

A virtual cell lab is not a chatbot for scientists. It is a set of models and data flows that can run virtual cell experiments, then feed those results back into real programs. It is a bridge between the messy reality of cell culture and the mathematical world that other sciences now live in.

---

## The Promise and the Hype: Understanding What a Virtual Cell Could Be

The vision of an AI-powered virtual cell has captured the imagination of researchers and industry alike. According to a landmark perspective published in *Cell* by Bunne et al., an AI Virtual Cell (AIVC) would be a multi-scale, multi-modal large neural network-based model capable of representing and simulating the behavior of molecules, cells, and tissues across diverse states ([DOI](https://doi.org/10.1016/j.cell.2024.11.015)). The authors, representing a consortium including the Chan Zuckerberg Initiative, Stanford, Genentech, and numerous other institutions, argue that such models could transform biological research by enabling high-fidelity simulations, accelerating discoveries, and guiding experimental studies.

This is not mere speculation. The paper outlines concrete capabilities that an AIVC should possess: generating universal representations of biological entities across scales, and facilitating interpretable in silico experiments to predict and understand cellular behavior. Yet the authors are also candid about the challenges ahead, including data needs, evaluation strategies, and community standards.

The question for biotech companies and CDMOs is not whether this vision will eventually be realized, but what practical steps can be taken today to move toward simulation-guided biology while the field matures.

---

## Biology Is Not Language: The Structural Problem with Genome-as-Text Models

Large language models work because human text is dense in meaning. Most words in a sentence carry useful information, and most relationships are local. A Transformer can look at nearby tokens and recover the structure of the sentence quite well.

Genomes and cell state data look nothing like this:

1. Only a small fraction of the genome codes for proteins. Vast stretches are regulatory, structural, or of unknown function.

2. Regulatory elements are scattered, often millions of bases away from the genes they control. Long-range chromatin interactions, enhancer-promoter loops, and three-dimensional nuclear architecture create dependencies that span enormous genomic distances.

3. Real function depends on three-dimensional folding, chromatin accessibility, transcription factors, signaling networks, nutrient states, and stochastic noise.

Single-cell data adds another layer of complexity. It is high-dimensional, sparse, and strongly context-dependent. Two cells with the same surface markers can behave differently because of subtle differences in history and microenvironment. As Gulati et al. describe in their comprehensive review of single-cell and spatial transcriptomics, understanding cellular identity requires integrating information across multiple scales and modalities ([DOI](https://doi.org/10.1038/s41580-024-00768-2)).

This is why simple "genome as text" LLMs struggle once you move from toy problems to real functional tasks. They were built for a world where almost every token carries meaning and most dependencies are short-range. Biology is the opposite.

---

## The Foundation Model Landscape: Promising but Imperfect

Despite these structural challenges, significant progress has been made in developing foundation models for single-cell biology. These models attempt to learn generalizable representations from large-scale datasets that can then be fine-tuned for specific tasks.

### scGPT and the Promise of Transfer Learning

The scGPT model, published in *Nature Methods* by Cui et al., represents one of the most ambitious efforts to build a foundation model for single-cell biology ([DOI](https://doi.org/10.1038/s41592-024-02201-0)). Trained on a repository of over 33 million cells using a generative pretrained transformer architecture, scGPT attempts to distill biological insights concerning genes and cells. The authors demonstrate that through transfer learning, the model can be optimized for diverse downstream applications including cell type annotation, multi-batch integration, multi-omic integration, perturbation response prediction, and gene network inference.

However, it is crucial to understand what these models can and cannot do. While scGPT shows impressive performance on certain benchmarks, the gap between benchmark performance and real-world utility in a manufacturing or clinical context remains substantial. Models that work well on held-out test sets may fail when confronted with the specific cell types, donors, and process conditions that matter for a particular program.

### GEARS: Integrating Knowledge Graphs with Deep Learning

A particularly promising approach comes from GEARS (Graph-Enhanced Gene Activation and Repression Simulator), published in *Nature Biotechnology* by Roohani et al. ([DOI](https://doi.org/10.1038/s41587-023-01905-6)). Rather than treating gene expression as an unstructured vector, GEARS integrates deep learning with a knowledge graph of gene-gene relationships to predict transcriptional responses to both single and multigene perturbations.

The key insight is that GEARS can predict outcomes of perturbing gene combinations that were never experimentally tested. The authors report that GEARS exhibited 40% higher precision than existing approaches in predicting genetic interaction subtypes in a combinatorial perturbation screen. This ability to generalize to unseen perturbation combinations is exactly what is needed for practical virtual screening applications.

### GenKI: Virtual Knockout for Gene Function Prediction

Another innovative approach is GenKI (Gene Knockout Inference), described by Yang et al. in *Nucleic Acids Research* ([DOI](https://doi.org/10.1093/nar/gkad450)). GenKI uses variational graph autoencoders to learn latent representations of genes and their interactions from single-cell gene regulatory networks. The model can then generate virtual knockout data by computationally removing the edges of a target gene, predicting how the system would respond without requiring actual knockout experiments.

This type of in silico perturbation analysis could partially replace the need for genetically modified animals or other expensive perturbation systems. The simulations show that GenKI accurately approximates perturbation profiles and can predict cell type-specific functions of knockout genes.

### Scaling Up: C2S-Scale and Closed-Loop Learning

Recent work has pushed the boundaries of what these models can achieve. The C2S-Scale (Cell2Sentence Scale) approach, described in a recent preprint by Rizvi et al., trains large language models on a corpus comprising over one billion tokens of transcriptomic data, biological text, and metadata ([DOI](https://doi.org/10.1101/2025.04.14.648850)). Scaling the model to 27 billion parameters yielded consistent improvements in predictive and generative capabilities.

Perhaps more importantly, work by Pershad et al. introduces a "closed-loop" framework that extends foundation models by incorporating perturbation data during fine-tuning ([DOI](https://doi.org/10.1101/2025.07.08.663754)). This allows the model to learn from experimental results and improve its predictions iteratively, representing a crucial step toward realizing truly useful virtual cell models.

---

## The Data Infrastructure Problem

Even if we had perfect modeling tools, many organizations would still struggle to use them well because of how data and decisions are handled today.

### Reproducibility Remains a Systemic Challenge

Biology has some of the highest rates of data problems and statistical shortcuts among scientific fields. Retractions, p-hacking, and selective reporting are not rare stories; they are systemic issues. As Macleod and Mohan discuss in their review of reproducibility in biomedical research, weaknesses in the design, conduct, and reporting of experiments have significant impacts on the development of new treatments ([DOI](https://doi.org/10.1093/ilar/ilz015)).

Common patterns include:

1. Running many tests on the same dataset until something looks significant
2. Quietly dropping outliers or failed runs to "clean up" a figure
3. Confusing p-values with biological relevance
4. Publishing only the positive results and burying the rest

This is not just an academic ethics issue. It directly affects how useful your AI models can be. Training on biased, overfitted, or selectively reported experiments will give you confident predictions that are wrong in exactly the ways you care about.

### Fragmented Data Ecosystems

At the same time, data inside a company is often fragmented. Old experiments live in disconnected LIMS instances, spreadsheets, and slide decks. Metadata is incomplete or inconsistent. Turning this into a single, coherent history of what the cells have actually experienced is real work, and it often does not happen.

McIntosh et al. developed the RepeAT framework specifically to assess empirical reproducibility in biomedical research, highlighting how data transparency and accessibility remain significant barriers ([DOI](https://doi.org/10.1186/s12874-017-0377-6)). Their work emphasizes that before sophisticated AI models can be deployed, organizations need robust practices for research transparency, documentation, and data sharing.

So the first step toward a virtual cell lab is not a new neural network. It is a commitment to statistical rigor, full reporting, and serious data engineering.

---

## The Cell Therapy Manufacturing Challenge

For biotech companies developing cell therapies, the challenges are particularly acute. As Wang et al. describe in their comprehensive review in *Cytotherapy*, scalable and quality-driven reproducible manufacturing of therapeutic cells at lower cost remains challenging ([DOI](https://doi.org/10.1016/j.jcyt.2024.03.011)).

The challenges include:

1. **Starting material and patient variability**: Unlike small molecules, cell therapies begin with highly variable starting materials, especially for autologous therapies
2. **Limited understanding of process parameter effects**: The relationships between manufacturing conditions and product quality remain poorly characterized
3. **Complex supply chain logistics**: Living products have unique handling requirements
4. **Lack of predictive quality attributes**: We often do not know which early measurements predict final product performance

These issues manifest as increased production costs, longer production times, greater batch-to-batch variability, and lower overall yield of viable, high-quality cells. The lack of data-driven insights and decision-making is an underlying commonality behind all these problems.

### AI-Driven Quality Prediction

Promising work demonstrates that these challenges can be addressed. Odeh-Couvertier et al. developed an AI-driven experimental-computational platform to identify critical process parameters and quality attributes from multiomics data during T-cell manufacturing ([DOI](https://doi.org/10.1002/btm2.10282)). Using machine learning, they identified feature combinations from early in-culture media assessment that were highly predictive of end-product CD4/CD8 ratio and memory T-cell populations.

Similarly, Strutt et al. demonstrated how machine learning can be coupled with long-read sequencing to rapidly detect microbial contaminants in T-cell cultures, potentially reducing testing time from 7-14 days to hours ([DOI](https://doi.org/10.1128/spectrum.01350-23)).

---

## Biology Is Waiting for Its Simulation-First Moment

Physics, aerospace, and materials science now live in a simulation-first world. Engineers run large simulations, explore design space in silico, and then use physical experiments as the final arbiter. Biology is still mostly in the opposite mode: wet lab first, modeling as an afterthought.

That gap is not because biology is "too complex" to compute. It is because the field has not yet committed to building the right mathematical and computational foundations. Other chaotic and stochastic systems were once called "too complex" as well, until new formalisms made them tractable.

### The Promise of Multi-Scale Modeling

Georgouli et al. provide an excellent overview of the progress and challenges in whole-cell modeling in their review in *Frontiers in Cell and Developmental Biology* ([DOI](https://doi.org/10.3389/fcell.2023.1260507)). They note that whole-cell models are an amalgam of several types of system models, simulated using hybrid methods where appropriate mathematical approaches are used for each biological process.

The authors acknowledge that developing and curating these models is labor-intensive, but they emphasize that the most important contribution has been highlighting the discrepancy between available data and observations needed for parameterization and validation. This is actually valuable information: it tells us where to invest in data generation.

### Multi-Scale Dynamics in Development

Work by Olariu et al. demonstrates what sophisticated multi-scale modeling can achieve ([DOI](https://doi.org/10.1016/j.celrep.2020.108622)). They developed a three-level dynamic model of T-cell commitment based on regulation at the gene regulatory network level, a stochastically controlled chromatin-state gate, and a single-cell proliferation model. The resulting model accurately predicts state-switching kinetics validated by experimental measurements.

Similarly, Kaul et al. developed a minimal gene regulatory network model embedded in cellular agents that respond to dynamic signaling microenvironments ([DOI](https://doi.org/10.1016/j.stemcr.2022.10.004)). Their simulations demonstrated that GRN wiring has significant non-intuitive effects on tissue pattern order, composition, and dynamics.

There is no law of nature that says gene regulation, differentiation, or reprogramming cannot be described with mechanistic, probabilistic models across scales. We simply have not finished that work.

---

## Perturbation Profiling: The Experimental Foundation

Any practical virtual cell lab must be grounded in high-quality perturbation data. The development of Perturb-seq by Dixit et al., combining single-cell RNA-seq with CRISPR-based perturbations to perform pooled genetic screens, represented a breakthrough ([DOI](https://doi.org/10.1016/j.cell.2016.11.038)).

Perturb-seq and its successors allow researchers to:

1. Accurately identify individual gene targets and their effects on transcriptional programs
2. Discover gene signatures and cell states affected by perturbations
3. Map genetic interactions and epistatic relationships

Recent advances have dramatically scaled these approaches. Zheng et al. describe massively parallel in vivo Perturb-seq that can label over 6% of cerebral cells, surpassing previous approaches by orders of magnitude ([DOI](https://doi.org/10.1016/j.cell.2024.04.050)). This scale enables analysis of over 30,000 cells in one experiment and identification of cell-type-specific transcriptional networks.

The availability of standardized, optimized CRISPRi libraries, as described by Replogle et al. in *eLife*, further accelerates the field ([DOI](https://doi.org/10.7554/eLife.81856)). These ultra-compact, highly active sgRNA libraries enable strong and consistent knockdown across mammalian cell models.

---

## What a Practical Virtual Cell Lab Looks Like

Once there is a clean data backbone and a culture that values honest signal over pretty figures, the virtual lab can be built in stages.

### Stage 1: Start from Real Constraints

The first principle is that virtual experiments are only useful if they are anchored in the constraints that scientists and process teams live with every day:

1. **Cell types and products**: What cell types or products matter for your programs?
2. **Feasible interventions**: What interventions are feasible in your platform (gene lists, delivery modalities, small molecules)?
3. **Trusted readouts**: What readouts do you trust (surface markers, transcriptomics, functional assays)?

A virtual cell lab that predicts responses to interventions you cannot actually perform, or outputs you cannot actually measure, is an academic exercise rather than a practical tool.

### Stage 2: Unify and Annotate Historical Data

The data engineering phase requires:

1. **Collecting datasets across projects**: Single-cell and bulk RNA-seq, proteomics, functional assays
2. **Standardizing annotations**: Cell type labels, time points, treatments, QC flags
3. **Tracking provenance**: Which experiments are exploratory versus validated processes

This is where you decide what the models are allowed to learn from. Bad or ambiguous experiments should be tagged as such, not quietly treated as ground truth. The deep learning methods described by Wang et al. for single-cell clustering can help organize heterogeneous data by learning robust representations despite sparsity and noise ([DOI](https://doi.org/10.1093/bfgp/elad017)).

### Stage 3: Build Models That Respect Biology

Instead of a single, generic model, use a set of models that jointly capture different aspects of your system:

1. **State-to-response models**: Map baseline cell state plus intervention to likely shifts in expression or pathway activity
2. **Expression-to-phenotype models**: Link expression shifts to fate scores or quality metrics
3. **Uncertainty quantification models**: Quantify where data is sparse or conflicting

The optimal transport-based approaches described by Su et al. for distribution-agnostic deep learning are particularly promising for recovering missing gene expression values while preserving biological relationships ([DOI](https://doi.org/10.1002/advs.202307280)).

The details can vary, but the principle is simple: the model should never forget that it is modeling a real cell, with regulators, networks, and noise, not random vectors.

### Stage 4: Run Virtual Screens Before Real Ones

Once you have a reasonable model stack, you can:

1. Enumerate hundreds or thousands of candidate perturbations and protocol variants
2. Simulate their effects on your baseline cells
3. Rank interventions by how strongly and safely they appear to move toward your target state

This does not replace real experiments. It reshapes them. Instead of spraying resources across a huge search space, you focus on the subset that looks most promising under both biological knowledge and mathematical modeling.

### Stage 5: Close the Loop

The most important step is closing the loop between virtual predictions and real experiments:

1. Run a focused experimental campaign based on virtual priorities
2. Feed the full results back into the models, not only the "good looking" outcomes
3. Repeat

Over time, your virtual lab becomes a living reflection of your actual lab and your programs. It gets better at predicting what will work for your specific cells, donors, and manufacturing realities.

---

## Why This Matters for Biotech Companies and CDMOs

### For Biotech Companies

A virtual cell lab can shorten the time between an idea and a real signal. It helps teams avoid dead ends earlier and gives a more honest view of which ideas are scientifically promising rather than just fashionable.

The key benefits include:

1. **Accelerated target identification**: Virtual screens can prioritize perturbations before expensive experiments
2. **Reduced attrition**: Predictions can flag interventions likely to fail due to toxicity or off-target effects
3. **Better understanding of mechanism**: Models that incorporate biological knowledge can suggest why certain interventions work

### For CDMOs

For a CDMO, a virtual cell lab can become part of the service itself. Instead of only offering capacity, you can offer guidance on which design spaces are worth exploring for a client, backed by simulations grounded in real data and rigorous statistics.

The value proposition includes:

1. **Process optimization**: Models can predict how changes in media, timing, or other parameters will affect product quality
2. **Client guidance**: Simulations can help clients understand the feasibility and likely outcomes of their programs before committing resources
3. **Quality prediction**: AI-driven approaches can predict end-product quality from early process measurements

---

## The Path Forward

The long-term vision is not science fiction. Other fields already live in a world where simulations guide most design decisions and experiments are used to confirm, not guess. Biology can get there, but not by treating genomes as text or treating AI as a marketing checkbox.

It will get there when:

1. **We treat biological data with the same respect we give to statistical integrity in other high-stakes fields.** This means honest reporting, appropriate uncertainty quantification, and resistance to publication bias.

2. **We invest in mathematical and mechanistic models that reflect how cells actually work.** Gene regulatory networks, signaling cascades, metabolic constraints, and stochastic dynamics all matter.

3. **We build virtual cell labs that sit inside real workflows and help decide which experiments to run next.** The value is not in a perfect oracle but in a system that learns from every experiment and gets progressively better.

That is the practical version of the vision. Not an oracle, but a system that lets AI test what you cannot, so that the experiments you do run are more likely to move biology, and your business, in the right direction.

---

## References

Key references include:

1. Bunne C, et al. "How to build the virtual cell with artificial intelligence: Priorities and opportunities." *Cell* (2024). [DOI](https://doi.org/10.1016/j.cell.2024.11.015)

2. Cui H, et al. "scGPT: toward building a foundation model for single-cell multi-omics using generative AI." *Nature Methods* (2024). [DOI](https://doi.org/10.1038/s41592-024-02201-0)

3. Roohani Y, et al. "Predicting transcriptional outcomes of novel multigene perturbations with GEARS." *Nature Biotechnology* (2023). [DOI](https://doi.org/10.1038/s41587-023-01905-6)

4. Yang Y, et al. "Gene knockout inference with variational graph autoencoder learning single-cell gene regulatory networks." *Nucleic Acids Research* (2023). [DOI](https://doi.org/10.1093/nar/gkad450)

5. Rizvi SA, et al. "Scaling Large Language Models for Next-Generation Single-Cell Analysis." *bioRxiv* (2025). [DOI](https://doi.org/10.1101/2025.04.14.648850)

6. Pershad Y, et al. "Closing the loop: Teaching single-cell foundation models to learn from perturbations." *bioRxiv* (2025). [DOI](https://doi.org/10.1101/2025.07.08.663754)

7. Gulati GS, et al. "Profiling cell identity and tissue architecture with single-cell and spatial transcriptomics." *Nature Reviews Molecular Cell Biology* (2024). [DOI](https://doi.org/10.1038/s41580-024-00768-2)

8. Wang B, et al. "Interfacing data science with cell therapy manufacturing: where we are and where we need to be." *Cytotherapy* (2024). [DOI](https://doi.org/10.1016/j.jcyt.2024.03.011)

9. Odeh-Couvertier VY, et al. "Predicting T-cell quality during manufacturing through an artificial intelligence-based integrative multiomics analytical platform." *Bioengineering & Translational Medicine* (2022). [DOI](https://doi.org/10.1002/btm2.10282)

10. Dixit A, et al. "Perturb-Seq: Dissecting Molecular Circuits with Scalable Single-Cell RNA Profiling of Pooled Genetic Screens." *Cell* (2016). [DOI](https://doi.org/10.1016/j.cell.2016.11.038)

11. Macleod M, Mohan S. "Reproducibility and Rigor in Animal-Based Research." *ILAR Journal* (2019). [DOI](https://doi.org/10.1093/ilar/ilz015)

12. McIntosh LD, et al. "Repeat: a framework to assess empirical reproducibility in biomedical research." *BMC Medical Research Methodology* (2017). [DOI](https://doi.org/10.1186/s12874-017-0377-6)

13. Georgouli K, et al. "Multi-scale models of whole cells: progress and challenges." *Frontiers in Cell and Developmental Biology* (2023). [DOI](https://doi.org/10.3389/fcell.2023.1260507)

14. Olariu V, et al. "Multi-scale Dynamical Modeling of T Cell Development from an Early Thymic Progenitor State to Lineage Commitment." *Cell Reports* (2021). [DOI](https://doi.org/10.1016/j.celrep.2020.108622)

15. Kaul H, et al. "Virtual cells in a virtual microenvironment recapitulate early development-like patterns in human pluripotent stem cell colonies." *Stem Cell Reports* (2022). [DOI](https://doi.org/10.1016/j.stemcr.2022.10.004)

16. Wang J, et al. "Single-cell RNA-seq data clustering by deep information fusion." *Briefings in Functional Genomics* (2024). [DOI](https://doi.org/10.1093/bfgp/elad017)

17. Su Y, et al. "Distribution-Agnostic Deep Learning Enables Accurate Single-Cell Data Recovery and Transcriptional Regulation Interpretation." *Advanced Science* (2024). [DOI](https://doi.org/10.1002/advs.202307280)

18. Zheng X, et al. "Massively parallel in vivo Perturb-seq reveals cell-type-specific transcriptional networks in cortical development." *Cell* (2024). [DOI](https://doi.org/10.1016/j.cell.2024.04.050)

19. Replogle JM, et al. "Maximizing CRISPRi efficacy and accessibility with dual-sgRNA libraries and optimal effectors." *eLife* (2022). [DOI](https://doi.org/10.7554/eLife.81856)

20. Strutt JPB, et al. "Machine learning-based detection of adventitious microbes in T-cell therapy cultures using long-read sequencing." *Microbiology Spectrum* (2023). [DOI](https://doi.org/10.1128/spectrum.01350-23)
