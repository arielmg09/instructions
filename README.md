# Non-IRB Model Validation Instructions

**4. Model Performance**

Performance validation assesses whether a model produces outputs that are sufficiently accurate, stable, and fit for their intended purpose. The tests described in this section do not constitute a mandatory checklist; the validator shall exercise professional judgement to identify which assessments are proportionate and appropriate given the model's type, materiality, and use case. For simple or low-materiality models, a subset of tests may suffice. For complex or high-materiality models, the validator should aim for breadth of coverage across sub-sections. Where a particular test is considered inapplicable, the validation report shall record the rationale for its exclusion.

---

### 4.1 Statistical Performance Testing

#### Purpose

Statistical performance testing evaluates the technical quality of model outputs using quantitative methods. It provides the evidential foundation for the validator's assessment of whether the model is performing in line with its design objectives. The tests in this sub-section span measures of discriminatory power, calibration accuracy, predictive fit, and residual behaviour. Not all tests will be applicable to every model type; the validator shall select methods appropriate to the model's functional form and output type.

---

#### 4.1.1 Discriminatory Power

Discriminatory power testing assesses the degree to which a model correctly ranks or separates observations — for example, distinguishing defaulted from non-defaulted exposures, or high-risk from low-risk cases. It is most directly applicable to classification and scoring models but may also be relevant to models that produce ranked outputs or banded categories.

The validator should consider the following techniques, as applicable:

**Area Under the ROC Curve (AUROC / Gini Coefficient).** The Receiver Operating Characteristic curve plots the true positive rate against the false positive rate across all classification thresholds. The AUROC summarises this in a single statistic between 0.5 (random) and 1.0 (perfect separation). The Gini coefficient is a linear transformation of the AUROC (Gini = 2 × AUROC − 1) and may be preferred in credit risk contexts. The validator should assess whether the observed AUROC/Gini is consistent with model development findings and, where benchmarks exist, with comparable models or industry references. Thresholds for acceptable discriminatory power should be determined with reference to the model's intended use and the population characteristics; there is no universal minimum, though values below 0.60 AUROC typically warrant scrutiny.

**Kolmogorov-Smirnov (KS) Statistic.** The KS statistic measures the maximum separation between the cumulative distribution functions of the positive and negative outcome populations. It is particularly useful for identifying the score or threshold at which the model achieves its greatest separation. The validator should note that KS is sensitive to the score distribution shape and may overstate apparent performance where the population is highly imbalanced.

**Accuracy, Precision, Recall, and F1 Score.** For binary classification models (e.g. fraud detection, financial crime alerts), the validator should assess performance across the confusion matrix. Precision and recall trade-offs should be evaluated in the context of the model's use case — for example, a fraud model deployed with downstream human review may tolerate higher false positive rates, whereas an automated decisioning model may require tighter precision. The F1 score, as the harmonic mean of precision and recall, provides a balanced summary statistic. Where class imbalance is material, the validator should consider the use of the Precision-Recall AUC in preference to, or alongside, the ROC AUC.

**Cumulative Accuracy Profile (CAP) and Accuracy Ratio.** The CAP curve plots the proportion of positives captured as a function of the proportion of the population ranked from highest to lowest risk. The Accuracy Ratio — the ratio of the area under the model CAP to that of a perfect model — provides an interpretable summary measure analogous to the Gini coefficient.

**Lift Charts and Decile Analysis.** Lift analysis segments the population into ranked deciles and compares the concentration of positive outcomes in each decile to the base rate. It provides an accessible representation of discriminatory power that is often useful for communicating results to non-technical stakeholders. The validator should assess whether the model consistently concentrates the majority of positive outcomes in the highest-risk deciles.

---

#### 4.1.2 Calibration and Bias Assessment

Calibration testing assesses whether the model's predicted values are appropriately scaled relative to observed outcomes — that is, whether the model is systematically over- or under-predicting. Good discrimination is a necessary but not sufficient condition for model fitness; a model can rank observations correctly while producing predicted values that are materially biased in absolute terms. Calibration is particularly important for models whose outputs are used directly in financial calculations, capital estimation, or limit-setting.

**Mean Predicted vs. Mean Observed Comparison.** The most direct calibration test is a comparison of the average predicted value with the average realised outcome across the validation sample. Significant and persistent differences suggest systematic bias. The validator should assess whether such differences can be attributed to structural factors (e.g. portfolio mix changes, macroeconomic shifts) or reflect model deficiencies requiring recalibration.

**Hosmer-Lemeshow Test.** For binary outcome models (e.g. probability of default), the Hosmer-Lemeshow test formally tests goodness-of-fit by partitioning observations into deciles of predicted probability and comparing predicted and observed event rates within each group using a chi-squared statistic. The validator should note that the test has known limitations at very large sample sizes, where it may reject adequate models due to statistical power, and at small sample sizes, where it may fail to detect genuine miscalibration. Results should be interpreted alongside graphical calibration plots.

**Calibration Plots.** A calibration plot (reliability diagram) provides a visual assessment of calibration by plotting mean predicted probabilities against observed event rates within probability bins. Systematic deviation from the 45-degree line indicates miscalibration; the direction and magnitude of deviation should be interpreted in the context of the model's use case.

**Bias Diagnostics for Regression and Forecasting Models.** For models producing continuous outputs (e.g. loss estimates, cash flow projections, interest rate sensitivities), the validator should assess systematic bias using the mean error (ME) or mean percentage error (MPE). An ME that is consistently positive or negative across sub-periods or segments indicates directional bias. The validator should also assess whether bias is homogeneous across the output range or concentrated in particular segments, which may indicate non-linearity that the model fails to capture.

**Population Stability Index (PSI).** The PSI assesses whether the distribution of model inputs or scores has shifted materially between the development sample and the validation or current application sample. PSI values above 0.25 are conventionally taken to indicate significant population shift that may impair model performance. The validator should use PSI to contextualise performance findings — apparent deterioration in accuracy may reflect population shift rather than model failure, and vice versa.

---

#### 4.1.3 Predictive Accuracy and Goodness of Fit

For models producing continuous or multi-valued outputs, the validator should assess overall predictive accuracy using measures of fit that are appropriate to the model's output type and loss function.

**Mean Absolute Error (MAE) and Root Mean Squared Error (RMSE).** MAE measures the average magnitude of prediction errors without regard to direction; RMSE penalises larger errors more heavily due to the squaring of residuals. The choice between them should reflect the model's use case: RMSE is preferred where large errors are disproportionately costly; MAE is more robust to outliers. The validator should assess these metrics relative to the scale of the model's output and, where available, relative to development-phase performance or peer model benchmarks.

**Mean Absolute Percentage Error (MAPE) and Symmetric MAPE (sMAPE).** MAPE expresses prediction error as a percentage of the observed value, facilitating comparisons across models or time periods with different output scales. The validator should note that MAPE is undefined where observed values are zero and may be distorted where observed values are small. sMAPE addresses the asymmetry of MAPE but introduces its own interpretational caveats; the validator should document which measure is used and why.

**R-squared and Adjusted R-squared.** For regression-based models, R-squared measures the proportion of variance in the outcome explained by the model. The validator should treat R-squared as a supplementary, rather than primary, measure of performance, since it can be inflated by overfitting and does not assess calibration. Adjusted R-squared, which penalises for the number of predictors, is preferred for models with large feature sets.

**Log-Likelihood and Information Criteria.** Where the model is estimated via maximum likelihood, the log-likelihood value provides a basis for comparing nested model specifications. Information criteria (AIC, BIC) penalise model complexity and may be used to assess whether additional predictors contribute meaningfully to fit. These are most relevant to the review of model specification decisions made during development, rather than as standalone performance measures during validation.

---

#### 4.1.4 Residual Analysis

Residual analysis examines the pattern of prediction errors to identify systematic model weaknesses that aggregate accuracy statistics may conceal. Even where overall fit is acceptable, structured residual patterns can indicate model misspecification, omitted variables, or inappropriate functional form.

**Residual Distribution.** The validator should plot the distribution of residuals and assess whether they are approximately symmetric and centred near zero. Skewed or fat-tailed residual distributions may indicate non-linearity or the presence of influential observations. Where the model assumes normally distributed errors (e.g. OLS regression), formal normality tests (Shapiro-Wilk, Jarque-Bera) should be considered, with the caveat that these tests have limited power at small sample sizes and high power at large ones.

**Residuals Against Fitted Values.** A scatter plot of residuals against fitted values should exhibit no discernible pattern. Funnel shapes indicate heteroscedasticity (non-constant error variance); curved patterns suggest non-linearity. The Breusch-Pagan or White test may be used to formally test for heteroscedasticity.

**Residuals Over Time.** For time-series or panel models, the validator should plot residuals over time to identify serial correlation or structural breaks. The Durbin-Watson statistic or Ljung-Box test may be used to test for autocorrelation. Persistent residual patterns in particular time periods may indicate the model's failure to capture regime changes or cyclical dynamics.

**Residuals by Segment.** The validator should assess whether residuals are randomly distributed across key portfolio segments (e.g. product type, obligor size, geography, vintage). Systematic over- or under-prediction in specific segments indicates that the model may not generalise adequately across the portfolio and may introduce bias in downstream capital or risk calculations.

**Influential Observations and Outlier Analysis.** The validator should assess whether model performance is materially driven by a small number of influential observations, using Cook's Distance or leverage statistics for regression models. Where influential observations are identified, the validator should assess whether they reflect genuine portfolio characteristics or data quality issues, and whether their exclusion materially alters model performance.

---

#### 4.1.5 Applicability to Non-Quantitative and Hybrid Models

The statistical tests described in sections 4.1.1 to 4.1.4 are primarily designed for quantitative and statistical models. For expert-judgement-based models or hybrid models combining quantitative and qualitative components, the validator should apply these tests to the quantitative components where feasible, and document the rationale where they are inapplicable. In such cases, the performance assessment should place greater reliance on the methods described in Section 4.3 (Comparative Assessment) and Section 4.4 (Temporal Validation), supplemented by structured review of the expert judgement process and its outcomes.

### 4.2 Robustness and Sensitivity Testing

#### Purpose

Robustness and sensitivity testing examines how a model responds to variation in its inputs, parameters, and operating environment. Where statistical performance testing (Section 4.1) assesses how well a model fits historical data, robustness and sensitivity testing assesses whether that performance is stable and whether the model behaves in a manner that is theoretically coherent and practically reliable under a range of conditions. This distinction is important: a model may exhibit strong in-sample fit whilst remaining highly sensitive to minor input changes or fragile under conditions outside its development sample.

Robustness and sensitivity testing is applicable across all model types within scope of these instructions, including quantitative, statistical, and expert-judgement-based models. The validator shall select from the methods described below those that are proportionate to the model's materiality, complexity, and intended use. Where a test is considered inapplicable, the validation report shall record the rationale for its exclusion.

---

#### 4.2.1 Sensitivity Analysis

Sensitivity analysis quantifies the responsiveness of model outputs to changes in individual inputs or parameters, holding all other inputs constant. It serves two distinct purposes: first, to identify which inputs and parameters drive model outputs most materially, thereby informing the focus of validation effort; second, to assess whether the magnitude and direction of model responses are consistent with theoretical expectations and economic intuition.

**Single-Factor Sensitivity (One-at-a-Time Analysis).** The validator should vary each material model input individually across a defined range — typically spanning the observed historical distribution of that input, extended to plausible tail values — and record the resulting change in model output. The range selected should be documented and justified. Where the model's development documentation specifies expected sensitivities or elasticities, the validator should assess whether observed sensitivities are consistent with those expectations. Unexpected non-linearities, threshold effects, or discontinuities should be investigated and, where they cannot be explained by the model's design, reported as findings.

**Parameter Sensitivity.** Distinct from input sensitivity, parameter sensitivity testing varies the model's internally estimated or externally calibrated parameters — such as regression coefficients, decay rates, volatility estimates, or expert-assigned weights — to assess the stability of outputs to estimation uncertainty. This is particularly relevant where parameters have been calibrated on limited data, where estimation uncertainty is high, or where parameters are subject to periodic recalibration. The validator should assess whether plausible parameter perturbations, for example within one or two standard errors of the point estimate, produce material changes in model outputs.

**Interaction Effects.** Where model inputs are expected to interact — for instance, where the effect of one variable is conditional on the level of another — the validator should consider multi-factor sensitivity tests that vary two or more inputs simultaneously. This is particularly relevant for non-linear models, models incorporating interaction terms, and models in which inputs are known to be empirically correlated. The validator should document which interaction effects were tested and the basis for their selection.

**Directional Reasonableness.** Irrespective of the magnitude of sensitivities, the validator should assess whether model outputs respond in the expected direction to changes in each material input. A credit risk model that produces lower predicted default probabilities in response to deteriorating macroeconomic conditions, or a liquidity model that produces lower stress outflows in response to increasing liability concentrations, would represent a directional failure that statistical performance metrics alone may not detect. Directional reasonableness checks are applicable to all model types, including expert-judgement models, and should be documented explicitly in the validation report.

---

#### 4.2.2 Stress Testing and Scenario Analysis

Stress testing and scenario analysis assess model behaviour under conditions that depart significantly from the historical norm, including severe but plausible adverse scenarios and, where relevant, hypothetical extreme events. They complement single-factor sensitivity analysis by evaluating the model's response to coherent combinations of stressed inputs, rather than isolated perturbations.

The distinction between stress testing and scenario analysis as used in these instructions is as follows: stress testing applies severe values to individual or small groups of inputs without necessarily constructing a macroeconomically coherent narrative; scenario analysis constructs internally consistent sets of conditions — typically macroeconomic, financial, or operational — and evaluates model outputs under each scenario in its entirety.

**Stress Testing of Key Inputs.** The validator should identify the inputs to which the model is most sensitive (drawing on findings from Section 4.2.1) and apply stressed values representative of historical tail events or regulatory stress calibrations. Where the Bank participates in regulatory stress testing exercises (e.g. PRA concurrent stress tests), the validator should assess whether the model produces outputs that are consistent in direction and order of magnitude with the Bank's stress testing framework. Material inconsistencies should be investigated and reported.

**Macroeconomic Scenario Analysis.** For models whose outputs depend on macroeconomic variables — including credit risk models, liquidity models, and IRRBB models — the validator should assess model performance under a defined set of macroeconomic scenarios. At a minimum, these should include a baseline scenario and at least one severe adverse scenario. Scenarios may be drawn from the Bank's internal stress testing programme, from PRA-published scenarios, or constructed by the validator for validation purposes. The validator should assess whether model outputs under each scenario are directionally and quantitatively plausible, and whether the model captures the key transmission mechanisms relevant to the scenario narrative.

**Operational and Structural Stress.** For models used in operational risk, financial crime, or fraud prevention contexts, scenario analysis should consider operational stress conditions such as material increases in transaction volumes, changes in customer behaviour, or shifts in the fraud typology landscape. The validator should assess whether the model remains fit for purpose under such conditions or whether its performance may deteriorate in ways that are not captured by historical testing.

**Reverse Stress Testing.** Reverse stress testing identifies the combination of input conditions under which model outputs reach a defined critical threshold — for example, the conditions under which a credit model produces predicted default rates materially in excess of the Bank's capital buffer, or under which a liquidity model indicates a breach of regulatory minimum requirements. The validator should consider whether reverse stress testing is appropriate for high-materiality models and, where conducted, document the identified threshold conditions and assess their plausibility relative to the Bank's risk appetite.

---

#### 4.2.3 Stability Testing

Stability testing assesses whether model performance is consistent across different subsamples, time periods, and population segments, or whether it is contingent on the specific characteristics of the development sample. A model that performs well in aggregate but exhibits material instability across segments or time periods may produce unreliable outputs when applied to portfolios or periods outside the development window.

**Out-of-Sample and Out-of-Time Testing.** The validator should assess whether model performance on a holdout sample — data excluded from the development and parameter estimation process — is materially consistent with in-sample performance. Where the model developer has not provided a holdout validation, the validator should consider constructing one from available data, subject to sample size constraints. Out-of-time testing, which uses data from a time period subsequent to the development window, is particularly important for detecting overfitting and assessing whether the model's predictive relationships have remained stable over time.

**Sub-sample Stability.** The validator should assess whether model performance is consistent across material portfolio segments, including product type, obligor type, geographic concentration, origination vintage, and any other segmentation considered relevant to the model's intended use. Material performance differences across segments should be investigated to determine whether they reflect genuine heterogeneity in the underlying population or model deficiencies.

**Temporal Stability of Parameters.** For models estimated on time-series or panel data, the validator should assess whether key parameter estimates are stable over time using rolling or recursive estimation where the sample size permits. The Chow test or similar structural break tests may be used to assess whether parameter instability at identified breakpoints is statistically significant. Where structural breaks coincide with known macroeconomic or regulatory events, the validator should assess whether the model adequately captures post-break dynamics.

**Stability Under Data Perturbation.** The validator should consider testing model stability by introducing small, controlled perturbations to the input data — for example, minor adjustments to individual observations or small amounts of synthetic noise — and assessing whether model outputs remain broadly stable. Material output changes in response to minor input perturbations may indicate overfitting, excessive complexity, or inappropriate reliance on a small number of observations.

---

#### 4.2.4 Boundary and Constraint Testing

Boundary and constraint testing assesses model behaviour at the limits of its input domain, including extreme values, edge cases, and conditions outside the range of the development data. This category of testing is often overlooked in validation but is particularly important for models deployed in automated decisioning or limit-setting contexts, where boundary conditions may be encountered in practice without human oversight.

**Input Domain Boundary Testing.** The validator should test model outputs at the extreme values of each material input — including the minimum and maximum values observed in the historical data, as well as values beyond the observed range — and assess whether outputs remain within theoretically plausible bounds. Models should not produce nonsensical outputs (e.g. negative probabilities, implausible cash flow estimates) at boundary values. Where outputs deteriorate at the extremes, the model documentation should specify the valid input range and the model's governance framework should include controls to prevent out-of-range inputs from being processed.

**Missing Data and Default Value Handling.** The validator should assess how the model handles missing or incomplete input data, including whether default or fallback values are applied and whether such values are documented and appropriate. Inappropriate default value choices can introduce systematic bias, particularly for models used across diverse portfolios where input completeness varies materially.

**Constraint Compliance.** Where the model incorporates structural constraints — such as non-negativity of predicted values, monotonicity requirements, or regulatory floors — the validator should verify that these constraints are consistently enforced across the full input domain. Constraint violations at boundary values may not be apparent from aggregate performance statistics but can generate material errors in specific applications.

---

#### 4.2.5 Applicability to Expert-Judgement and Hybrid Models

For expert-judgement models and hybrid models, many of the quantitative tests described above require adaptation. The validator should nonetheless apply the conceptual principles of robustness and sensitivity testing in a manner appropriate to the model type.

For expert-judgement models, sensitivity analysis should assess the degree to which overall model outputs are sensitive to the judgements applied in individual components — for example, by varying expert-assigned scores or weights across their plausible ranges and assessing the resulting output distribution. This is particularly relevant where the model incorporates a small number of high-weight judgement factors, since concentration in a few subjective inputs introduces model risk analogous to parameter instability in quantitative models.

For hybrid models, the validator should assess the sensitivity of overall outputs to the relative weighting between quantitative and qualitative components, and consider whether the model's governance framework provides adequate controls over adjustments to the qualitative overlay.

