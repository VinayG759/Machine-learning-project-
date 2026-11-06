# Paper guide: what to write, in what order, with which numbers

Written for: Vinay, who will write the paper in his own words.
This file is notes and facts, not paper text. Do not copy sentences from it into the paper (the plagiarism check will flag them, and the learning is in the writing). Every number below comes from `main.ipynb`; re-run the notebook before you quote anything.

## 0. What the professor asked for

**From the IEEE template (ICICACS 2025, A4, two columns):**
- Title: no symbols, special characters, footnotes, math or abbreviations.
- Authors block (up to six per the template, fewer is allowed).
- Abstract (one paragraph, no symbols or footnotes) and Keywords.
- Roman-numbered headings (I, II, III ...). Do not number them by hand, the template does it.
- Figure captions go **below** the figure ("Fig. 1."), table titles go **above** the table ("TABLE I.").
- Numbered citations in square brackets, like [1], placed before the full stop. References listed in order of first citation, IEEE style.
- Define each abbreviation the first time it is used. Use a zero before decimals (0.25, not .25). The word "data" is plural.
- Write the text in a separate file first, then paste into the template. No page numbers, no hard tabs.

**From the whiteboard (my reading, please confirm the starred items):**

| Part | Requirement |
|---|---|
| Title | yes |
| Keywords | 4 to 5 |
| Abstract | yes |
| I. Introduction | contains **2 figures and 2 tables**; at least one citation [1] |
| II. Literature Review | contains **Table III** with three columns: Title, Technique*, Gaps |
| III. Methodology | contains **Fig. 3: flow chart** |
| IV. Results | yes |
| V. Conclusion and Future Scope | yes |
| References | numbered [1], [2] ... |
| Sources for papers | IEEE Xplore and ScienceDirect (ticked on the board) |

*The middle column of Table III is hard to read on the photo. I read it as "Technique" or "Method". Check with your professor.*

Numbering consequence: Table I, Table II, Fig. 1, Fig. 2 are in the Introduction, Table III is in the Literature Review, Fig. 3 is in the Methodology, and the Results continue from Fig. 4 and Table IV.

## 1. Order of work (do it in this order)

1. **Read** 6 to 8 papers (section 4). For each, fill one row of Table III in your own words from your own understanding.
2. **Write the Methodology first.** It is the easiest, because you built it and the notebook documents every step.
3. **Write the Results** next, straight from the notebook outputs.
4. Write the **Literature Review** and **Introduction** (you now know the field and your own result).
5. Write the **Conclusion and Future Scope**.
6. Write the **Abstract last**, then the **title**, then pick the **keywords**.
7. Move everything into the IEEE template, add the figures and tables, number them, check captions.
8. Check the references against section 7, run a similarity check, proofread.

## 2. Section by section: what to cover

### Title
- Say what is predicted, for whom, and with what kind of inputs. No abbreviations.
- Keep it factual. Example of the pattern (write your own): "Predicting Dropout Among Academically Successful University Students".

### Keywords (4 to 5)
Student dropout, early warning, machine learning, class imbalance, algorithmic fairness. Pick the five that best describe your paper.

### Abstract (about 150 to 250 words, one paragraph, no symbols)
One sentence each, in this order: the problem; the gap; what you did (data, rule, models); the main result with two or three numbers; what it means. Numbers to choose from: AP 0.47 against 0.13 for guessing, AUC 0.73, top 20% of flagged students catches 56% of dropouts. Write it last.

### I. Introduction
Points to cover:
- Why dropout matters (cost to students, colleges, society). **You must cite a source** for any general claim here, for example [1].
- The usual approach: predict dropout for all students, and most of the signal then comes from poor grades.
- Your angle: students who are **already doing well** can still leave, usually for non-academic reasons such as money. They are invisible to grade-based warnings.
- What your paper contributes (keep to three honest points): a definition of "doing well" at the end of semester 1; a leakage-safe pipeline on a public dataset; a fairness audit with sensitive columns excluded.
- Short outline of the paper's sections.

**The 2 tables and 2 figures (my suggestion, all produced from the notebook):**
- **Table I:** the dataset's feature groups (personal, family, entry route, money, semester 1 performance, economy) with an example column and the count in each group.
- **Table II:** class counts: Graduate 2,209, Dropout 1,421, Enrolled 794; kept for modelling 3,630; "doing well" 1,350 with 151 dropouts.
- **Fig. 1:** histograms of semester 1 pass rate and grade by outcome (step 1).
- **Fig. 2:** dropout rate by money factors (debtor, tuition up to date, scholarship) among doing-well students (step 3).

### II. Literature Review
- Group the papers by theme, not one paragraph per paper. Suggested themes: (a) models used for dropout prediction; (b) which predictors matter; (c) class imbalance and evaluation; (d) fairness and sensitive attributes.
- **Table III:** one row per paper: Title, Technique, Gap. The **gap** is what the paper did not do that your work addresses, for example: predicts for all students and not a successful subgroup; reports accuracy on imbalanced data; uses information from later in the year; no fairness check.
- End the section with one paragraph that says how your paper fills the gap.
- Cite only what you have read. Never cite from memory or from a snippet.

### III. Methodology
**Fig. 3 (flow chart), the steps in order:**
1. Dataset: 4,424 students, 36 features, Polytechnic Institute of Portalegre.
2. Remove "Enrolled" students (outcome unknown) -> 3,630.
3. Compute semester 1 pass rate and average grade (grade 0 treated as missing).
4. Stratified split 80/20 -> 2,904 train and 726 test.
5. Course grade cut-offs from the **training** students only.
6. "Doing well" label: pass rate at least 0.8 **and** grade at or above the own-course cut-off.
7. Choose input columns; leave out economy and sensitive columns.
8. Preprocessing pipeline (one-hot encoding, standardisation).
9. Models compared with 5-fold cross-validation repeated 5 times.
10. Best model refitted on all training data and tested once.
11. Evaluation and fairness audit.

**Text to write:** dataset description; prediction moment (end of semester 1); the label rule and why (include the finding that a grade of 0 means "passed nothing"); why the split comes first (data leakage); feature groups and which were excluded; models and settings; evaluation metrics and why accuracy is not used (11% positive class); how cross-validation and the bootstrap interval work.

Facts to include: course 171 has 152 students with all-zero semester 1 records (likely missing data stored as zeros); courses with fewer than 30 graded training students use the pooled median (12.75); `class_weight = balanced`; random seed 42.

### IV. Results (continue numbering: Table IV onward, Fig. 4 onward)
- **Table IV, feature-set comparison** (logistic regression, 25 folds, mean AP / AUC): money only 0.320 / 0.683; external only 0.380 / 0.689; external + semester 1 0.424 / 0.717; external + economy 0.378 / 0.699; external + sensitive 0.384 / 0.686; everything 0.417 / 0.727. Guessing AP is 0.107. Spread between folds is about 0.06, so differences of a few hundredths are not clear-cut.
- **Table V, model comparison** (same inputs): logistic C=0.1 0.424 / 0.717; C=0.3 0.422 / 0.715; C=0.03 0.415 / 0.718; C=0.01 0.406 / 0.719; random forest (leaf 5) 0.395 / 0.721; gradient boosting 0.382 / 0.691; random forest (leaf 15) 0.355 / 0.703. A simple model won; say why that is plausible with 117 positives.
- **Test result:** AP 0.474 (95% bootstrap interval 0.322 to 0.631), AUC 0.726 (0.616 to 0.826), guessing AP 0.131. Say clearly that only 34 test dropouts exist and the interval is wide.
- **Table VI, flagging the top of the risk list:** top 10% (26 students) catches 12 dropouts (35%), precision 0.46, lift 3.5; top 20% (52) catches 19 (56%), precision 0.37, lift 2.8; top 30% (78) catches 21 (62%), precision 0.27; top 50% (130) catches 25 (74%), precision 0.19.
- **Fig. 4:** precision-recall curve. **Fig. 5:** permutation importance chart.
- **Important factors:** credited units, approved courses, tuition up to date, debtor status, evaluations, age, course. Direction in training data: credited units 14.8% against 9.9% dropout; age 25 and over 18% against 9%; unpaid tuition 66.7% (39 students) against 8.7%; debtor 41.7% against 8.9%; scholarship 6.4% against 13.3%.
- **Fairness table (top 20% policy):** recall 0.50 for women and 0.61 for men; precision 0.29 and 0.46; age group 21 to 24 has only 2 dropouts, so ignore it. Present as a caution, because groups are small.
- **Do not say:** that unpaid tuition causes dropout (timing is unknown), why credited units matter (not explained by the data), or that your model is better than other papers (see section 5).

### V. Conclusion and Future Scope
- Conclusion: three or four sentences with the main result, the main insight (money and early performance signals), and the honest strength (about 3 times better than guessing).
- Limitations: one institution; 34 test positives; no health, family or mental-health data; undocumented columns (evaluations, timing of tuition data); small groups in the fairness audit; economy columns mark only 10 cohorts.
- Future scope: validate on other institutions and later years (train on earlier cohorts, test on later ones); more data on positives; probability calibration; SHAP explanations; adding wellbeing and attendance data; a pilot study to test whether support actually reduces dropout.

### References
IEEE numbered style, in order of first citation. Use the formats in the template (journal article, conference paper, dataset). Include the dataset paper (section 4).

## 3. Figures and tables: practical rules from the template
- Place figures and tables at the top or bottom of a column; cite each in the text before it appears.
- Always write "Fig. 1" (even at the start of a sentence). Table titles in capitals above the table.
- Axis labels in words, with units in parentheses; 8 pt Times New Roman for labels.
- Export charts at 300 dpi. I can export the notebook charts as clean PNG files if you want.
- The flow chart (Fig. 3) is drawn in PowerPoint, draw.io or Word. I can produce an editable diagram file if you want.

## 4. Papers to read first (all found by search; I only saw abstracts, so read each one yourself before using it)

**IEEE Xplore:**
- [Predicting Dropout in Higher Education Based on Secondary School Performance](https://ieeexplore.ieee.org/abstract/document/8523888): predicts dropout from entry-time data; enrolment-time information only.
- [Student Dropout Prediction in High Education, Using Machine Learning and Deep Learning Models: Case of Ecuadorian University](https://ieeexplore.ieee.org/document/10590352/): compares ML and deep learning; reports very high accuracy.
- [Application of Logistic Regression Technique for Predicting Student Dropout](https://ieeexplore.ieee.org/document/9920280/): logistic regression, which is your best model too.
- [Student Dropout Prediction Using Machine Learning](https://ieeexplore.ieee.org/document/10875840/): XGBoost with resampling for class imbalance.
- [Predictive Modeling and Explainability for Academic Dropout Risk Detection Using Machine Learning](https://ieeexplore.ieee.org/document/11081590/): ensemble with SHAP and LIME. It reports AUC 0.995, which is unusually high; read how it was validated before trusting it.
- [A Portable, Generalizable Machine Learning Framework for Long-Term Student Dropout Prediction](https://ieeexplore.ieee.org/document/11406099/): portability across institutions and long horizons.
- [Student Dropout Forecasting and Feature Analysis through Machine Learning Algorithms](https://ieeexplore.ieee.org/document/11258036)

**ScienceDirect:**
- [Predicting student dropouts with machine learning: An empirical study in Finnish higher education](https://www.sciencedirect.com/science/article/pii/S0160791X24000228)
- [Predicting dropout from higher education: Evidence from Italy](https://www.sciencedirect.com/science/article/pii/S0264999323003954)
- [Predicting student's dropout in university classes using two-layer ensemble machine learning approach: A novel stacked generalization](https://www.sciencedirect.com/science/article/pii/S2666920X22000212)
- [Performance and early drop prediction for higher education students using machine learning](https://www.sciencedirect.com/science/article/abs/pii/S095741742300581X)
- [Predicting student retention: A comparative study of machine learning approach utilizing sociodemographic and academic factors](https://www.sciencedirect.com/science/article/pii/S277294192500170X)

**The dataset (you must cite it):** V. Realinho, J. Machado, L. Baptista, and M. V. Martins, "Predicting student dropout and academic success," *Data*, vol. 7, no. 11, art. 146, 2022, doi: 10.3390/data7110146. Page: [doaj.org record](https://doaj.org/article/01a8ef129677407983e785650966e062). Check the details against the published version before you finalise the reference.

**Fairness (not from IEEE or ScienceDirect, so ask your professor if allowed):** [Predicting Early Dropout: Calibration and Algorithmic Fairness Considerations](https://arxiv.org/pdf/2103.09068). Another search result reported that a large college audit found removing protected attributes did not clearly improve accuracy or fairness. I only saw a news summary of it, so find and read the original paper before relying on it.

## 5. What you can and cannot claim

- **Safe:** "Using only information available at the end of semester 1, a logistic regression identifies about 56% of dropouts among doing-well students when the top 20% are flagged, about three times better than random."
- **Not safe:** "Our model is better than [other paper]." Others report accuracy above 90% and AUC above 0.95, but they usually predict dropout for **all** students (including those failing), often with later-year information, on different data. Your task is harder and your numbers are not comparable. Explain this difference in the Results and in the Literature Review, and do not compete on the numbers.
- **Not safe:** causal wording ("unpaid fees cause dropout"). Use "is associated with".
- **Say plainly:** the test set has 34 positives, the interval is wide, and the fairness groups are small.

## 6. Avoiding plagiarism (the professor mentioned a test)
- Read a paper, close it, then write your notes from memory in your own words. Do not write with the paper open beside you.
- Rewording a sentence by swapping words is still plagiarism. Restate the idea from your own understanding.
- Cite every idea that is not yours, even when you rewrite it.
- Standard definitions (precision, recall, cross-validation) should be explained in your own sentences, not copied from a website or from this file.
- Keep a list of every source you use as you read.
- Do not paste text from this guide or from any AI tool into the paper. Check what your course allows regarding AI assistance, and follow it.
- Run a similarity check before submitting if one is available to you.

## 7. Final checklist
- [ ] Title has no abbreviations or symbols.
- [ ] 4 to 5 keywords.
- [ ] Abstract is one paragraph, no symbols.
- [ ] Introduction: 2 figures and 2 tables, with a citation.
- [ ] Literature Review: Table III filled from papers you read.
- [ ] Methodology: Fig. 3 flow chart.
- [ ] Results, Conclusion and Future Scope, References.
- [ ] Every figure and table is cited in the text and numbered in order.
- [ ] Every abbreviation defined at first use (AP, AUC, ROC, SMOTE if used).
- [ ] References in IEEE format, numbered by first citation, none cited from memory.
- [ ] Similarity check done.
