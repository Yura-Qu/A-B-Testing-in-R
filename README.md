# A/B Testing in R

![image](https://github.com/Yura-Qu/A-B-Testing-in-R/assets/143141778/3290d36a-46fa-49d2-b2d7-58e451c2f954)

Summary: ![B Testing ](https://github.com/Yura-Qu/A-B-Testing-in-R/assets/143141778/734e2fa0-fc81-4be0-8e1d-05a5b07b49ac)


## Introduction 
AB testing is a statistical technique to make data-driven decisions by testing differences and relationships of data collected in a controlled AB design experiment.

> Example: The time a group takes to eat Cheese versus Pepperoni pizza. 
1. We need to use a **between-subjects design**[^1], where each participant contributes data to a single group.
2. We end up with two **randomly** selected groups to eat Cheese or Pepperoni pizza, group A or B.
3. Ideally, the AB design will have a **control group**, cheese, to compare the experimental group, pepperoni.
4. **Wide format V.S. Long format**
   1. Wide Format: In a wide-format dataset, variables are typically stored in columns, with each variable having its own column. This format can be convenient for certain analyses, especially when dealing with fewer observations and a larger number of variables. Each row represents a unique observation or case.
   2. Long Format: Long-format data involves organizing data in a way where each row represents a single observation or data point, often with multiple rows per subject or case. Variables are typically stored in two columns: one column for variable names and another for their respective values. This format is advantageous for certain types of analyses, such as statistical modeling and some types of visualizations. **prefered format**
       ![image](https://github.com/Yura-Qu/A-B-Testing-in-R/assets/143141778/313e1bf2-5795-4d49-b9a0-52719c0c9560)

5.  A/B Test Hypothesis
      - Compare measures between group -- the difference between conditions 
      - Relationship of measure in groups -- Trend between measures

   ```r
   library(tidyr)
   # Assuming 'wide_data' is your wide-format dataset
   long_data <- wide_data %>%
   pivot_longer(cols = -id,       # Columns to pivot (excluding the 'id' column)
               names_to = "variable_name",   # Name for the column containing previous column names
               values_to = "value_name")    # Name for the column containing previous values

   # 'id' here refers to the unique identifier column that remains unchanged in the transformation
   ```
   ```r
   # Load the libraries needed
    library(tidyr)
    library(ggplot2)

    # Transform the data from wide to long
    longabsent <- absenteeism %>% 
      pivot_longer(
        cols = c("Drinker.yes", "Drinker.no"),
        names_to = "Group",
        values_to = "Absence") %>%
      na.omit()

    # Create a histogram of each group
    ggplot(longabsent,
    aes( x=Absence, fill = Group)) + 
     geom_histogram() + 
     facet_grid(Group~.)
  ```

[^1]: "Between-subjects design" refers to a research methodology in which different groups of subjects are exposed to different conditions or treatments. This approach allows for comparisons between the groups to assess the impact of these varying conditions on the subjects' outcomes or behaviors.

## A/B Testing Consideration 

1. **Data fluctuation**
   - A statistical representation of the whole population is needed to estimate sample results accurately.
   - Data fluctuation refers to the **variation** or changes observed in a dataset over time or across different conditions. It represents the variability or movement in the values of the data points, indicating how they differ from one measurement to another.
   - Fluctuations in external factors can impact results:
     - **Temporal Changes**: Variations observed over time due to seasonal patterns, trends, or cyclical behavior.
     - **Random Variability**: Natural variations or randomness within the data points.
     - **External Factors**: Changes caused by external influences such as market dynamics, environmental conditions, or human behavior.
     - **Experimental Variability**: Variations resulting from experimental conditions or settings.
2. **Number of variables**:
   - **In A/B testing, ensuring accuracy often involves isolating and assessing individual variables to draw clear and actionable conclusions.**
     > - Scenario:
     >   - One-Topping Variable: Compares individual toppings (e.g., pepperoni vs. cheese).
     >   - Two-Topping Variable: Evaluates topping pairings (e.g., bell pepper and onion vs. olive and garlic), lacking a control group for comparison.
   - **Importance of Isolating Variables:**
     >   1. **Clarity in Causation**: Testing individual toppings allows for clear causation identification. For instance, determining if pepperoni positively impacts enjoyment compared to cheese.
     >   2. **Complexity of Pairings**: Assessing pairings introduces complexity, making it harder to attribute changes in enjoyment solely to one topping. It's challenging to discern if a higher enjoyment score is due to the specific topping or the combination.
     >   3. **Control Group Absence**: Without a control group, there's no baseline for comparison. This absence can make it difficult to isolate the impact of the toppings on enjoyment.

   - **When conducting multiple tests or comparisons involving various variables, there's a critical consideration regarding Type I error[^2]**

     Type I Error and Multiple Comparisons:
     >  1. Increased Analyses, Increased Risk: **As the number of variables or tests increases, conducting multiple analyses escalates the chance of encountering a Type I error.**
     >  2. Significance Level: A common threshold is a significance level of 5% (α = 0.05). This implies a 5% probability of incorrectly rejecting the null hypothesis when it's actually true.
     >  3. Family-Wise Error Rate (FWER): It calculates the collective likelihood of encountering at least one Type I error across all tests.
     >  4. Confidence Level: Derived from the significance level (α). For instance, if α = 0.05, then the confidence level is 95%.

     Calculating Family-Wise Error Rate:
     >  Significance level of 5% (α = 0.05 = probability of false positives).
     > 
     >  The confidence level is 95%.
     > 
     >  The probability of no false positives per test is (1 - α) = 0.95.
     > 
     >  To find the chance of no false positives across all ten tests, raise 0.95 to the power of 10: $(0.95^{10}) = 0.60$. Subtract this from 1 to get the probability of encountering one or more false positives, which is around 0.40 or 40%.
     > ```r
     > 1-((1-0.05)^10)
     > [1] 0.4012631
     > 1-((1-0.05)^100)
     > [1] 0.9940795
     > ```
     > - If we run 10 tests, the probability of having at least 1 false-positive = 40%
     > - If we run 100 tests, the probability of having at least 1 false-positive = 99%
     [^2]:Type I error refers to a statistical error that occurs when a null hypothesis is incorrectly rejected. In hypothesis testing, it signifies the rejection of a true null hypothesis, indicating an effect or relationship when there isn't one in reality. This error is associated with the significance level (often denoted as alpha), typically set at 5% or 0.05 in many statistical analyses.
3. **Regression to the mean**
   Regression to the mean refers to an observation that extreme initial measurements of a variable tend to move closer to the average upon subsequent measurements.
   - **Impact on Type I Error:** When analyzing changes or interventions, extreme results in initial measurements might not represent the true effect. This shift towards the mean can lead to misinterpretation if not considered properly, resulting in a Type I error.
     > **Example - Submit Button Size Change:** If a website changes the size of a submit button, initially, it might attract more attention from visitors. However, over time, this novelty effect might diminish, causing the attention to regress to the average level. If this change is not considered for a sufficient duration during data collection, it can lead to a Type I error.
     >  ![image](https://github.com/Yura-Qu/A-B-Testing-in-R/assets/143141778/98b12289-d253-476c-b371-db420f89b709)
   - **Importance of Proper Time for Data Collection:** Allowing enough time post-intervention or change is crucial. It's essential to ensure that measurements are taken when the novelty or immediate effects have stabilized, providing a more accurate assessment.
4. **Role of Control Group and Sample Size:**
   - Control Group Importance: In AB testing, having a control group helps in benchmarking against the original state, aiding in assessing whether observed changes are due to the intervention or merely natural variations.
     > In the previous example of the Submit button size change, having a control group (original submit button size) helps to understand the impact of submit button size on the number of submission
     >  ![image](https://github.com/Yura-Qu/A-B-Testing-in-R/assets/143141778/f6ed06a4-41d5-48b1-8727-f368ffde1c0c)
   - Small Sample Sizes' Impact: Limited data may not accurately represent the true mean or characteristics. Larger datasets tend to provide a more accurate depiction of the actual underlying patterns. For instance, in the assessment of pizza enjoyment, one individual's dislike might skew initial data, but as more opinions are gathered, the mean becomes more representative.

## Power and sample size 

### Power 

> In statistics, "power" refers to the probability that a statistical test will correctly reject a false null hypothesis. It's the ability of a statistical test to detect an effect, given that the effect genuinely exists in the population. For example, the probability that there is a difference in enjoyment of the Cheese and Pepperoni pizza, given we found a significant difference. Ideally, an experiment has a **small probability of a type II error[^3] and large power**. Having high power means the test is more likely to correctly reject a false null hypothesis, thus avoiding a Type II error.
[^3]:A Type II error, in statistics, occurs when you fail to reject a false null hypothesis. In other words, it's a situation where a test concludes that there is no effect or difference when, in fact, there is one present in the population.

 **Power benefits**
 1. **Determining Test Usefulness**: Power helps determine if a test is capable of detecting an effect when it genuinely exists. Assessing power before conducting a study allows researchers to estimate the required sample size. Without enough subjects, the analysis might lack the statistical support to detect an effect even if it truly exists. This avoids wasting resources on experiments that might not yield meaningful results.
 2. **Sample Size Estimation:** By understanding the power of a test, researchers can estimate the sample size needed to achieve a desired level of power. This informs the planning phase before data collection, ensuring the study has adequate statistical strength to detect the effect of interest.
 3. **Evaluating Trust in Results:** After conducting a test, calculating its achieved power helps in interpreting the reliability of the results. If the power is low, it suggests that even if the null hypothesis was false, the test might not have been able to detect it. Conversely, high power increases confidence in the results, indicating a greater likelihood that the test accurately captured the true effect.

### Sample size 

- Effect Size: This refers to the magnitude of the difference or the strength of the relationship between variables in a statistical test. A larger effect size represents a more noticeable or significant difference between groups or variables. = mean of the control group - mean of the experimental group
  - Before to analysis, the effect size can be determined by
    - Background information
    - Preliminary data
 - After the analysis, the effect can be determined by
   - Full dataset 
- Sample Size Impact: Smaller sample sizes tend to yield narrower distributions and require a relatively larger test statistic to reject the null hypothesis. With a smaller sample, the test might lack the sensitivity to detect smaller effects, leading to reduced power.
- Power: Power represents the probability of correctly rejecting a false null hypothesis. Higher power implies a greater ability to detect a true effect if it exists. To determine sample size, researchers often aim for a high power, commonly around 0.80, which allows for a better chance of detecting genuine effects.
- Determining Sample Size: The pwr package in statistics assists in calculating the necessary sample size for an experiment. Inputting values such as the estimated effect size (d), desired power level (power), and significance level (alpha) allows researchers to determine the sample size required for a given study design and hypothesis test.
  ```r
  # Load the pwr package (install it if you haven't already)
  install.packages("pwr")
  library(pwr)

  # Set parameters for the t-test
  effect_size <- 0.5  # Set the effect size (Cohen's d)
  power <- 0.8        # Set the desired power level
  alpha <- 0.05       # Set the significance level

  # Calculate the sample size needed
  result <- pwr.t.test(d = effect_size, power = power, sig.level = alpha, type = "two.sample")

  # View the result
  print(result)
  ```
### Power analysis 

> A power analysis of a test aids in determining if the results of the experiment can be trusted. A test with higher power denotes a higher probability of correctly rejecting the null hypothesis.
> 1. Sample size = 20
> 2. Effect size = 0.81
> 3. Alpha = 0.45
>    ```r
>    install.packages("pwr")
>    library(pwr)
>
>    pwr.t.test(
>    n = 20,
>    d = 0.81,
>    sig.level = 0.045,
>    type = "one.sample")
>    ```
>    ![image](https://github.com/Yura-Qu/A-B-Testing-in-R/assets/143141778/7bae9678-46f5-4c24-8a8b-3cf2ebaf8511)
>    - This means that with these parameters, the test has a high likelihood (92.23%) of correctly rejecting the null hypothesis when it's false (i.e., detecting a true effect), indicating a strong ability to detect a difference if one truly exists in the population.
>    - The two-sided alternative hypothesis suggests the test is assessing if the sample mean significantly differs from the hypothesized population mean in either direction.
  
