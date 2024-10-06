# Final-Project
# ======================================
# Data Loading and Initial Preprocessing
# ======================================

# Load the dataset
`df <- read.csv("C:\\Users\\User\\Desktop\\proposal -orian rashti\\data\\employee_survey.csv")`
Read Tables
> You can download all used data from:
 [https://www.kaggle.com/datasets/lainguyn123/employee-survey]
> You should than place all that data in the root directory of the project under a folder named data.


# Load necessary library
`library(dplyr)
`
# Convert TrainingHoursPerYear to numeric and remove records where Gender is "Other"
```
df$TrainingHoursPerYear <- as.numeric(df$TrainingHoursPerYear)
df_clean <- subset(df, Gender != "Other")
```

# ======================================
# Install Required Libraries
# ======================================
```

install.packages("randomForest")
install.packages("ggplot2")
install.packages("reshape2")

library(randomForest)
library(ggplot2)
library(reshape2)

```
# ======================================
# Factorizing Ordinal and Categorical Variables
# ======================================

# Ordinal variables (JobSatisfaction, WLB, WorkEnv, Workload, Stress)
```
df_clean$JobSatisfaction <- factor(df_clean$JobSatisfaction, ordered = TRUE)
df_clean$WLB <- factor(df_clean$WLB, ordered = TRUE)
df_clean$WorkEnv <- factor(df_clean$WorkEnv, ordered = TRUE)
df_clean$Workload <- factor(df_clean$Workload, ordered = TRUE)
df_clean$Stress <- factor(df_clean$Stress, ordered = TRUE)

```
# Categorical variables (without order)
```
df_clean$Gender <- as.factor(df_clean$Gender)
df_clean$MaritalStatus <- as.factor(df_clean$MaritalStatus)
df_clean$JobLevel <- as.factor(df_clean$JobLevel)
df_clean$Dept <- as.factor(df_clean$Dept)
df_clean$EmpType <- as.factor(df_clean$EmpType)
df_clean$CommuteMode <- as.factor(df_clean$CommuteMode)
df_clean$EduLevel <- as.factor(df_clean$EduLevel)
df_clean$haveOT <- as.factor(df_clean$haveOT)

```
# ======================================
# Random Forest Models with 500 and 1000 Trees
# ======================================

# Model 1: All features except EmpID (500 trees)
`model_rf1 <- randomForest(JobSatisfaction ~ . - EmpID, data = df_clean, ntree = 500)
`
# Model 2: All features except EmpID (1000 trees)
`model_rf2 <- randomForest(JobSatisfaction ~ . - EmpID, data = df_clean, ntree = 1000)
`
# Print model importance and performance
```
importance(model_rf1)
print(model_rf1)

importance(model_rf2)
print(model_rf2)
```

# ======================================
# Reduced Model with Selected Features (500 Trees)
# ======================================

# Reduced model with selected features
```
model_rf3 <- randomForest(JobSatisfaction ~ Dept + Experience + SleepHours + Workload + PhysicalActivityHours + WorkEnv + WLB, 
                          data = df_clean, ntree = 500)

```
# Print importance and model performance
```
importance(model_rf3)
print(model_rf3)
```

# ======================================
# Variable Importance Visualization (Reduced Model)
# ======================================

# Extract importance from the reduced model
`importance_df <- as.data.frame(importance(model_rf3))
`
# Add variable names as a column
`importance_df$Variable <- rownames(importance_df)
`
# Plot variable importance
```
ggplot(importance_df, aes(x = reorder(Variable, MeanDecreaseGini), y = MeanDecreaseGini)) +
  geom_bar(stat = "identity") +
  coord_flip() + 
  xlab("Variables") + 
  ylab("Mean Decrease Gini") +
  ggtitle("Variable Importance in Random Forest Model")

```
# ======================================
# Confusion Matrix and Heatmap Visualization
# ======================================

# Create a confusion matrix for Model 2
`conf_matrix <- table(predictions = predict(model_rf2, df_clean), actual = df_clean$JobSatisfaction)
`
# Convert matrix to a data frame for plotting
`conf_matrix_df <- melt(conf_matrix)
`
# Plot a heatmap for the confusion matrix
```
ggplot(conf_matrix_df, aes(x = predictions, y = actual, fill = value)) +
  geom_tile() +
  scale_fill_gradient(low = "white", high = "red") +
  labs(title = "Confusion Matrix Heatmap", x = "Predicted", y = "Actual")

```


