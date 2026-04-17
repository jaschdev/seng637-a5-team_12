**SENG 637- Dependability and Reliability of Software Systems**

**Lab. Report \#5 – Software Reliability Assessment**

| Group \#: 12     |
| ---------------- |
| Student Names:   |
| Jason Chiu       |
| William Watson   |
| Jack Shenfield   |
| Barrett Sapunjis |

# Introduction
This lab focuses on evaluating the reliability of a system using failure data collected during integration testing. Understanding system reliability is important for determining when a product is ready to be deployed and meets performance requirements. Instead of guessing or assuming, reliability analysis uses statistical methods to prove a system’s performance over time.

In this assignment, two approaches are utilised: reliability growth testing and Reliability Demonstration Chart (RDC) analysis. The former, Reliability growth testing, is used to determine how a system may perform while failures are identified and fixed, which provides insight into metrics such as failure rate and mean time to failure (MTTF) as well as predictive capabilities. The latter, RDC, is used to determine if a system meets a specified reliability target for a given confidence level.

Using the tools and dataset provided, this lab aims to analyze system reliability from both perspectives. 

# Part 1 - Assessment Using Reliability Growth Testing 
## 1.1 Introduction
In the first part of this lab, using provided data, we analyzed the  reliability of a software and its failure rate over time to determine appropriate models for representing and predicting failures of the system.

## 1.2 Model Comparison
To identify the most suitable reliability models for the given failure dataset, several candidate models were evaluated using multiple fit and prediction metrics, including Log-likelihood, AIC, BIC, SSE, and PSSE as they were provided in the model comparison tool in C-SFRAT.

Log-likelihood measures how well a model explains the observed data where higher values (i.e., less negative) indicate a better fit. AIC (Akaike Information Criterion) and BIC (Bayesian Information Criterion) both evaluate model quality while penalizing complexity, with lower values indicating a better balance between fit and simplicity. SSE (Sum of Squared Errors) quantifies the total deviation between the model’s predictions and the observed data, while PSSE (Predictive Sum of Squared Errors) measures how well the model predicts unseen data. For both, lower values indicate better performance.
Based on these metrics, the DW3 F (Discrete Weibull Type 3) model was identified as the best-performing model, as it achieved the lowest AIC, BIC, SSE, and PSSE, along with the highest Log-likelihood among all candidates. The IFRGSB F (Increasing Failure Rate Generalized Salvia & Bollinger) model ranked second, consistently showing near next-best values across most evaluation metrics. This can be seen in the table below and while GM (Geometric) (F) has many metrics in 2nd, IFRGSB (F) doesn’t come far behind. GM (F) also has a notably high PSSE makes it less reliable, and as a Geometric model is too simple and is less realistic due to its basis as a constant failure rate model.

**Table 1:** Highest Ranked Models by AIC metric
| Model  | Covariates | Log-likelihood | AIC     | BIC     | SSE     | PSSE     |
|--------|------------|----------------|---------|---------|---------|----------|
| DW3    | F          | -57.1          | 122.199 | 127.935 | 528.046 | 37.071   |
| GM     | F          | -59.662        | 125.323 | 129.625 | 759.655 | 1320.009 |
| IFRGSB | F          | -59.155        | 126.31  | 132.046 | 756.46  | 105.937  |
| GM     | F, C       | -59.653        | 127.306 | 133.042 | 745.938 | 81.079   |
| GM     | E, F       | -59.661        | 127.322 | 133.058 | 755.156 | 102.777  |
| S      | F          | -59.662        | 127.323 | 133.059 | 759.17  | 147.809  |
| TL     | F          | -59.662        | 127.323 | 133.059 | 759.655 | 26.42    |

The strong performance of these models can be attributed to their ability to capture reliability growth behavior effectively. The DW3 model, based on the discrete Weibull distribution, is flexible in modeling different failure rate trends, making it well-suited for datasets where failure behavior evolves over time. The IFRGSB model, which assumes an increasing failure rate structure, is effective in representing systems where failure detection improves as testing progresses, a common characteristic in reliability growth scenarios.

In addition, the use of the F covariate (failure identification work measured in person-hours) contributed to the superior performance of these models. Compared to other covariates such as execution time (E) and computer time (C), the F covariate more directly reflects the actual effort spent by testers in identifying and resolving failures. Since reliability growth is closely tied to debugging and fault removal activities, person-hours provide a more meaningful measure of testing progress than raw execution or computer time. Furthermore, combining multiple covariates (e.g., E, C, or their combinations) can introduce additional complexity without necessarily improving model fit, especially if those variables are less correlated with failure detection. As a result, models using only the F covariate achieved better fit and prediction performance, as reflected in the evaluation metrics.

In addition to the numerical evaluation, visual inspection of the model fits further supports this selection. The reliability and failure intensity plots generated by the DW3 (F) and IFRGSB (F) models most closely follow the shape of the original stepwise failure data. Compared to other models, their curves align more closely with the observed failure pattern, indicating a better representation of the underlying system behavior.

## 1.3 Range Analysis
Range analysis was performed by gradually increasing the number of failure data points used in the model fitting (from 0 to X (max of 31)). It was observed that in the early stages of the dataset, the evaluation metrics (AIC, BIC, SSE) showed higher variability and the model rankings were less consistent, indicating insufficient data for stable estimation. However as more data points were included, the metrics stabilized and the same top-performing models, as mentioned in the previous section (DW3 (F), IFRGSB (F)), consistently appeared. Additionally, the reliability and failure rate curves became smoother and more representative of reliability growth. Therefore, the later portion of the dataset (e.g., after the first 25 intervals where model rankings changed mininally) was considered more suitable for analysis, as it provides more reliable and stable results for model selection.

## 1.4 Failure Rate and Reliability Plots
The first plot, the MVF graph, shows the cumulative number of failures over intervals of time where it can be seen the match between the models DW3 and IFRGSB show their alignment with some future predictions also mapped.

<img src="media/MVF graph.png" alt="media/MVF graph.png"/>

**Figure 1:** DW3 and IFRGSB models as shown on the MVF Graph for 31 intervals with predicted intervals

The second plot are the same models as they are mapped on the intensity graph showing failures at each interval.

<img src="media/Intensity graph.png" alt="media/Intensity graph.png"/>

**Figure 2:** DW3 and IFRGSB models as shown on the Intensity Graph for 31 intervals with predicted intervals

## 1.5 Target Failure Rate Decision Making
For target failure rate criteria, the plots and models generated for a set of reliability data are useful for understanding how software reliability evolves during testing. In this context, reliability refers to the likelihood that the system does not reveal additional failures under a given amount of testing effort, rather than random failure in operational use. The MVF shows the cumulative number of failures detected over time, while the failure intensity reflects the rate at which new failures are being discovered. A decreasing intensity and flattening MVF curve indicate improving reliability. Additionally, different covariates such as execution time (E), computer time (C), and failure identification effort (F) represent different ways of measuring testing progress. In particular, F is often more strongly correlated with reliability growth because it captures the human effort involved in identifying and recording failures, rather than only execution duration. These plots therefore support decision making by showing whether the system is approaching a stable failure rate that meets the target reliability threshold.

## 1.6 Advantages and Disadvantages of Reliability Growth Analysis
One advantage of reliability growth analysis is that it provides insight into how different types of testing effort (e.g., execution time (E), failure identification effort (F), and computer time (C)) influence failure detection. This helps identify which activities contribute most to reliability improvement and where testing resources should be focused. Additionally, reliability growth models can be used to estimate future failure trends, allowing teams to predict reliability growth over time and make informed decisions about required testing effort, schedule, and resource allocation.

One disadvantage of reliability growth analysis is the difficulty in determining whether a system has reached an acceptable reliability threshold. While plots such as the MVF or failure intensity curve may show a flattening trend, this does not necessarily guarantee that the failure rate has dropped below a required target. Furthermore, the accuracy of the analysis depends heavily on the quantity and quality of failure data available, and insufficient or noisy data can lead to unreliable model fitting and misleading conclusions.

# Part 2 - Assessment Using Reliability Demonstration Chart 

## 2.1 Introduction
Using the provided failure data and RDC Excel tool, we were able to determine the MTTF for the system. 

## 2.2 Data Preparation and Unit Conversion
The failure data provided was in interval form, with the number of failures corresponding to that interval. For compatibility with the software tool, we had to convert these values to cumulative time and add a separate row for each failure. Once this was complete, the data could be added into the plotting tool.

Additionally, the units of the failure data needed to be set to properly calculate the normalized time to failure. In our case, the data was in hours, so the units selected were failures per one hour. The risk acceptance and tolerance values were left at their default and recommended values of: 0.1 (alpha), 0.1 (beta), and 2.0 (gamma).

## 2.3 RDC Plots
The minMTTF, defined as the lowest number of failures per unit time that the system is trending into the accept region, is plotted below. This was determined through trial-and-error by manually adjusting the acceptable failure value, which for the data provided, is 12.4 failures per hour. Therefore, the minMTTF is 1/12.4 = 0.0816 hours.

<img src="media/RDC plot1.png" alt="media/RDC plot1.png" />

**Figure 3:** RDC at minMTTF of 0.0816 hours (12.4 failures per hour)

If we double the minMTTF from 0.0806 hours to 0.1613 hours, we get the following RDC plot. As shown, with this stricter failure tolerance, the system must be rejected.

<img src="media/RDC plot2.png" alt="media/RDC plot2.png"/>

**Figure 4:** RDC at 2x the minMTTF of 0.1613 hours (6.2 failures per hour)

Lastly, if we half the min MTTF from 0.0816 to 0.0408, we get the following plot. By this applied standard the system is easily accepted.

<img src="media/RDC plot3.png" alt="media/RDC plot3.png"/>

**Figure 5:** RDC at 0.5x the minMTTF of 0.0408 hours (24.8 failures per hour)

The RDC results show that at minMTTF, the system lies on the boundary of acceptability, indicating marginal compliance with the reliability requirement. When the MTTF is doubled (stricter requirement), the system falls into the rejection region, demonstrating that it does not yet meet higher reliability standards. Conversely, when the MTTF is halved (less strict requirement), the system is clearly within the acceptance region, indicating that it satisfies lower reliability expectations.

This confirms that the system is sensitive to the chosen reliability target and is currently operating near the threshold of acceptability.

## 2.4 Advantages and Disadvantages of RDC
One main advantage of RDC is that it provides an explicit quantitative framework for testing. Instead of estimating when sufficient testing has been completed, the RDC directly links test time, number of failures allowed, and confidence level. This makes it easier to justify testing plans and results to external stakeholders.

However, RDC is not a perfect system. One major disadvantage is that it assumes constant failure rates (i.e. exponential distribution) which may not always reflect real life systems. This may cause the results and conclusions relating to RDC analysis to not be fully relevant to real-world system behaviour.

Another limitation of RDC is that its purpose is primarily for demonstration. RDC will not be able to help a tester figure out what is wrong with the code or how to fix it, just how it is currently performing.

# Comparison of Results
The results from reliability growth testing and RDC analysis provide complementary but distinct perspectives on system reliability. The reliability growth models (DW3 F and IFRGSB F) indicate that the system is experiencing steady improvement over time, as evidenced by the decreasing failure intensity and the gradual flattening of the MVF curve. These trends suggest that faults are being effectively identified and removed, and that continued testing and debugging are likely to further improve system reliability.

However, when evaluated using RDC with a defined reliability target, a different perspective emerges. For example, when an acceptable failure rate of 6.2 failures per hour (corresponding to a higher MTTF requirement) is used as the threshold, the system does not meet the acceptance criteria and falls within the rejection region. This indicates that, despite observable reliability growth, the system has not yet achieved the level of reliability required under stricter operational expectations.

By combining insights from both methods, a more complete understanding can be obtained. The reliability growth models suggest that with additional testing effort, potentially as little as a few more testing intervals, the failure rate could decrease significantly, possibly approaching half of its current value. Such an improvement would meaningfully alter the trend of failure points on the RDC, potentially shifting the system from the rejection region into the acceptance region. This demonstrates that the system is not fundamentally unreliable, but rather still in a transitional phase of reliability growth.

Conversely, if the system were already close to or within the acceptance region on the RDC, the growth models could provide confidence that continued testing would further stabilize the system and increase the margin of safety. Therefore, reliability growth analysis is particularly useful for predicting future reliability and guiding testing effort, while RDC serves as a stricter, decision-oriented tool for determining whether the system currently satisfies predefined reliability requirements.

Overall, the combined results indicate that the system is improving and trending toward acceptability but has not yet reached a level of reliability sufficient to confidently meet more demanding criteria. This highlights the importance of using both techniques together: one to understand and predict reliability growth, and the other to enforce objective acceptance thresholds.

# Discussion on Similarity and Differences of the Two Techniques
Both reliability growth testing and Reliability Demonstration Chart (RDC) analysis are used to evaluate software reliability based on observed failure data collected during testing. In both approaches, failure occurrences over time serve as the primary input, and statistical methods are applied to interpret how the system behaves under testing conditions. As a result, both techniques provide quantitative measures of reliability and help support engineering decisions regarding system readiness. Additionally, neither method requires knowledge of the system’s internal functionality, making them broadly applicable to a wide range of software systems.

Despite these similarities, the two techniques differ significantly in their purpose, methodology, and type of insight they provide. Reliability growth testing is focused on modeling how reliability evolves over time as faults are detected and corrected. It provides continuous insight into system behavior, allowing engineers to observe trends such as decreasing failure intensity and to predict future reliability if testing continues. This makes it particularly valuable for planning, resource allocation, and determining how much additional testing effort may be required.

In contrast, RDC is a discrete, decision-oriented technique used to determine whether a system meets a predefined reliability requirement at a specified confidence level. Rather than modeling trends, it evaluates whether observed failure data falls within acceptance or rejection regions based on statistical boundaries. As a result, RDC is more suitable for certification, release decisions, and validating whether contractual or operational reliability targets have been met.

Another key difference is that reliability growth analysis accounts for the dynamic nature of testing and debugging, whereas RDC typically assumes a constant failure rate and does not explicitly model reliability improvement over time. This means that RDC may reject a system that is still improving, even if it is close to meeting the required reliability threshold.

In summary, reliability growth testing provides a forward-looking, predictive view of system reliability, while RDC provides a strict, threshold-based assessment of whether the system is currently acceptable. Used together, these techniques offer a more comprehensive evaluation: one explains how the system is improving, and the other determines whether it is ready for deployment.

# How the team work/effort was divided and managed
The group decided to split the team into two pairs in order to complete the RDC and C-SFRAT testing. Once each side was complete, the opposing pair reviewed and double checked each others work. The team then worked in parallel on the report via a discord call to wrap up the assignment.

# Difficulties encountered, challenges overcome, and lessons learned
With Part 1, the C-SFRAT tool appeared very foreign and understanding the different parts of the tool and what was happening when using it took some time. With Part 2, there were initial issues with the RDC spreadsheet provided. The tabs are locked limiting editing capabilities, and it was not clear how to unlock them as this was required to actually use the tool to get the results needed for this lab. Overall, more detail on these aspects of both parts would have been helpful before using them as more time was spent reading/trying to figure out the tools than time spent actually producing the results.

# Comments/feedback on the lab itself
The lab was a good exercise for understanding the practical differences between C-SFRAT and RDC charts, especially seeing how one focuses on trend prediction while the other is more of a pass/fail check.