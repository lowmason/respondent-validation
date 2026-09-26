# respondent-validation — References (APA 7, annotated and verified)

Companion to [`respondent-validation.md`](respondent-validation.md). Built 2026-09-26 from the spec's own citations. It seeds, but is not, the Req 20 methods note (`docs/respondent-validation-review.md`), whose reference list must also carry evidence labels per claim.

## How this list was built

**Inclusion rule — the spec is the filter.** A source is listed when the spec (1) cites it by author; (2) names a method eponymously, so the name is the citation (Hui–Walter, Qu–Tan–Kutner, Dendukuri–Joseph, Elkan–Noto, Bekker & Davis, Begg & Greenes, Bolck–Croon–Hagenaars, Adams & MacKay, Bates et al., Snijders, Rubin's rules, Wilson intervals); (3) relies on it as a dataset, benchmark, detector or reference implementation (RAID, ASURRE, Affonso's trap repository, Gordon et al.'s OSF data, Binoculars, Fast-DetectGPT, the R packages `careless`, `PerFit`, `randomLCA`, `carelessonset`); or (4) names it as a vendor or platform source — those sit in their own section because Req 20 bars vendor figures as performance evidence. Where the spec's short name was ambiguous, the resolution follows the review locator the spec cites, and the entry says which work was chosen. What was left out, and why, is at the end.

**Verification.** Every bibliographic field was taken from a record fetched on 2026-09-26 — Crossref (by DOI, or by title search when no DOI was known), the PubMed E-utilities and PMC ID converter, the arXiv API, and DataCite for arXiv DOIs — or, where no registry record exists (proceedings without DOIs, software, vendor pages, preprint claims), from the publisher's, repository's or vendor's own page found by web search. Nothing was filled in from memory. Status labels:

-   **Verified** — every APA field matches a fetched record.
-   **Corrected** — a review's citation was wrong; the corrected form is given and the error named.
-   **Partial** — the work exists and core fields match; the field that could not be confirmed is named.
-   **Unverified** — could not be confirmed.

Claims were checked in two cases: every conflict Req 20 names, and figures a requirement rests on that could be read directly in the full text. A figure in an annotation counts as checked only when that entry's **Verification** line says so. The rest are carried over from the reviews and remain Req 20's job. Two of the spec's own computations were also re-derived: the Wilson 95% intervals in Req 17 (36/40 → 0.770–0.960; 3/60 → 0.017–0.137), and the conformal resolutions in Req 14 (1/61 ≈ 0.016 for a 60-respondent group; α_R = 0.01 needs n_g ≥ 99). Both match.

## Findings that bear on the spec itself

-   **Req 3 / Verification — no R golden file for a polytomous *l_z*\*.** PerFit 1.4.7's `lzstar` is dichotomous-only, and its `lzpoly` extends the original *l_z*. The parity bullet should either target `lzpoly` or name another reference for a polytomous *l_z*\*. See Tendeiro et al. (2016).
-   **Req 17 — ASURRE is not yet public.** The repository its paper names is empty (Wang et al., 2026). RAID, Affonso's trap repository, and Gordon et al.'s OSF data are public.
-   **Reqs 1–2 — the documented Qualtrics fields don't match the contract's categories.** Qualtrics documents `Q_RecaptchaStatus` as "complete" or "error", plus a separate `Q_RecaptchaError`, not the contract's four categories. MaxMind's risk score runs from 0.01 to 99, not \[0, 1\]. Both need explicit adapter mappings. This informs the Req 1 (open) item but does not discharge it.
-   **Req 21 — recall challenges are not verification.** In Pinzón et al. (2024), 82% of confirmed-fraudulent repliers correctly recalled at least one earlier answer. That supports the spec's choice of staff identity verification.
-   **Req 20 — "Zhang et al. 2026" is registered as Chen et al. (2026).** The platform figures hold, secondhand, but the "Mindworks baseline" detail could not be confirmed.
-   **Motivation holds.** The Pinzón et al. wording the spec inherits from the ChatGPT review ("false-positive rates the authors called unacceptable") is supported: the paper reports "persistent unacceptable error rates exceeding 5%", where error means valid responses flagged.

## Req 20 conflicts — resolved on the record

| Conflict named in Req 20 | What the reviews said | What the record shows | Resolution |
|------------------|------------------|------------------|------------------|
| Affonso (2026) citation | Gemini: "Survey Sabotage: Insights into Reducing the Risk of Fraudulent Responses in Online Surveys", `10.1093/jcr/ucaf051`. Claude: "Brief Commentary: A Framework for Detecting AI Agents in Online Research", 53(3):619–633, `10.1093/jcr/ucag006`. | `ucag006` is the agent-detection paper: *Journal of Consumer Research* 53(3), 619–633, online 2026-03-25. `ucaf051` is Affonso et al. (2025), "Concealing Prices", a pricing study. The "Survey sabotage…" title belongs to Bonnamy et al. (2025), *Anatomical Sciences Education*. | **Claude correct.** Gemini fused a real title from one paper with a DOI from another; neither is about AI agents. |
| "Careless Responding: Why Many Findings Are Spurious or Spuriously Inflated" | Gemini: "Guy, M. D., et al. (2024)", *AMPPS* 7(1). | The authors are Stosic, Murphy, Duong, Fultz, Harvey & Bernieri (2024), `10.1177/25152459241231581`. Guy et al. (2024) is the *Psychological Methods* multimodal bot-screening paper, `10.1037/met0000696`. | **Gemini wrong.** Both papers are listed below, so the correction is on the record. |
| RelevantID retirement date | Prompt: "retired July 2025". Claude: June 30, 2025, after June 15 was announced first. ChatGPT: "mid-2025". | Qualtrics' own page says "we're deprecating RelevantID on June 30, 2025", both live and in an August 2025 archived copy. Institutional notices carried June 15 first (Memorial University, 12 June; Boise State, 16 June), then June 30 (Ohio State, 18 June). Duke's notice of 7 July gives July 1, 2025 as its migration date. | **Cite June 30, 2025 (Qualtrics).** "July" traces to institutional migration notices, not to Qualtrics. The replacement flag `Q_DuplicateRespondent` is set only after submission, so it cannot drive branch logic. |
| Westwood figures and title | Claude: 99.8% of 6,000 attention-check trials. Gemini titles the DOI "The entities enabling scientific fraud at scale are large, resilient, and growing rapidly". | The full text confirms "a 99.8% pass rate on 6,000 trials", with 10 errors in 20 × 300 trials. The title is "The potential existential threat of large language models to online survey research". Gemini's title is Richardson et al. (2025), *PNAS* 122(32), a paper-mill study. | **Figures hold; Gemini's title is wrong.** |
| MacKinnon et al. (2025) retention | Claude: 957 of 1,377 kept (69.5%). | The indexed abstract confirms 957 of 1,377 (69.5%) "deemed eligible and included". Crossref confirms MacKinnon as first author. | **Holds.** |
| Zhang et al. (2026) platform failure rates | Claude: known only via the *APS Observer*: verified humans ≈2% against a "Mindworks baseline", Prolific 6%, CloudResearch 10%, MTurk 40%. | The preprint is public: Chen, Urminsky, Zhang, et al. (2026), PsyArXiv. Grace Zhang leads the project but is third in the registered byline. The *Observer* (Burrell, 2026) confirms the four figures; the preprint abstract gives 6–41% across platforms against a 2.4% in-person human false-positive rate. "Mindworks" appears in neither accessible source. | **Figures hold, secondhand.** Cite the preprint under its registered byline. The Mindworks detail is unverified. All figures are panel-based. |
| Pinzón et al. (2024), "559 of 560" | ChatGPT: "560 replies, 559 were fraudulent", and its pipeline says the re-contact "caught 99% of fraud". | The paper reports "560 responses, of which all but one were fraudulent". However, 82% (n = 442) of those fraudulent repliers correctly confirmed at least one earlier answer; fraud was identified from greeting, sign-off, structure and duplication. | **Numbers hold; the reading is wrong.** The recall challenge did not detect fraud, because fraud rings keep records. Recall questions are not a verification method (bears on Req 21). |
| Vendor detection rates (CloudResearch Sentry, Verisoul, Prolific) | Quoted in the reviews as detection performance. | Every figure exists, but only on vendor pages, and two are on pages other than those the reviews cite. Prolific's January 2026 audit (0.8% flagged) is in its methodological justification pack. CloudResearch's "\>99% … false positive rate below 1%" is in a December 2025 post about its Engage platform. Prolific's own pack pairs the LLM check's 98.7% precision with 78.9% recall. | **Labeled vendor; never performance evidence** (Req 20). The exact pages are listed in the vendor section below. |

## Index — requirement → references

| Spec location | References |
|------------------------------------|------------------------------------|
| Motivation (the gaps) | Pratt-Chapman et al. (2021); Pinzón et al. (2024); Oyler et al. (2026); Westwood (2025); Affonso (2026); Dugan et al. (2024); Mi et al. (2019); Gómez-Boix et al. (2018); Hui & Walter (1980); Dendukuri & Joseph (2001); MacDonald et al. (2025); Kennedy et al. (2020); Lambert & Luisi (2026); Delgado-Ron et al. (2024); `careless`, `PerFit`, `randomLCA`, `carelessonset` |
| Reqs 1–2 — input contract, Qualtrics adapter, telemetry | Qualtrics fraud-detection documentation; RelevantID retirement notices; Google reCAPTCHA v3; Gordon et al. (2026); Westwood (2025) |
| Req 3 — careless indices and person fit | Snijders (2001); Westwood (2025); `careless`; `PerFit` (Tendeiro et al., 2016) |
| Req 4 — content checks and cognitive traps | Robinson-Cimpian (2014); Cimpian et al. (2018); Affonso (2026); Westwood (2025); Phillips et al. (2020); Delgado-Ron et al. (2024) |
| Req 5 — telemetry indicators | Pinzón et al. (2024); Veselovsky et al. (2023); Westwood (2025) |
| Req 6 — network, device, identity | Pinzón et al. (2024); MaxMind minFraud; Pozzar et al. (2020); Mi et al. (2019); Kennedy et al. (2020); Gómez-Boix et al. (2018); MacDonald et al. (2025) |
| Req 7 — open-ended text | Zhang, Xu & Alvero (2025); Hans et al. (2024); Bao et al. (2024); Dugan et al. (2024); Wang et al. (2026) |
| Req 9 — response-time mixture | Ulitzsch et al. (2022); Ulitzsch, Shin & Lüdtke (2024); Welz & Alfons (2023); `carelessonset`; Robinson-Cimpian (2014) |
| Req 10 — fusion LCM | Qu, Tan & Kutner (1996); Dendukuri & Joseph (2001) |
| Req 11 — identifiability and priors | Hui & Walter (1980); Cerullo et al. (2022); Pozzar et al. (2020); Griffin et al. (2022); Pinzón et al. (2024); Dendukuri & Joseph (2001) |
| Req 12 — cohort agent diagnostics | Westwood (2025) |
| Req 13 — positive-unlabeled analysis | Elkan & Noto (2008); Bekker & Davis (2018, 2020); Schroeders et al. (2022); Alfons & Welz (2024) |
| Req 14 — conformal triage | Bates et al. (2023) |
| Req 15 — uncertainty propagation | Rubin (1987); Cimpian, Timmer & Kim (2023); Bolck et al. (2004) |
| Req 16 — online monitoring | Adams & MacKay (2007); Pozzar et al. (2020) |
| Req 17 — ground truth and metrics | Westwood (2025); Begg & Greenes (1983); Wilson (1927); Lambert & Luisi (2026); Dugan et al. (2024); Wang et al. (2026); Affonso (2026); Gordon et al. (2026) |
| Req 18 — adversarial drift | Affonso (2026) |
| Req 19 — R parity | `careless`; `PerFit` |
| Req 20 — named conflicts | Affonso (2026); Stosic et al. (2024); Guy et al. (2024); Westwood (2025); MacKinnon et al. (2025); Chen et al. (2026) — the spec's "Zhang et al. 2026" — and Burrell (2026); Pinzón et al. (2024); vendor sources |
| Req 21 — design-layer interface | Lambert & Luisi (2026) |

## Annotated references (A–Z)

Adams, R. P., & MacKay, D. J. C. (2007). *Bayesian online changepoint detection* \[Preprint\]. arXiv. https://doi.org/10.48550/arXiv.0710.3742

-   **Applicability:** Req 16 — the recorded upgrade to the hourly CUSUM monitor on posterior-mean prevalence.
-   **Verification:** Verified — arXiv API and DataCite.

Affonso, F. M. (2026). Brief commentary: A framework for detecting AI agents in online research. *Journal of Consumer Research*, *53*(3), 619–633. https://doi.org/10.1093/jcr/ucag006

-   **Applicability:** The evidence base for the cognitive-trap family (Req 4(iv)) and for demoting attention checks to indicators (Req 4(v), Motivation). Its multi-model trap test motivates the per-wave trap refresh (Req 18), and its trap repository is a calibration resource for the trap indicators only (Req 17). Its citation is a named Req 20 conflict.

-   **Verification:** Verified — Crossref (online 2026-03-25; October 2026 issue). Figures confirmed by web search against the OUP full text:

    -   cognitive traps detected 97.1% of 526 deployed agents, versus 2.3% for traditional attention checks;
    -   4.1% of 1,007 Prolific humans were flagged;
    -   two traps reached 93.7% detection at 2.6% false positives, for about one minute of survey time;
    -   the six retained traps were tested against 34 vision-language models in 2,040 trials.

    The trap repository is https://github.com/FelipeMAffonso/cognitive-trap-repository. Its README reports cumulative totals larger than the paper's, so cite the paper for figures. Data and code are at https://osf.io/f2jhx. **Corrected** in the Gemini review — see the conflicts table.

Alfons, A., & Welz, M. (2024). Open science perspectives on machine learning for the identification of careless responding: A new hope or phantom menace? *Social and Personality Psychology Compass*, *18*(2), Article e12941. https://doi.org/10.1111/spc3.12941

-   **Applicability:** Req 13 — the source of the reading that the best published careless-responding classifier (Schroeders et al., 2022) reached at most 19% precision against instructed labels; the reason uncorrected supervised boosting is rejected as a primary score.
-   **Verification:** Verified — Crossref (title search; a 2022 PsyArXiv preprint also exists).

Bao, G., Zhao, Y., Teng, Z., Yang, L., & Zhang, Y. (2024). Fast-DetectGPT: Efficient zero-shot detection of machine-generated text via conditional probability curvature. In *International Conference on Learning Representations (ICLR 2024)*. https://openreview.net/forum?id=Bpcgcr8E8Z

-   **Applicability:** Req 7(c) — one of the two open-source AI-text detectors the spec sanctions, alongside Binoculars. Either one supplies a single **weak** indicator, never a hurdle. It stays disabled until its false-positive rate on verified-valid adolescent open-ends is measured (the Req 7 (open) item).
-   **Verification:** Partial. Authors and title come from the arXiv API and DataCite (https://doi.org/10.48550/arXiv.2310.05130). The ICLR 2024 venue is confirmed by the ICLR 2024 virtual-site listing and the authors' repository. The OpenReview page blocked automated access, so it was not fetched.

Bates, S., Candès, E., Lei, L., Romano, Y., & Sesia, M. (2023). Testing for outliers with conformal *p*-values. *The Annals of Statistics*, *51*(1), 149–178. https://doi.org/10.1214/22-AOS2244

-   **Applicability:** Req 14 — conformal Benjamini–Hochberg over the conformal *p*-values, controlling the false discovery rate among rejections, is the recorded alternative to per-group false-rejection control.
-   **Verification:** Verified — Crossref; page range from the arXiv journal reference (arXiv:2104.08279).

Begg, C. B., & Greenes, R. A. (1983). Assessment of diagnostic tests when disease verification is subject to selection bias. *Biometrics*, *39*(1), 207–215. https://doi.org/10.2307/2530820

-   **Applicability:** Req 17(b) — sensitivity and specificity on the stratified verification subset are estimated under inverse-probability weights for verification bias.
-   **Verification:** Verified — Crossref (DOI) and PubMed 6871349 (page range).

Bekker, J., & Davis, J. (2018). Learning from positive and unlabeled data under the selected at random assumption. *Proceedings of Machine Learning Research*, *94*, 8–22. https://proceedings.mlr.press/v94/bekker18a.html

-   **Applicability:** Req 13 — "Bekker & Davis" for the SAR variant. The labeling propensity *e*(*x*) is modeled as a function of detection pathway, because the fraud already caught is the easy fraud. Resolved to this paper because it states its contribution as formulating the SAR assumption, with the propensity score *e*(*x*) = Pr(*s* = 1 \| *y* = 1, *x*). The companion conference paper, Bekker, J., Robberechts, P., & Davis, J. (2020), "Beyond the selected completely at random assumption for learning from positive and unlabeled data", ECML PKDD 2019 proceedings (pp. 71–85, Springer, https://doi.org/10.1007/978-3-030-46147-8_5), develops the same setting. Neither cites the other.
-   **Verification:** Verified — PMLR volume page and the paper itself (LIDTA 2018 workshop, published 2018-11-05). The companion paper was verified via Crossref; its LNCS volume number is not given in the record.

Bekker, J., & Davis, J. (2020). Learning from positive and unlabeled data: A survey. *Machine Learning*, *109*(4), 719–760. https://doi.org/10.1007/s10994-020-05877-5

-   **Applicability:** Req 13 — the survey both the Claude and Gemini reviews cite for "Bekker & Davis"; covers the SCAR and SAR settings the positive-unlabeled sensitivity analysis moves between.
-   **Verification:** Verified — Crossref.

Bolck, A., Croon, M., & Hagenaars, J. (2004). Estimating latent structure models with categorical variables: One-step versus three-step estimators. *Political Analysis*, *12*(1), 3–27. https://doi.org/10.1093/pan/mph001

-   **Applicability:** Req 15 — (rejected for v1) the Bolck–Croon–Hagenaars three-step approach, recorded as the alternative for structural-equation users; multiple imputation of class membership is chosen instead.
-   **Verification:** Verified — Crossref.

Burrell, T. (2026, March 27). The biggest threat to online data collection is humans, not bots. *APS Observer*. https://www.psychologicalscience.org/publications/observer/humans-not-bots.html

-   **Applicability:** Req 20 — the secondhand source through which the Claude review knew the "Zhang et al. 2026" platform failure rates. Cite the preprint (Chen et al., 2026) for the figures; this entry records provenance only.
-   **Verification:** Verified — page fetched; title, author, date and the four failure rates as shown. The article does not mention "Mindworks".

Cerullo, E., Jones, H. E., Carter, O., Quinn, T. J., Cooper, N. J., & Sutton, A. J. (2022). Meta-analysis of dichotomous and ordinal tests with an imperfect gold standard. *Research Synthesis Methods*, *13*(5), 595–611. https://doi.org/10.1002/jrsm.1567

-   **Applicability:** Req 11 — "Cerullo et al.": diffuse priors in Stan latent class models produce near-non-identifiability diagnostics, so informative priors on some sensitivities and specificities are mandatory when anchors are absent. Resolved to this paper because the Claude review's locator for the claim is arXiv:2103.06858, whose published version this is — note the published title says "with an imperfect gold standard", the preprint "without a gold standard".
-   **Verification:** Verified — Crossref. The authors (and their order) match arXiv:2103.06858 (v4, 2022). The claim was confirmed in the preprint text, but not re-checked against the typeset version: "Attempts to conduct sensitivity analysis using more diffuse priors led to diagnostic errors", and Stan "is quite sensitive at detecting non-identifiabilities". The later simulation study the Claude review treats as the fusion model's template (§9) reports substantial divergences under a "vague" default prior: Cerullo, E., Pinkney, S., Sutton, A. J., Lucas, T., Cooper, N. J., & Jones, H. E. (2025). *Latent class multivariate probit and latent trait models for evaluating test accuracy without a gold standard: A simulation study* \[Preprint\]. arXiv. https://doi.org/10.48550/arXiv.2509.18489 — unpublished as of 2026-09-26.

Chen, S., Urminsky, O., Zhang, G., Walatka, R., Fernandez, K., Low, A., Bogard, J., & Fox, C. R. (2026). *Estimating the threat of AI-agent responding across online survey platforms* \[Preprint\]. PsyArXiv. https://doi.org/10.31234/osf.io/xcg26_v1

-   **Applicability:** Req 20 — this is the spec's "Zhang et al. 2026". Grace Zhang leads the project; she is third in the registered byline, though her materials repository lists her first. It reports AI-detection failure rates of 6–41% across online platforms against a 2.4% in-person human false-positive rate (the *APS Observer*'s 2%/6%/10%/40% for humans/Prolific/CloudResearch/MTurk). These are platform figures, so they do not measure agent prevalence on open social-media links (Motivation).
-   **Verification:** Verified — Crossref and OSF (posted 2026-03-02). The platform breakdown is confirmed in Burrell (2026). The "Mindworks baseline" detail could not be confirmed.

Cimpian, J. R., Timmer, J. D., Birkett, M. A., Marro, R. L., Turner, B. C., & Phillips, G. L. (2018). Bias from potentially mischievous responders on large-scale estimates of lesbian, gay, bisexual, or questioning (LGBQ)–heterosexual youth health disparities. *American Journal of Public Health*, *108*(S4), S258–S265. https://doi.org/10.2105/AJPH.2018.304407

-   **Applicability:** Req 4(iii) — "Cimpian et al.", resolved to the Youth Risk Behavior Survey analysis the Claude review cites (§1): LGBQ–heterosexual disparities shrink once potentially mischievous responders are removed, which is why low-base-rate items are registered, and why they matter for the gender-minority disparity estimates this study reports.
-   **Verification:** Verified — Crossref (title search).

Cimpian, J. R., Timmer, J. D., & Kim, T. H. (2023). Mitigating invalid and mischievous survey responses: A registered report examining risk disparities between heterosexual and lesbian, gay, bisexual, or questioning youth. *Child Development*, *94*(5), 1136–1161. https://doi.org/10.1111/cdev.13957

-   **Applicability:** Req 15 — the reweighting whose continuous analogue (weights 1 − *p_i*, bootstrapped over the LCM fit) ships as a sensitivity analysis beside multiple imputation. Req 4(a) — the method Delgado-Ron et al. (2024) applied, whose weights penalized multiply-marginalized youth.
-   **Verification:** Verified — Crossref (title search). This supplies the venue the Claude review left unverified.

Delgado-Ron, J. A., Jeyabalan, T., Watt, S., & Salway, T. (2024). Mitigating invalid data bias in the estimation of sexual orientation disparities in a survey of youth in US and Canada. *Child Development*, *95*(5), e373–e376. https://doi.org/10.1111/cdev.14111

-   **Applicability:** Req 4(a) and Motivation — Cimpian-style reweighting gave a mean weight of 1.01 to youth selecting one racial identity versus 0.40 to those selecting four or more. This is the reason low-base-rate counts never become a hurdle or a weight, and each instead enters the fusion model with a group-specific false-positive rate.
-   **Verification:** Verified — Crossref. The figures were confirmed in the article's results by web search: "1.01 for one, 0.93 for two, 0.79 for three, and 0.40 for 4+". The article applies the Cimpian, Timmer & Kim (2023) methods to the UnACoRN sample of about 9,674 SGM youth.

Dendukuri, N., & Joseph, L. (2001). Bayesian approaches to modeling the conditional dependence between multiple diagnostic tests. *Biometrics*, *57*(1), 158–167. https://doi.org/10.1111/j.0006-341X.2001.00158.x

-   **Applicability:** Req 10 (chosen) — half of the random-effect conditional-dependence device (shared *u_i* with class-specific loadings). Req 11 — its fixed-effects pairwise-covariance form is the recorded alternative for K ≤ 4 and is rejected as the primary. Motivation — no review found an application to survey-fraud checks.
-   **Verification:** Verified — Crossref.

Dugan, L., Hwang, A., Trhlík, F., Zhu, A., Ludan, J. M., Xu, H., Ippolito, D., & Callison-Burch, C. (2024). RAID: A shared benchmark for robust evaluation of machine-generated text detectors. In *Proceedings of the 62nd Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers)* (pp. 12463–12492). Association for Computational Linguistics. https://doi.org/10.18653/v1/2024.acl-long.674

-   **Applicability:** Motivation and Req 7(c) — detectors are easily fooled and few operate below 1% false positives, so the AI-text detector is one weak indicator, never a hurdle. Req 17 — RAID calibrates the text-detector indicator only.
-   **Verification:** Verified — Crossref. Data and code are at https://github.com/liamdugan/raid (MIT licence). The dataset is public and ungated on Hugging Face (`liamdugan/raid`).

Elkan, C., & Noto, K. (2008). Learning classifiers from only positive and unlabeled data. In *Proceedings of the 14th ACM SIGKDD International Conference on Knowledge Discovery and Data Mining* (pp. 213–220). Association for Computing Machinery. https://doi.org/10.1145/1401890.1401920

-   **Applicability:** Req 13 — the SCAR baseline, *P*(*C* = 1 \| *x*) = *P*(*s* = 1 \| *x*)/*c*, with *c* estimated out of fold on labeled positives after calibration.
-   **Verification:** Verified — Crossref.

Gómez-Boix, A., Laperdrix, P., & Baudry, B. (2018). Hiding in the crowd: An analysis of the effectiveness of browser fingerprinting at large scale. In *Proceedings of the 2018 World Wide Web Conference (WWW '18)* (pp. 309–318). ACM Press. https://doi.org/10.1145/3178876.3186097

-   **Applicability:** Motivation and Req 6 — mobile fingerprints are only 18.5% unique, so adolescents on the same phone model collide legitimately. That is why fingerprint collision is a soft indicator with a group-specific false-positive rate, and why deterministic removal of shared-device respondents is rejected (Req 8).
-   **Verification:** Verified — Crossref (DOI, pages; record carries the short title only) and the ACM Digital Library, dblp and Semantic Scholar listings (full title).

Gordon, A., Rothschild, D., Affonso, F. M., Sulik, J., Hauser, D., Pepin, K., & Jones, S. (2026). *AI agent prevalence and data quality across multiple online sample providers* \[Preprint\]. PsyArXiv. https://doi.org/10.31234/osf.io/pvdjr_v2

-   **Applicability:** Req 2 — its "automated environment check achieving perfect discrimination in pilot testing" is a preprint pilot result, so environment checks enter as indicators and never as a block. Req 17 — its public OSF data (https://osf.io/m2asr) calibrate the agent-facing indicators only. Motivation — its prevalence figures (5,200 respondents, 10 platforms) are panel-based, not open-link. The Claude review flags a potential conflict of interest: Affonso describes this as Prolific's evaluation.
-   **Verification:** Verified — Crossref and the OSF API. Only the versioned DOI (`_v2`, posted 2026-04-01) resolves; the bare `pvdjr` DOI does not. Both quoted claims were confirmed verbatim in the OSF abstract.

Griffin, M., Martino, R. J., LoSchiavo, C., Comer-Carruthers, C., Krause, K. D., Stults, C. B., & Halkitis, P. N. (2022). Ensuring survey research data integrity in the era of internet bots. *Quality & Quantity*, *56*(4), 2841–2852. https://doi.org/10.1007/s11135-021-01252-1

-   **Applicability:** Req 11 — 61.8% of an LGBTQ+ survey's first wave was removed. With Pozzar et al. and Pinzón et al., this shows invalid responses are often the majority on open links, which is why the spec rejects the π \< 0.5 label-switching constraint. Req 20 — a row of the prevalence-by-channel table.
-   **Verification:** Verified — Crossref.

Guy, A. A., Murphy, M. J., Zelaya, D. G., Kahler, C. W., & Sun, S. (2024). Data integrity in an online world: Demonstration of multimodal bot screening tools and considerations for preserving data integrity in two online social and behavioral research studies with marginalized populations. *Psychological Methods*. Advance online publication. https://doi.org/10.1037/met0000696

-   **Applicability:** Req 20 only — listed because the Gemini review attributed a different paper (Stosic et al., 2024) to "Guy et al. 2024". This paper is the multimodal bot-screening study; the spec cites it for no claim.
-   **Verification:** Verified — Crossref (published online 2024-09-09; no volume or issue assigned in the record as of 2026-09-26).

Hans, A., Schwarzschild, A., Cherepanova, V., Kazemi, H., Saha, A., Goldblum, M., Geiping, J., & Goldstein, T. (2024). Spotting LLMs with Binoculars: Zero-shot detection of machine-generated text. *Proceedings of Machine Learning Research*, *235*, 17519–17537. https://proceedings.mlr.press/v235/hans24a.html

-   **Applicability:** Req 7(c) — the other sanctioned open-source detector (ICML 2024). The Claude review reports RAID found it the strongest at low false-positive rates, yet it is still only one weak indicator. Its false-positive rate on short, informal adolescent writing is the Req 7 (open) item.
-   **Verification:** Verified — PMLR page (volume 235, *Proceedings of the 41st International Conference on Machine Learning*); authors from the arXiv API.

Hui, S. L., & Walter, S. D. (1980). Estimating the error rates of diagnostic tests. *Biometrics*, *36*(1), 167–171. https://doi.org/10.2307/2530508

-   **Applicability:** Motivation and Req 11 — the two-population identification route. The spec explains why ad sets and strata cannot substitute for checks: Hui–Walter needs sensitivity and specificity invariant across populations, and Req 10's group-specific false-positive rates deliberately relax that.
-   **Verification:** Verified — Crossref (DOI) and PubMed 7370371 (page range).

Kennedy, R., Clifford, S., Burleigh, T., Waggoner, P. D., Jewell, R., & Winter, N. J. G. (2020). The shape of and solutions to the MTurk quality crisis. *Political Science Research and Methods*, *8*(4), 614–629. https://doi.org/10.1017/psrm.2020.6

-   **Applicability:** Motivation and Req 6 — 76% of VPS users in Study 1 failed no quality check, and some US respondents use VPS for privacy. This underpins the rejection of VPN/IP auto-rejection and the demand that the pilot measure these rules' false-positive rate among gender-minority teens.
-   **Verification:** Verified — Crossref.

Lambert, D., & Luisi, N. (2026). The feasibility and cost-effectiveness of tailored social media advertising to recruit sexual and gender minority adolescents in the Southern United States: Online survey study. *JMIR Formative Research*, *10*, Article e95006. https://doi.org/10.2196/95006

-   **Applicability:** Req 21 — the closest published analogue of the target design: staff identity verification, single-use links, and re-verification before the incentive (34,455 clicks became 384 enrolled adolescents at US\$101.76 each). It is also the model for the Req 17(b) verification subset. Its finding that recruitment costs more in states with more anti-LGBTQIA+ legislation is the Motivation's plausible driver of VPN use among the oversampled teens.
-   **Verification:** Verified — PMC ID converter (PMC13528605 → DOI) and Crossref; published 2026-08-31.

MacDonald, K. V., Nguyen, G. C., Sewitch, M. J., & Marshall, D. A. (2025). Identifying and managing fraudulent respondents in online stated preferences surveys: A case example from best–worst scaling in health preferences research. *The Patient – Patient-Centered Outcomes Research*, *18*(4), 373–390. https://doi.org/10.1007/s40271-025-00740-y

-   **Applicability:** Motivation — the closest precedent for latent-class fusion of fraud checks: LCA classes aligned with age-verified respondent categories. Req 6 — "suspicious email" was the most frequently failed red flag, supporting the email-pattern indicator. This resolves the authorship the Claude review left unverified (PubMed 40316881).
-   **Verification:** Verified — PubMed esummary and Crossref.

MacKinnon, K. R., Khan, N., Newman, K. M., Gould, W. A., Marshall, G., Salway, T., Pullen Sansfaçon, A., Kia, H., & Lam, J. S. H. (2025). Introducing novel methods to identify fraudulent responses (Sampling With Sisyphus): Web-based LGBTQ2S+ mixed-methods study. *Journal of Medical Internet Research*, *27*, Article e63252. https://doi.org/10.2196/63252

-   **Applicability:** Req 20 — "MacKinnon et al. 2025 retention" is a named conflict. It is an LGBTQ2S+ open-link study that kept 957 of 1,377 completed responses (69.5%), making it a row of the prevalence-by-channel table and the population nearest the study's oversampled stratum.
-   **Verification:** Verified — Crossref (authorship; MacKinnon is first author). The retention figure was confirmed in the indexed abstract (OpenAlex) by web search; the publisher page would not render for the agent.

Mi, X., Feng, X., Liao, X., Liu, B., Wang, X., Qian, F., Li, Z., Alrwais, S., Sun, L., & Liu, Y. (2019). Resident Evil: Understanding residential IP proxy as a dark service. In *2019 IEEE Symposium on Security and Privacy (SP)* (pp. 1185–1201). IEEE. https://doi.org/10.1109/SP.2019.00011

-   **Applicability:** Motivation and Req 6 — only 2.2% of 6.2 million residential-proxy IPs appear on any blacklist, so proxy detection misses the very infrastructure farms use. This is part of the case for soft indicators over IP auto-rejection.
-   **Verification:** Verified — Crossref (title search).

Oyler, D. R., Edgecombe, S. J., Babusci, E. A., Dolly Prothro, J., Begley, A. L., & Rojas-Ramirez, M. V. (2026). Practical fraud detection and prevention in incentivized online surveys: Secondary analysis of the ADOPT study. *Journal of Medical Internet Research*, *28*, Article e90159. https://doi.org/10.2196/90159

-   **Applicability:** Motivation — the "2026 ADOPT secondary analysis" that states it had no ground-truth labels; evidence that practice remains rule counts with unknown error rates.
-   **Verification:** Verified — PMC ID converter (PMC13426555 → DOI) and Crossref; published 2026-07-31. The spec's short name "ADOPT secondary analysis" now has authors.

Phillips, G., Felt, D., Fish, J. N., Ruprecht, M. M., Birkett, M., & Poteat, V. P. (2020). A response to Cimpian and Timmer (2020): Limitations and misrepresentation of "mischievous responders" in LGBT+ health research. *Archives of Sexual Behavior*, *49*(5), 1409–1414. https://doi.org/10.1007/s10508-020-01746-3

-   **Applicability:** Req 4(a) — "mischief screening is contested (Phillips 2020)". Screening methods "carry a risk of conflating sexual and gender minority youth (SGMY) with mischievous responders". This is why low-base-rate counts never become a hurdle or a weight, and instead enter the fusion model with group-specific false-positive rates. It responds to Cimpian, J. R., & Timmer, J. D. (2020), Mischievous responders and sexual minority youth survey data, *Archives of Sexual Behavior*, *49*(4), 1097–1102, https://doi.org/10.1007/s10508-020-01661-7.
-   **Verification:** Verified — Crossref (both papers); the claim was confirmed in the article text. The article header styles the first author "Gregory Phillips II"; Crossref omits the suffix.

Pinzón, N., Koundinya, V., Galt, R. E., Dowling, W. O'R., Baukloh, M., Taku-Forchu, N. C., Schohr, T., Roche, L. M., Ikendi, S., Cooper, M., Parker, L. E., & Pathak, T. B. (2024). AI-powered fraud and the erosion of online survey integrity: An analysis of 31 fraud detection strategies. *Frontiers in Research Metrics and Analytics*, *9*, Article 1432774. https://doi.org/10.3389/frma.2024.1432774

-   **Applicability:** One of the few studies with verified ground truth, using closed distribution lists as the valid reference. The spec uses it in five places:
    -   Motivation: rule ensembles reached high recall only "with persistent unacceptable error rates exceeding 5%", where error is the share of valid responses flagged, i.e. a false-rejection rate.
    -   Req 5: the speeding tiers (≤30% and 31–50% of the median time).
    -   Req 6: the MinFraud Risk Score and a novel email-address score are among the best indicators, but 95% of fraud had US IPs, so IP country is a weak, soft signal.
    -   Req 11: 36–39% verified fraud on open distributions (560/1,540; 627/1,616), which is why the prevalence constraint is rejected.
    -   Req 20: the "559 of 560" reading.
-   **Verification:** Verified — Crossref. Every figure above was checked in the full text (Europe PMC, PMC11646990). The spec's Motivation wording is supported. Req 20 resolution: see the conflicts table.

Pozzar, R., Hammer, M. J., Underhill-Blazey, M., Wright, A. A., Tulsky, J. A., Hong, F., Gundersen, D. A., & Berry, D. L. (2020). Threats of bots and other bad actors to data quality following research participant recruitment through social media: Cross-sectional questionnaire. *Journal of Medical Internet Research*, *22*(10), Article e23021. https://doi.org/10.2196/23021

-   **Applicability:** Several requirements lean on this study:
    -   Req 6: 82.5% of screeners arrived between midnight and 4 a.m., which is the basis for the submission-hour indicator.
    -   Req 11: 94.5% of completed surveys were invalid, part of the case that invalid responses can be the majority.
    -   Req 16 and the Verification list: 576 screeners in 7 hours is the "Pozzar-shaped fixture" that both the burst detector and the CUSUM must trip.
-   **Verification:** Verified — Crossref.

Pratt-Chapman, M., Moses, J., & Arem, H. (2021). Strategies for the identification and prevention of survey fraud: Data analysis of a web-based survey. *JMIR Cancer*, *7*(3), Article e30730. https://doi.org/10.2196/30730

-   **Applicability:** Motivation — the exemplar of rule-count practice: a two-indicator rule excluded 1,408 of 1,977 surveys with no error rate. That is the gap the fusion model (Req 10) exists to close.
-   **Verification:** Verified — Crossref (title search). Supplies the citation the ChatGPT review gave only as "JMIR Cancer (2021)".

Qu, Y., Tan, M., & Kutner, M. H. (1996). Random effects models in latent class analysis for evaluating accuracy of diagnostic tests. *Biometrics*, *52*(3), 797–810. https://doi.org/10.2307/2533043

-   **Applicability:** Req 10 (chosen) — the other half of "the Qu–Tan–Kutner / Dendukuri–Joseph conditional-dependence device": one shared random effect whose class-specific loadings absorb dependence among checks.
-   **Verification:** Verified — Crossref (DOI) and PubMed 8805757 (page range).

Robinson-Cimpian, J. P. (2014). Inaccurate estimation of disparities due to mischievous responders: Several suggestions to assess conclusions. *Educational Researcher*, *43*(4), 171–185. https://doi.org/10.3102/0013189X14534297

-   **Applicability:** Req 4(iii) — the origin of low-base-rate "mischievous" screener items. Req 9 — the recorded extension (a third mixture component inflating endorsement of those items) is "a mixture version of Cimpian's screener logic".
-   **Verification:** Verified — Crossref.

Rubin, D. B. (1987). *Multiple imputation for nonresponse in surveys*. Wiley. https://doi.org/10.1002/9780470316696

-   **Applicability:** Req 15 (chosen) — Rubin's rules combine substantive estimates across the *M* imputations of class membership, carrying classification uncertainty into analysis instead of deleting cases.
-   **Verification:** Verified — Crossref.

Schroeders, U., Schmidt, C., & Gnambs, T. (2022). Detecting careless responding in survey data using stochastic gradient boosting. *Educational and Psychological Measurement*, *82*(1), 29–56. https://doi.org/10.1177/00131644211004708

-   **Applicability:** Req 13 — the supervised gradient-boosting classifier whose precision against instructed-careless labels was at most 19% (per Alfons & Welz, 2024). This is why supervised scoring is only a positive-unlabeled sensitivity analysis.
-   **Verification:** Verified — Crossref (online 2021; issue 2022).

Snijders, T. A. B. (2001). Asymptotic null distribution of person fit statistics with estimated person parameter. *Psychometrika*, *66*(3), 331–342. https://doi.org/10.1007/BF02294437

-   **Applicability:** Req 3 — the "Snijders-corrected *l_z*\*" person-fit index. Its R reference implementation is `PerFit::lzstar`, the parity target of Req 19.
-   **Verification:** Verified — Crossref.

Stosic, M. D., Murphy, B. A., Duong, F., Fultz, A. A., Harvey, S. E., & Bernieri, F. (2024). Careless responding: Why many findings are spurious or spuriously inflated. *Advances in Methods and Practices in Psychological Science*, *7*(1), Article 25152459241231581. https://doi.org/10.1177/25152459241231581

-   **Applicability:** Req 20 only — the true authors of the paper the Gemini review attributed to "Guy, M. D., et al. (2024)". The spec cites it for no claim, but the correction must be on the record.
-   **Verification:** **Corrected** — Crossref DOI record (online 2024-03-15). No "Guy, M. D." is an author.

Ulitzsch, E., Pohl, S., Khorramdel, L., Kroehne, U., & von Davier, M. (2022). A response-time-based latent response mixture model for identifying and modeling careless and insufficient effort responding in survey data. *Psychometrika*, *87*(2), 593–619. https://doi.org/10.1007/s11336-021-09817-7

-   **Applicability:** Req 9 (chosen) — the careless-responding component is specified "after Ulitzsch et al. (2022), simplified": attentive graded responses versus uniform careless responses with faster response times, plus a position slope for onset.
-   **Verification:** Verified — Crossref.

Ulitzsch, E., Shin, H. J., & Lüdtke, O. (2024). Accounting for careless and insufficient effort responding in large-scale survey data—Development, evaluation, and application of a screen-time-based weighting procedure. *Behavior Research Methods*, *56*(2), 804–825. https://doi.org/10.3758/s13428-022-02053-6

-   **Applicability:** Req 9 (chosen) — justifies screen-level mixture components on Qualtrics page timers; per-question JavaScript response times are rejected for v1.
-   **Verification:** Verified — Crossref (DOI) and PubMed 36867339 (February 2024 issue; online 2023-03-03).

Veselovsky, V., Horta Ribeiro, M., & West, R. (2023). *Artificial artificial artificial intelligence: Crowd workers widely use large language models for text production tasks* \[Preprint\]. arXiv. https://doi.org/10.48550/arXiv.2306.07899

-   **Applicability:** Req 5 — 41 of 46 LLM-written summaries involved pasting, which makes paste count the most discriminating signal against LLM-assisted humans. A related peer-reviewed piece by an overlapping team is Veselovsky, V., Horta Ribeiro, M., Cozzolino, P. J., Gordon, A., Rothschild, D., & West, R. (2025). Prevalence and prevention of large language model use in crowd work. *Communications of the ACM*, *68*(3), 42–47. https://doi.org/10.1145/3685527
-   **Verification:** Verified — arXiv API and DataCite (preprint); Crossref (CACM piece).

Wang, Q., Mamaev, B., & Leckie, C. (2026). *Towards detecting AI-assisted responses in online surveys* \[Preprint\]. arXiv. https://doi.org/10.48550/arXiv.2609.17317

-   **Applicability:** Req 17 — the source of the ASURRE benchmark, which the spec names as a calibration resource for the text-detector indicator only. Req 7(c) — its finding that persona-grounded agents push detectors toward chance (via the Claude review, KF2) is why the detector stays a weak indicator. Posted 2026-09-15, eleven days before the spec.
-   **Verification:** Citation verified — arXiv API and DataCite (v1 is the only version). **The ASURRE data are not yet public.** The repository the paper names (https://github.com/mike-qz-wang/ASURRE) exists but is empty: created 2026-08-31, no content as of 2026-09-26. So Req 17's list of public calibration resources overstates ASURRE's availability for now.

Welz, M., & Alfons, A. (2023). *When respondents don't care anymore: Identifying the onset of careless responding* \[Preprint\]. arXiv. https://doi.org/10.48550/arXiv.2303.07167

-   **Applicability:** Req 9 — CODERS ("careless onset detection in extensive rating-scale surveys"), a discrete changepoint with nonparametric false-positive guarantees. It is (rejected for v1) in favour of the smooth onset slope κ, and recorded as the roadmap alternative. Its reference implementation is `carelessonset` (see Software).
-   **Verification:** Verified — arXiv API and DataCite. The latest version is v4 (2026-03-13), and no journal publication was found.

Westwood, S. J. (2025). The potential existential threat of large language models to online survey research. *Proceedings of the National Academy of Sciences*, *122*(47), Article e2518075122. https://doi.org/10.1073/pnas.2518075122

-   **Applicability:** The spec's main evidence that autonomous agents defeat conventional checks:
    -   Motivation: 99.8% of 6,000 attention-check trials passed while simulating reading times, mouse movement and typing.
    -   Req 2: attestation proves a browser, not an eligible human; the agent was built around reCAPTCHA bypass tools.
    -   Req 3: persona-consistent psychometric profiles defeat careless indices.
    -   Req 5: keystroke-level typing blunts paste and focus signals.
    -   Req 12: agents bias answers toward the inferred hypothesis.
    -   Req 17: its persona-plus-memory design is the template for seeded agents.
    -   Req 20: its figures are a named conflict.
-   **Verification:** Verified — Crossref. "A 99.8% pass rate on 6,000 trials" confirmed in the full text (Europe PMC, PMC12663962). **Corrected** in the Gemini review, which gave this DOI the title of Richardson, R. A. K., Hong, S. S., Byrne, J. A., Stoeger, T., & Amaral, L. A. N. (2025), *The entities enabling scientific fraud at scale are large, resilient, and growing rapidly*, *PNAS*, *122*(32), e2420092122 — an unrelated paper-mill study.

Wilson, E. B. (1927). Probable inference, the law of succession, and statistical inference. *Journal of the American Statistical Association*, *22*(158), 209–212. https://doi.org/10.1080/01621459.1927.10502953

-   **Applicability:** Req 17 — Wilson intervals for per-threat sensitivity and per-group false-rejection rates. They produce the stated pilot precision (36/40 → 0.77–0.96; 3/60 → 0.017–0.137, re-derived here), which sets the Req 14 α defaults.
-   **Verification:** Verified — Crossref.

Zhang, S., Xu, J., & Alvero, A. J. (2025). Generative AI meets open-ended survey responses: Research participant use of AI and homogenization. *Sociological Methods & Research*, *54*(3), 1197–1242. https://doi.org/10.1177/00491241251327130

-   **Applicability:** Req 7(b) — the cross-respondent stylistic-homogeneity indicator (embedding cosine to centroid and neighbours, length and lexicon shift) targets the homogenization of AI-assisted open-ends this paper documents.
-   **Verification:** Verified — Crossref (a 2025 SocArXiv preprint also exists). \## Software — R reference implementations

Beath, K. J. (2017). randomLCA: An R package for latent class with random effects analysis. *Journal of Statistical Software*, *81*(13), 1–25. https://doi.org/10.18637/jss.v081.i13

-   **Package:** *randomLCA: Random effects latent class analysis* (Version 1.1-4, published on CRAN 2024-09-23). https://CRAN.R-project.org/package=randomLCA
-   **Applicability:** Motivation — named as R tooling for random-effects latent class analysis that has no maintained Python equivalent. That gap is part of the case for the package.
-   **Verification:** Verified — Crossref (article); CRAN page (version and date); the page range is from the journal's site.

Tendeiro, J. N., Meijer, R. R., & Niessen, A. S. M. (2016). PerFit: An R package for person-fit analysis in IRT. *Journal of Statistical Software*, *74*(5), 1–27. https://doi.org/10.18637/jss.v074.i05

-   **Package:** *PerFit: Person fit* (Version 1.4.7, published on CRAN 2025-04-02; maintainer J. N. Tendeiro). https://CRAN.R-project.org/package=PerFit

-   **Applicability:** Reqs 3 and 19 — the reference implementation of *l_z* and *l_z*\* for the committed parity golden files. **PerFit 1.4.7 has no polytomous *l_z*\*:**

    -   `lzstar` takes dichotomous items only.
    -   `lzpoly` extends the original *l_z*, which the manual attributes to Drasgow, Levine & Williams (1985), not Snijders' correction.
    -   The package describes its polytomous statistics as "extensions of lz, U3, and (normed) number of Guttman errors".

    So the Verification bullet requiring parity "including the polytomous *l_z*\*" has no R golden file to match. Either the bullet should target `lzpoly`, or the polytomous *l_z*\* needs a different reference implementation.

-   **Verification:** Verified — Crossref (article); CRAN page and reference manual (functions and description); the page range is from the journal's site.

Welz, M., & Alfons, A. (n.d.). *carelessonset: Estimating the onset of careless responding* (Version 0.1.0) \[R package\]. GitHub. Retrieved September 26, 2026, from https://github.com/mwelz/carelessonset

-   **Applicability:** Req 9 and Motivation — the reference implementation of CODERS (Welz & Alfons, 2023), the recorded alternative to the onset slope κ. It is not on CRAN and needs Python's TensorFlow/Keras through `reticulate`. Licence: GPL (≥ 3).
-   **Verification:** Verified — GitHub API (the DESCRIPTION file; tags v0.0.1 and v0.1.0, with no dated release). The repository description still uses the working-paper title "I Don't Care Anymore: Identifying the Onset of Careless Responding".

Yentes, R., & Wilhelm, F. (2023). *careless: Procedures for computing indices of careless responding* (Version 1.2.2) \[R package\]. https://CRAN.R-project.org/package=careless

-   **Applicability:** Reqs 3 and 19 — the reference implementation for long-string, intra-individual response variability, even–odd consistency, psychometric synonyms and antonyms, and Mahalanobis *D*² (`longstring`, `irv`, `evenodd`, `psychsyn`, `psychant`, `mahad`). It supplies the parity golden files.
-   **Verification:** Verified — CRAN page (published 2023-10-01) and reference manual (all six functions present).

## Vendor and platform documentation — vendor claims, never performance evidence (Req 20)

Every entry here was fetched on 2026-09-26, and the key quotations were checked against the page source. Titles are the on-page headings; where a review cited a page by its browser-tab title, the note says so.

CloudResearch. (n.d.). *Sentry: Survey fraud detection software*. Retrieved September 26, 2026, from https://www.cloudresearch.com/products/fraud-detection/

-   **Applicability:** Out of scope and Req 20. This is a pre-survey vetting layer that the vendor says "can be used with any sample source, survey platform, or respondent device". No independent evaluation exists, so the spec leaves it out and labels its figures vendor.
-   **Verification:** Verified — page fetched; no visible date.

CloudResearch. (2025, December 12). *AI-agent detection in survey research: Results of a randomized trial to detect human vs AI-generated survey data*. https://www.cloudresearch.com/resources/blog/ai-agent-detection/

-   **Applicability:** Req 20 — the source of the Sentry figure "\>99% with a false positive rate below 1%", measured on the vendor's own Engage platform. The ChatGPT review quoted it as detection performance; it is a vendor claim only.
-   **Verification:** Verified — page fetched. The figure does not appear on the pages the reviews cite.

Google for Developers. (2024, July 10). *reCAPTCHA v3*. https://developers.google.com/recaptcha/docs/v3

-   **Applicability:** Reqs 1, 2 and 5 — the score behind Qualtrics' `Q_RecaptchaScore` ("1.0 is very likely a good interaction, 0.0 is very likely a bot"). It attests a real browser, not an eligible human, so it is logged, never thresholded at ingestion, and enters Req 5 separately from its status field.
-   **Verification:** Verified — page fetched ("Last updated 2024-07-10 UTC").

Gordon, A. (2026, February 4). *Authenticity checks: How we tested the most accurate method for identifying agentic AI*. Prolific. https://www.prolific.com/resources/authenticity-checks-how-we-tested-the-most-accurate-method-for-identifying-agentic-ai

-   **Applicability:** Req 20 and Out of scope — a vendor-internal test. Its bot check reports "100% sensitivity and 100% specificity" against five AI agents, and its LLM check "examines over 15 different behaviors, such as copy-pasting and tab-switching". It works only for Prolific-sourced participants, so it cannot serve an open link.
-   **Verification:** Verified — page fetched. The reviews cite it by its browser-tab title, "Authenticity checks detect AI agents best".

Haycraft Mee, L. (2026, February 4). *Introducing authenticity checks: Detect AI agents and responses in your research with exceptional accuracy*. Prolific. https://www.prolific.com/resources/introducing-authenticity-checks-beta-ensure-genuine-human-responses-in-the-age-of-ai

-   **Applicability:** Req 20 — vendor figures: the LLM check's "98.7% precision and a 0.6% false-positive rate", and "only 0.04%" of more than a million submissions flagged as AI agents.
-   **Verification:** Verified — page fetched. The reviews cite it by its browser-tab title, "New authenticity checks detect AI misuse in research".

MaxMind. (n.d.). *Overall risk score*. MaxMind Knowledge Base. Retrieved September 26, 2026, from https://support.maxmind.com/knowledge-base/articles/overall-risk-score-minfraud-maxmind

-   **Applicability:** Req 6 — the minFraud risk score behind the `vendor_risk_score` indicator, which Pinzón et al. (2024) rank among their best. MaxMind reports it as a percentage from 0.01 to 99, while Req 1 types the field as \[0, 1\], so the adapter must rescale it. No independent accuracy evaluation exists (Motivation).
-   **Verification:** Verified — page fetched (MaxMind's older help-centre URL redirects here); no visible date.

Prolific. (2026, August 4). *Methodological justification pack: Responding to reviewer and editor concerns*. Prolific Researcher Help Centre. https://researcher-help.prolific.com/en/articles/621856-methodological-justification-pack-responding-to-reviewer-and-editor-concerns

-   **Applicability:** Req 20 — the actual source of the Claude review's "January 2026 internal audit found that 0.8% of responses … were flagged for AI-generated content". It also gives the LLM check's recall (78.9%) and accuracy (88.4%) next to its 98.7% precision, which the headline figure leaves out.
-   **Verification:** Verified — page fetched ("Last updated: 04 August 2026").

Qualtrics. (n.d.). *Fraud detection*. Qualtrics Support. Retrieved September 26, 2026, from https://www.qualtrics.com/support/survey-platform/survey-module/survey-checker/fraud-detection/

-   **Applicability:** Reqs 1 and 2 — documents the export fields the adapter reads: `Q_RecaptchaScore` (0.0–1.0), `Q_RecaptchaStatus` ("complete" or "error"), `Q_RecaptchaError` (the check could not run), `Q_DuplicateRespondent`, and `Q_BallotBoxStuffing`. This informs the Req 1 (open) item but does not discharge it; that still needs an inventory of the live instance's export. The documented status values differ from the spec's target categories (`ok`, `error`, `script_load_failed`, `missing`), so the adapter needs an explicit mapping. For Req 20, it dates RelevantID's deprecation to June 30, 2025.
-   **Verification:** Verified — page fetched (no visible date). The deprecation notice is also in an August 2025 archived copy. Institutional RelevantID notices consulted are Memorial University (2025-06-12), Boise State (2025-06-16), Ohio State (2025-06-18) and Duke (2025-07-07, https://oit.duke.edu/news/changes-qualtrics-relevant-id); JHU's notice blocked automated access.

Wardrop, B. (2026, June 9). *Introducing Sentry's Verisoul integration and what it means for data quality*. CloudResearch. https://www.cloudresearch.com/resources/blog/sentry-verisoul-integration-data-quality/

-   **Applicability:** Req 20 — the Verisoul figure the spec requires to be labeled vendor: Sentry and Verisoul "together flagged 84% of low-quality respondents". Verisoul is a separate device-fraud vendor (https://www.verisoul.ai) whose checks are integrated into Sentry.
-   **Verification:** Verified — page fetched.

## Not included

-   **Cited by the reviews but not taken up by the spec:** Jaffe et al. (2026); Comachio et al. (2025); Ward & Meade (2023); Lebrun et al. (2024); Mayer et al. (2025); Poese et al. (2011); Dennis, Goodson & Pearson (2020); Panesar & Mayo (2023); Salinas (2023); Ali et al. (SOUPS 2026); Teitcher et al.; Ulitzsch et al. (2022, *BJMSP*); Alfons & Wilms (arXiv:2412.20802); Hardt, Price & Srebro (2016), whose "equal opportunity" the spec invokes without attribution. The spec's synthesis did not rest a requirement on them.
-   **Methods the spec names without attribution:** MinHash, SimHash, Leiden, stochastic block models, CUSUM, Mondrian split-conformal prediction, isolation forest, LOF, non-negative and contrastive PU learning, prediction-powered online conformal detection, the graded response model, NUTS and its diagnostics. Their canonical sources can be added if the methods note wants them.
-   **Software left to the plans (Req 19):** networkx, igraph, datasketch, MAPIE, crepes; and the house stack (NumPyro, JAX, ArviZ, Polars), which the spec inherits from the prompt rather than adjudicates.