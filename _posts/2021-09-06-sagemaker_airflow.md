---
title: "Sagemaker Batch Transform"
date: 2021-09-06
tags: [deep learning, mlops]
excerpt: "Productionizing a fun new model."
---
I’ve spent the last few weeks at work building a new model to predict which skills are taught by a particular course. I modeled this problem using a CNN with various text and user enrollment features, and was able to improve on the existing model’s performance by a significant margin in terms of AUC, F1, and simplicity/scalability.

The new model is productionized using Airflow and Sagemaker. I was already familiar with Airflow and online Sagemaker endpoints, but this was my first experience working with Sagemaker Batch Transform.

The pipeline is broken up into two steps: training, which happens weekly, and inference, which takes place daily (in case there is any new content). Each has their own Airflow DAG.


The training pipeline:

1. Queries the data.
2. Prepares the data (formatting, creating sample weights to handle class imbalance and prioritize high-confidence data points, etc.).
3. Trains the model on Sagemaker.
	- Note that hyperparam tuning is done on an ad-hoc basis and therefore lives outside of this pipeline.
4. Registers the model and evaluation metrics in the Sagemaker model registry.
5. Evaluates the model’s performance, and changes the registry status based on whether it meets some predefined criteria (either meeting a baseline or beating the most-recently-approved model). 


The inference pipeline:

1. Prepares the data for a Sagemaker Batch Transform job (“jsonify-ing” it).
2. Launches the batch transform job, using the most-recently-approved model from the registry.
3. Saves the predictions for use in downstream applications.

This was a super fun project and I learned a ton -- both about building and improving on neural networks, and also about Sagemaker batch transform.
