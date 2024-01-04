# A/B Testing in R

![image](https://github.com/Yura-Qu/A-B-Testing-in-R/assets/143141778/3290d36a-46fa-49d2-b2d7-58e451c2f954)

## Introduction 
AB testing is a statistical technique to make data-driven decisions by testing differences and relationships of data collected in a controlled AB design experiment.

> Example: The time a group takes to eat Cheese versus Pepperoni pizza. 
1. We need to use a **between-subjects design**[^1], where each participant contributes data to a single group.
2. We end up with two **randomly** selected groups to eat Cheese or Pepperoni pizza, group A or B.
3. Ideally, the AB design will have a **control group**, cheese, to compare the experimental group, pepperoni.
4. **Wide format V.S. Long format**
   1. Wide Format: In a wide-format dataset, variables are typically stored in columns, with each variable having its own column. This format can be convenient for certain analyses, especially when dealing with fewer observations and a larger number of variables. Each row represents a unique observation or case.
   2. Long Format: Long-format data involves organizing data in a way where each row represents a single observation or data point, often with multiple rows per subject or case. Variables are typically stored in two columns: one column for variable names and another for their respective values. This format is advantageous for certain types of analyses, such as statistical modeling and some types of visualizations. **prefered format**
5.  A/B Test Hypothesis
      - Compare measures between group -- the difference between conditions 
      - Relationship of measure in groups -- Trend between measures
 ![image](https://github.com/Yura-Qu/A-B-Testing-in-R/assets/143141778/313e1bf2-5795-4d49-b9a0-52719c0c9560)

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
     [^2]:Type I error refers to a statistical error that occurs when a null hypothesis is incorrectly rejected. In hypothesis testing, it signifies the rejection of a true null hypothesis, indicating an effect or relationship when there isn't one in reality. This error is associated with the significance level (often denoted as alpha), typically set at 5% or 0.05 in many statistical analyses.
3. **Regression to the mean**
   Regression to the mean refers to an observation that extreme initial measurements of a variable tend to move closer to the average upon subsequent measurements.
   - **Impact on Type I Error:** When analyzing changes or interventions, extreme results in initial measurements might not represent the true effect. This shift towards the mean can lead to misinterpretation if not considered properly, resulting in a Type I error.
     > **Example - Submit Button Size Change:** If a website changes the size of a submit button, initially, it might attract more attention from visitors. However, over time, this novelty effect might diminish, causing the attention to regress to the average level. If this change is not considered for a sufficient duration during data collection, it can lead to a Type I error.
     >  ![image](https://github.com/Yura-Qu/A-B-Testing-in-R/assets/143141778/98b12289-d253-476c-b371-db420f89b709)
   - **Importance of Proper Time for Data Collection:** Allowing enough time post-intervention or change is crucial. It's essential to ensure that measurements are taken when the novelty or immediate effects have stabilized, providing a more accurate assessment.
4. **Role of Control Group and Sample Size:**
   - Control Group Importance: In AB testing, having a control group helps in benchmarking against the original state, aiding in assessing whether observed changes are due to the intervention or merely natural variations.
     - In the previous example of the Submit button size change, having a control group (original submit button size) helps to understand the impact of submit button size on the number of submission
       ![image](https://github.com/Yura-Qu/A-B-Testing-in-R/assets/143141778/f6ed06a4-41d5-48b1-8727-f368ffde1c0c)
   - Small Sample Sizes' Impact: Limited data may not accurately represent the true mean or characteristics. Larger datasets tend to provide a more accurate depiction of the actual underlying patterns. For instance, in the assessment of pizza enjoyment, one individual's dislike might skew initial data, but as more opinions are gathered, the mean becomes more representative.

 
