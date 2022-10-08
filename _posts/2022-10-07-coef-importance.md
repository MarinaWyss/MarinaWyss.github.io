---
title: "Coefficient Magnitude and Feature Importance"
date: 2022-10-07
tags: [statistics]
excerpt: "Don't forget to standardize."
---

I recently started a new position where I've been supporting a team with more statistical models. I was asked whether we can use coefficient magnitude in a regression model as a measure of feature importance.

The answer to that is no, not unless we do some pre-processing. If we naively create a regression model with features on different scales, coefficient size tells us more about the scale of a feature than its importance relative to others.

Maybe we could get around this by **normalizing** all features to ragnge from 0-1? While that gets us closer, it doesn't account for differences in the variance (see this [stackexchange post](https://stats.stackexchange.com/questions/202221/for-linear-classifiers-do-larger-coefficients-imply-more-important-features) for a more detailed answer).

What we can do, however, is **standardize** our features so that they have mean 0 and SD of 1. In this case, coefficient size *does* indicate feature "importance" (though that term itself is loosely defined). 

I got curious about this and did some experiments, and I found that once the features are standardized the coefficients align with the feature importance scores from SHAP nicely, too.
