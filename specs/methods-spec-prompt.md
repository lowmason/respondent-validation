ROLE AND GOAL
I am a senior statistician (Bayesian hierarchical/mixture models, IRT, ML; Python, JAX/NumPyro, Stan) building an open-source Python package, survey-response-validation, that scores individual survey responses by the probability that they are invalid. Produce a state-of-the-art, technically precise review of methods for detecting and scoring invalid responses in online surveys, with emphasis on work from 2023 to the present. I want methods, evidence quality, and implementation guidance, not an introduction to the problem.

Target application (use it to prioritize, not to narrow the review): incentivized self-report surveys of adolescents aged 14–17, including a gender-minority oversample, recruited through open social-media ads rather than panels, on Qualtrics or similar. Prior waves saw large fraud increases despite reCAPTCHA, honeypots, IP screening, and screener/survey cross-checks.

SCOPE
1. Threat taxonomy, kept separate because detection differs by type: scripted bots and form-fillers; human survey/click farms using VPN/VPS; eligibility fraud (adults posing as minors, cisgender respondents posing as gender-minority for incentives); duplicates and ballot-box stuffing; mischievous responders (Robinson-Cimpian 2014; Cimpian et al.); careless/insufficient-effort responding; autonomous LLM "synthetic respondents" (Westwood 2025, PNAS); and LLM-assisted humans pasting generated text. Report evidence on the relative prevalence of each type by recruitment channel (e.g., Jaffe et al. 2026 argue most bad data comes from human fraud, not bots).

2. Signal families and what each can and cannot detect:
   a. Paradata and telemetry: page timing, keystroke and mouse dynamics, paste events, focus/blur, automation and headless-browser signals (navigator.webdriver, CDP detection), device attestation (Turnstile, reCAPTCHA Enterprise scores, Private Access Tokens), and their accessibility/privacy costs for minors.
   b. Network and device: IP intelligence and VPN/proxy/datacenter classification (MaxMind, IPQualityScore, Scamalytics), time-zone/IP/self-report consistency, browser fingerprinting (FingerprintJS). Report any independent accuracy evaluations of these vendors.
   c. Identity: email/phone pattern heuristics, disposable domains, re-contact and verification-call protocols.
   d. Content, closed-ended: long-string, IRV, even–odd consistency, psychometric synonyms/antonyms, Mahalanobis D, IRT person-fit (lz, lz*), logically inconsistent item pairs, low-base-rate "mischievous" items.
   e. Content, open-ended: exact and near-duplicate detection across respondents (MinHash/SimHash, embeddings), stylometric homogeneity across respondents, LLM-generated-text detectors (Binoculars, Fast-DetectGPT, commercial detectors) and the evidence on their fragility (e.g., the RAID benchmark; persona-prompted agents pushing detectors toward chance).
   f. Cross-respondent structure: respondent–identifier graphs, community detection, submission-time burst detection, distributional anomalies (e.g., LLM agents producing systematically biased rather than noisy answers).

3. Modeling and fusion approaches, with model specification, estimands, supervision required, and identifiability conditions where relevant:
   - Rule-count / multiple-hurdle ensembles and their unknown error rates.
   - Supervised classifiers (gradient boosting, neural nets) trained on adjudicated or instructed labels (e.g., Schroeders, Schmidt & Gnambs 2022).
   - Positive-unlabeled and semi-supervised learning, since known fraud is usually available but the remainder is unlabeled.
   - Unsupervised anomaly detection (isolation forest, LOF, autoencoders).
   - Latent class / finite mixture models treating each check as an imperfect diagnostic test without a gold standard: Hui–Walter, Dendukuri–Joseph conditional dependence, random-effects and probit LCMs, LCMs with prevalence covariates, semi-supervised LCMs with anchor cases; identifiability, label switching, and prior specification. Report any application of these to survey fraud.
   - Response-time latent response mixture and mixture-IRT models for careless responding (Ulitzsch and colleagues 2022–2024), and changepoint models for the onset of carelessness (Welz & Alfons).
   - Graph-based methods for farms and duplicates.
   - Sequential/online monitoring during fielding (CUSUM, Bayesian changepoint, drift detection).
   - Calibration and decision theory: threshold selection under asymmetric costs, conformal prediction for flag sets with guaranteed error rates, three-band triage (auto-accept / review / reject).
   - Equity: methods for estimating and constraining group-specific false-positive rates (equalized-odds-style constraints, group-specific test parameters), and evidence that IP/VPN rules disproportionately flag legitimate members of privacy-sensitive groups.
   - Carrying classification uncertainty into substantive analysis: probability weighting, multiple imputation of class membership, bounds.

4. Evaluation: designs that produce ground truth (seeded scripted bots, seeded LLM persona agents, human red-team eligibility fraud, instructed careless responding, blinded two-coder adjudication); metrics (AUROC, PR-AUC, calibration, subgroup FPR); adversarial adaptation and drift; and any public labeled datasets or simulators. Report sensitivity/specificity wherever a study measured them rather than removal rates.

5. Software and tooling: R packages (careless, carelessonset, PerFit, mirt, poLCA, randomLCA), Python equivalents or gaps, Stan/PyMC/NumPyro implementations of the latent class models, and platform features (Qualtrics bot detection and duplicate flags, noting that RelevantID was retired July 2025; CloudResearch Sentry; Prolific authenticity checks) with a clear statement of which apply to open social-media links rather than panels. Distinguish peer-reviewed evaluations from vendor white papers.

6. Adjacent fields whose methods transfer: click/ad fraud, sybil and fake-account detection, social-media bot detection, behavioral biometrics, diagnostic-test-accuracy meta-analysis without a gold standard, PU learning, and fairness-aware classification.

ANCHOR REFERENCES (start here, then go well beyond them): Westwood 2025 PNAS; Jaffe et al. 2026 Perspect Psychol Sci; Pinzón et al. 2024 Front Res Metr Anal; Comachio et al. 2025 BMJ EBM; Guy et al. 2024 Psychol Methods; Ward & Meade 2023 Annu Rev Psychol; Ulitzsch et al. 2022 Psychometrika; Schroeders et al. 2022 EPM; Veselovsky et al. 2023; Lebrun et al. 2024; Affonso 2026 J Consum Res; Gordon et al. 2026 preprint; Kennedy et al. 2020 PSRM; Dennis, Goodson & Pearson 2020; Griffin et al. 2022; Pozzar et al. 2020.

OUTPUT
- A comparison table of methods: signal inputs, supervision needed, reported performance and on what ground truth, robustness to LLM agents and human farms, implementation availability, maturity (peer-reviewed / preprint / vendor claim).
- For the five most promising approaches, model specification with estimands and identifiability requirements, and a sketch of how each would be implemented in Python (NumPyro or Stan for Bayesian models).
- A recommended reference pipeline and evaluation protocol for a ~400-respondent pilot, and a separate list of open problems where the literature is thin.
- Full reference list with DOIs or URLs. Verify every citation; flag anything you cannot verify rather than dropping it silently. Prioritize 2024–2026 sources and state publication status. No padding, no restatement of basics.