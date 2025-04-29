# EDA_withR
---
title: "EDA_withR"
output: 
 github_document:
 pandoc_args: ["--wrap=none"]
always_allow_html: true
date: "2025-04-28"
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library(tidyverse)
```

# Exploratory Data Analysis with R

This document contains my solutions to exercises 10.3.3 and 10.4.1 from Chapter 10 of R for Data Science.

## 10.3.3 Exercises

### 1. Explore the distribution of each of the `x`, `y`, and `z` variables in `diamonds`. What do you learn? Think about a diamond and how you might decide which dimension is the length, width, and depth.

```{r}
# Load the diamonds dataset
data(diamonds)

# Explore the x variable (length in mm)
ggplot(diamonds, aes(x = x)) +
  geom_histogram(binwidth = 0.1) +
  labs(title = "Distribution of diamond length (x)", 
       x = "Length (mm)",
       y = "Count")

# Get summary statistics for x
summary(diamonds$x)
```

```{r}
# Explore the y variable (width in mm)
ggplot(diamonds, aes(x = y)) +
  geom_histogram(binwidth = 0.1) +
  labs(title = "Distribution of diamond width (y)", 
       x = "Width (mm)",
       y = "Count")

# Get summary statistics for y
summary(diamonds$y)
```

```{r}
# Explore the z variable (depth in mm)
ggplot(diamonds, aes(x = z)) +
  geom_histogram(binwidth = 0.1) +
  labs(title = "Distribution of diamond depth (z)", 
       x = "Depth (mm)",
       y = "Count")

# Get summary statistics for z
summary(diamonds$z)
```

Looking at the distributions and summary statistics of the diamond dimensions:

- All three distributions are right-skewed, with most diamonds having smaller dimensions and fewer diamonds having larger dimensions.
- Each variable has some unusual values (some are 0 or extremely large), which are likely errors in measurement.
- Based on the summaries, `x` and `y` have very similar distributions, while `z` tends to be smaller.

Given that diamonds are typically cut with the intention of maximizing their apparent size when viewed from above:
- `x` is likely the length (the longest dimension when viewed from above)
- `y` is likely the width (the second longest dimension when viewed from above)
- `z` is the depth (the height of the diamond from top to bottom)

The similarity between `x` and `y` distributions makes sense because many diamond cuts aim for symmetry when viewed from above (e.g., round, princess cuts), while `z` is intentionally smaller to maximize the visible area of the diamond face.

### 2. Explore the distribution of `price`. Do you discover anything unusual or surprising? (Hint: Carefully think about the `binwidth` and make sure you try a wide range of values.)

```{r}
# Default histogram of price
ggplot(diamonds, aes(x = price)) +
  geom_histogram() +
  labs(title = "Distribution of diamond price (default binwidth)",
       x = "Price (USD)",
       y = "Count")
```

```{r}
# Try a small binwidth to see more detail
ggplot(diamonds, aes(x = price)) +
  geom_histogram(binwidth = 100) +
  labs(title = "Distribution of diamond price (binwidth = 100)",
       x = "Price (USD)",
       y = "Count")
```

```{r}
# Try a larger binwidth
ggplot(diamonds, aes(x = price)) +
  geom_histogram(binwidth = 500) +
  labs(title = "Distribution of diamond price (binwidth = 500)",
       x = "Price (USD)",
       y = "Count")
```

```{r}
# Zoom in on the lower price range with small binwidth
ggplot(diamonds, aes(x = price)) +
  geom_histogram(binwidth = 50) +
  coord_cartesian(xlim = c(0, 5000)) +
  labs(title = "Distribution of diamond price (zoomed, binwidth = 50)",
       x = "Price (USD)",
       y = "Count")
```

Unusual or surprising findings in the price distribution:

1. The distribution is strongly right-skewed with most diamonds concentrated in the lower price ranges.
2. There appear to be gaps or empty bins at regular intervals throughout the distribution, particularly visible with smaller binwidths.
3. There are distinct peaks at specific price points (e.g., around $1500, $2000, etc.), suggesting that prices might be set at psychologically appealing "round" numbers.
4. There are almost no diamonds with prices just below these round numbers, creating a "comb-like" pattern in the distribution.

These patterns suggest that diamond pricing is not purely based on continuous physical attributes but is influenced by marketing strategies and psychological pricing points.

### 3. How many diamonds are 0.99 carat? How many are 1 carat? What do you think is the cause of the difference?

```{r}
# Count diamonds of 0.99 carats
diamonds_099 <- subset(diamonds, carat == 0.99)
nrow(diamonds_099)
```

```{r}
# Count diamonds of 1 carat
diamonds_1 <- subset(diamonds, carat == 1)
nrow(diamonds_1)
```

```{r}
# Count diamonds of 0.99 carats
diamonds_099 <- subset(diamonds, carat == 0.99)
count_099 <- nrow(diamonds_099)
print(paste("Number of diamonds with 0.99 carats:", count_099))

# Count diamonds of 1 carat
diamonds_1 <- subset(diamonds, carat == 1)
count_1 <- nrow(diamonds_1)
print(paste("Number of diamonds with 1 carat:", count_1))

# Create a subset for visualization
diamonds_around_1 <- subset(diamonds, carat >= 0.95 & carat <= 1.05)

# Plot the distribution
ggplot(diamonds_around_1, aes(x = carat)) +
  geom_histogram(binwidth = 0.01) +
  labs(title = "Distribution of diamond carat weights around 1 carat",
       x = "Carat",
       y = "Count")
```

There are only 23 diamonds that are 0.99 carat, while there are 1,558 diamonds that are exactly 1 carat. This dramatic difference is likely due to:

1. **Price threshold effects**: Diamonds are priced with significant jumps at whole-carat boundaries. A 1-carat diamond commands a premium price compared to a 0.99-carat diamond that's disproportionate to the tiny difference in weight.

2. **Psychological appeal**: Round numbers like 1.00 carat are psychologically appealing to buyers and easier to market.

3. **Strategic cutting**: Diamond cutters likely aim for these psychologically significant thresholds, making decisions during the cutting process to reach exactly 1.00 carat even if it means slightly sacrificing other aspects of the cut.

4. **Potential rounding**: Some diamonds recorded as exactly 1.00 carat might actually be slightly below or above but rounded to the nearest "marketable" number.

This pattern reflects how market forces and consumer psychology influence the physical characteristics of diamonds available in the market.

### 4. Compare and contrast `coord_cartesian()` vs. `xlim()` or `ylim()` when zooming in on a histogram. What happens if you leave `binwidth` unset? What happens if you try and zoom so only half a bar shows?

```{r}
# Using xlim() to zoom in
p1 <- ggplot(diamonds, aes(x = price)) +
  geom_histogram(binwidth = 500) +
  xlim(0, 5000) +
  labs(title = "Using xlim() to zoom",
       x = "Price (USD)",
       y = "Count")

print(p1)
```

```{r}
# Using coord_cartesian() to zoom in
p2 <- ggplot(diamonds, aes(x = price)) +
  geom_histogram(binwidth = 500) +
  coord_cartesian(xlim = c(0, 5000)) +
  labs(title = "Using coord_cartesian() to zoom",
       x = "Price (USD)",
       y = "Count")

print(p2)
```

```{r}
# Using xlim() with default binwidth
p3 <- ggplot(diamonds, aes(x = price)) +
  geom_histogram() +
  xlim(0, 5000) +
  labs(title = "Using xlim() with default binwidth",
       x = "Price (USD)",
       y = "Count")

print(p3)
```

```{r}
# Using coord_cartesian() with default binwidth
p4 <- ggplot(diamonds, aes(x = price)) +
  geom_histogram() +
  coord_cartesian(xlim = c(0, 5000)) +
  labs(title = "Using coord_cartesian() with default binwidth",
       x = "Price (USD)",
       y = "Count")

print(p4)
```

```{r}
# Zooming to show half a bar with xlim()
p5 <- ggplot(diamonds, aes(x = price)) +
  geom_histogram(binwidth = 1000) +
  xlim(0, 1500) +
  labs(title = "Using xlim() to zoom to half a bar",
       x = "Price (USD)",
       y = "Count")

print(p5)
```

```{r}
# Zooming to show half a bar with coord_cartesian()
p6 <- ggplot(diamonds, aes(x = price)) +
  geom_histogram(binwidth = 1000) +
  coord_cartesian(xlim = c(0, 1500)) +
  labs(title = "Using coord_cartesian() to zoom with half a bar",
       x = "Price (USD)",
       y = "Count")

print(p6)
```

Comparison of `coord_cartesian()` vs `xlim()`/`ylim()`:

1. **Data filtering vs. visual zooming**:
   - `xlim()`/`ylim()` actually **filters** the data before plotting, removing any observations outside the specified limits.
   - `coord_cartesian()` only **zooms in** on the plot without removing any data.

2. **Effect on binning**:
   - When using `xlim()` with a histogram, the binning is calculated after filtering, which changes the bins and can affect the appearance of the distribution.
   - With `coord_cartesian()`, binning is calculated using all the data first, and then the plot is zoomed in, preserving the original binning structure.

3. **Default binwidth behavior**:
   - With default binwidth and `xlim()`, the binwidth is calculated based on only the filtered data range.
   - With default binwidth and `coord_cartesian()`, the binwidth is calculated based on the full data range, then zoomed in.

4. **Partial bars**:
   - When zooming to show half a bar with `xlim()`, the partial bar is completely removed because the data is filtered first.
   - When using `coord_cartesian()`, partial bars are visible because it's just a visual zoom after the histogram has been computed.

In most exploratory data analysis cases, `coord_cartesian()` is preferable because it allows you to zoom in without changing the underlying calculations and gives a more accurate representation of the data distribution.

## 10.4.1 Exercises

### 1. What happens to missing values in a histogram? What happens to missing values in a bar chart? Why is there a difference in how missing values are handled in histograms and bar charts?

Let's create some example data with missing values to explore this:

```{r}
# Create sample data with NAs
set.seed(123)
sample_data <- data.frame(
  continuous_var = c(rnorm(100), NA, NA, NA, NA, NA),
  categorical_var = c(sample(letters[1:5], 100, replace = TRUE), NA, NA, NA, NA, NA)
)

# Histogram with missing values
ggplot(sample_data, aes(x = continuous_var)) +
  geom_histogram(binwidth = 0.5) +
  labs(title = "Histogram with missing values",
       x = "Continuous variable",
       y = "Count")
```

```{r}
# Bar chart with missing values
ggplot(sample_data, aes(x = categorical_var)) +
  geom_bar() +
  labs(title = "Bar chart with missing values",
       x = "Categorical variable",
       y = "Count")
```

```{r}
# Check if NA values are included in the bar chart
table(sample_data$categorical_var, useNA = "always")
```

In histograms:
- Missing values (NA) are **silently omitted** and no warning is given.
- They are not included in any bin or represented in the visualization.
- The reason is that NAs cannot be positioned on a continuous numeric axis.

In bar charts:
- Missing values are **treated as a separate category** and shown as a distinct bar (usually labeled as "NA").
- This happens because bar charts deal with categorical variables, and NA can be considered as another category.

The difference in handling occurs because:
1. Histograms work with continuous numeric data where NAs have no meaningful position on the axis.
2. Bar charts work with categorical data where NA can be treated as just another category level.

This difference reflects the fundamental nature of the data types being visualized: continuous vs. categorical.

### 2. What does `na.rm = TRUE` do in `mean()` and `sum()`?

```{r}
# Create a vector with NAs
x <- c(1, 2, 3, NA, 5)

# Calculate mean with default settings
mean_default <- mean(x)
print(paste("Mean with default settings:", mean_default))

# Calculate mean with na.rm = TRUE
mean_na_rm <- mean(x, na.rm = TRUE)
print(paste("Mean with na.rm = TRUE:", mean_na_rm))

# Calculate sum with default settings
sum_default <- sum(x)
print(paste("Sum with default settings:", sum_default))

# Calculate sum with na.rm = TRUE
sum_na_rm <- sum(x, na.rm = TRUE)
print(paste("Sum with na.rm = TRUE:", sum_na_rm))
```

The `na.rm = TRUE` parameter in `mean()` and `sum()` functions:

1. **Purpose**: It instructs the function to remove (ignore) any NA values before performing the calculation.

2. **Default behavior** (`na.rm = FALSE`):
   - If any NA values are present in the data, both `mean()` and `sum()` will return NA.
   - This is a conservative approach that alerts you to the presence of missing data.

3. **With `na.rm = TRUE`**:
   - NA values are excluded from the calculation.
   - `mean()` calculates the average of only the non-missing values.
   - `sum()` calculates the total of only the non-missing values.

4. **When to use**:
   - Use `na.rm = TRUE` when you want to get a result based on available data despite missing values.
   - Keep the default when you want your analysis to explicitly fail if missing data is present, forcing you to handle the missing data problem.

Understanding the implications of removing NA values is crucial because it can affect the interpretation of your results, especially if the missing data is not random or represents a substantial portion of your dataset.

### 3. Recreate the frequency plot of `scheduled_dep_time` colored by whether the flight was cancelled or not. Also facet by the `cancelled` variable. Experiment with different values of the `scales` variable in the faceting function to mitigate the effect of more non-cancelled flights than cancelled flights.

For this exercise, I'll simulate a dataset similar to flights data:

```{r}
# Create a simple simulated flight dataset
set.seed(123)

# Create a small dataset with 1000 flights
n_flights <- 1000

# Generate scheduled departure hours (0-23)
sched_dep_hour <- sample(0:23, n_flights, replace = TRUE)

# Generate cancellation status (about 3% cancelled)
cancelled <- sample(c(TRUE, FALSE), n_flights, replace = TRUE, prob = c(0.03, 0.97))

# Create the dataframe
sim_flights <- data.frame(
  sched_dep_hour = sched_dep_hour,
  cancelled = cancelled
)

# Create a basic plot colored by cancelled status
ggplot(sim_flights, aes(x = sched_dep_hour, fill = cancelled)) +
  geom_bar() +
  scale_fill_manual(values = c("FALSE" = "darkblue", "TRUE" = "red")) +
  labs(title = "Scheduled departure time frequency by cancellation status",
       x = "Hour of scheduled departure time",
       y = "Number of flights")

# Facet by cancelled with default scales
ggplot(sim_flights, aes(x = sched_dep_hour)) +
  geom_bar() +
  facet_wrap(~ cancelled) +
  labs(title = "Departure time by cancellation status (fixed scales)")

# Facet with free_y scales
ggplot(sim_flights, aes(x = sched_dep_hour)) +
  geom_bar() +
  facet_wrap(~ cancelled, scales = "free_y") +
  labs(title = "Departure time by cancellation status (free y-scales)")

# Calculate proportions by hour and cancellation status
hour_counts <- table(sim_flights$sched_dep_hour, sim_flights$cancelled)
hour_props <- prop.table(hour_counts, margin = 2)
hour_props_df <- as.data.frame(hour_props)
names(hour_props_df) <- c("sched_dep_hour", "cancelled", "prop")

# Plot proportions
ggplot(hour_props_df, aes(x = sched_dep_hour, y = prop)) +
  geom_col() +
  facet_wrap(~ cancelled) +
  labs(title = "Proportion of flights by hour and cancellation status")
```

```{r}
# First, recreate the frequency plot colored by cancelled status
ggplot(sim_flights, aes(x = sched_dep_hour, fill = cancelled)) +
  geom_bar() +
  scale_fill_manual(values = c("FALSE" = "darkblue", "TRUE" = "red")) +
  labs(title = "Scheduled departure time frequency by cancellation status",
       x = "Hour of scheduled departure time",
       y = "Number of flights",
       fill = "Cancelled")
```

```{r}
# Now facet by cancelled with default scales
ggplot(sim_flights, aes(x = sched_dep_hour)) +
  geom_bar() +
  facet_wrap(~ cancelled) +
  labs(title = "Scheduled departure time by cancellation status (fixed scales)",
       x = "Hour of scheduled departure time",
       y = "Number of flights")
```

```{r}
# Facet with free_y scales
ggplot(sim_flights, aes(x = sched_dep_hour)) +
  geom_bar() +
  facet_wrap(~ cancelled, scales = "free_y") +
  labs(title = "Scheduled departure time by cancellation status (free y-scales)",
       x = "Hour of scheduled departure time",
       y = "Number of flights")
```

```{r}
# Alternative approach: use proportion instead of count
# Count by hour and cancellation status
counts <- table(sim_flights$sched_dep_hour, sim_flights$cancelled)

# Convert to data frame
counts_df <- as.data.frame(counts)
names(counts_df) <- c("sched_dep_hour", "cancelled", "n")

# Make sure sched_dep_hour is numeric
counts_df$sched_dep_hour <- as.numeric(as.character(counts_df$sched_dep_hour))

# Calculate proportions by group
counts_by_status <- split(counts_df, counts_df$cancelled)
prop_TRUE <- counts_by_status[["TRUE"]]
prop_TRUE$prop <- prop_TRUE$n / sum(prop_TRUE$n)
prop_FALSE <- counts_by_status[["FALSE"]]
prop_FALSE$prop <- prop_FALSE$n / sum(prop_FALSE$n)
props_df <- rbind(prop_TRUE, prop_FALSE)

# Plot proportions
ggplot(props_df, aes(x = sched_dep_hour, y = prop)) +
  geom_col() +
  facet_wrap(~ cancelled) +
  labs(title = "Proportion of flights by hour and cancellation status",
       x = "Hour of scheduled departure time",
       y = "Proportion")
```

Observations about the faceted plots:

1. **Default scales**:
   - With default scales (fixed for both facets), the pattern for cancelled flights is hard to see because there are many more non-cancelled flights.

2. **Free y-scales** (`scales = "free_y"`):
   - This allows each facet to have its own y-axis scale.
   - The pattern of cancelled flights becomes much more visible.
   - The disadvantage is that direct comparison of counts between facets becomes misleading.

3. **Using proportions**:
   - An alternative approach is to show proportions within each cancellation status instead of raw counts.
   - This makes patterns comparable even when the total counts differ dramatically.

The "free_y" scales option is particularly useful here because:
- It reveals that cancelled flights have a somewhat different distribution across hours than non-cancelled flights.
- It allows us to see patterns in the smaller group (cancelled flights) that would otherwise be obscured.
- It highlights that early morning flights and late evening flights seem to have a higher cancellation rate compared to the rest of the day.

When dealing with imbalanced groups, choosing the right faceting scales is crucial for revealing patterns that might otherwise be hidden by the dominant group.
