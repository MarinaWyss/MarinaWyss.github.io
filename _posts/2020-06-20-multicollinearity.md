---
title: "Multicollinearity"
date: 2020-06-20
tags: [machine learning, statistics]
header: 
  image: "/images/multicollinearity/heatmap.png"
excerpt: "Why statisticians care about it and ML folks don’t (but maybe should)."
mathjax: "true"
---

## What I Learned: How Multicollinearity Is and Isn't Important for ML

As someone coming from a more traditional quantitative methods/statistics background, multicollinearity is often something that occurs to me as potentially hazardous when building models. I’ve noticed that this issue is often ignored in machine learning, so I wanted to get a bit clearer about why.

**What is multicollinearity, and why do statisticians care about it so much?**

Multicollinearity is basically when two or more variables are highly linearly correlated. It can present major problems in a regression setting, because it distorts the coefficients, increases standard errors, and decreases statistical power.

Think about it this way: The main goal of regression is to isolate the relationships between each independent variable and the dependent variable. The regression coefficients are supposed to represent the mean change in the dependent variable for each one-unit change in an independent variable, holding the other variables constant. But, if the independent variables are highly correlated, this isn’t possible. The model can’t estimate each independent variable’s effect individually. 

This causes the coefficient estimates to be inconsistent and highly sensitive to small changes in the model. They are less precise and have larger standard errors, which weakens the power of the model, and p-values may not be as reliable for determining statistical significance. 

If we cannot trust our coefficient estimates, causal inference is also no longer possible if the collinear variables are the ones we’re actually interested in, as we can’t see which of the collinear predictors is responsible for the prediction and the causal effect of interest. It isn’t so important if the control variables are collinear, though, since multicollinearity only affects the specific independent variables that are correlated, and we won’t be interpreting control variables anyway most likely. 


**Why do machine learning folks tend to ignore it?**

While multicollinearity affects coefficients and p-values, it does not influence the quality of predictions. In ML predictive performance is typically king, so in such cases where interpreting the coefficients is not important, neither is multicollinearity.

Also, many ML algorithms overcome the problem of multicollinearity on their own naturally. For example, decision trees will simply randomly choose one only of the collinear features to use. Regularization can also help overcome this problem.


**But what about IML?**

Personally, I find modeling without any attention to causal inference quite unsatisfying. While we may be able to make good predictions, a prediction alone is not sufficient for creating actionable analyses in many cases. And multicollinearity can make interpreting the features challenging, even when using models that are robust when predicting.

For example, there’s the phenomenon of “correlation bias” in the field of XAI/interpretable machine learning. If we were to use SHAP on a model that was trained on collinear features, these features may be given an importance score of zero. If we have a group of highly-correlated features, we would expect them to receive equal importance scores. But SHAP explains the model, rather than the data - and the model may choose one feature to randomly assign all of the importance to while ignoring the others entirely, as mentioned above.


**How can multicollinearity be avoided?**

So, if we decide we do care about interpreting features, what can we do to avoid multicollinearity? Well, first we need to identify the collinear features. This is most commonly done using VIF (Variance Inflation Factors). 
VIFs are calculated for each predictor using a linear regression of that predictor and all the other predictors. The VIF uses the resulting $$R^2$$:

$$ VIF = 1/(1-R^2) $$

This value tells us by how much the variance of a coefficient is “inflated” because of linear dependence with other predictors. For example, “a VIF of 1.8 tells us that the variance (the square of the standard error) of a particular coefficient is 80% larger than it would be if that predictor was completely uncorrelated with all the other predictors.” [source](https://statisticalhorizons.com/multicollinearity)

VIF values start at 1 and have no upper bound. Some folks are cautious about VIFs over 5, some people over 10. 

As an alternative, a simple heatmap can frequently be an easy first step to identifying highly-correlated features.
Once we know which features are potentially collinear, we can use domain knowledge to either:

1. Feature engineer a new feature that combines the separate collinear variables into one, or 
2. Simply drop the variable that is less theoretically important, or less strongly correlated with the target variable.
