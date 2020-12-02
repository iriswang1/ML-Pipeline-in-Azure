# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary
This dataset contains 32,950 clients information (19 independent variables) from a bank marketing campaign. The goal of is to predict if the client will subscribe (yes/no) a term deposit, the variable y, using classification models.

The best performing model was VotingEnsemble generated by AutoML, which is a prefitted soft voting classifier using 10 estimators.  

## Scikit-learn Pipeline
In the Scikit-learn pipeline architecture, I first loaded the dataset from a CSV file in the workspace storage as a tabular dataset. After data cleaning, I split data into training (80%) and testing (20%) datasets. Then I started applying Logistic Regression model to the training dataset. I used RandomParameterSampling in a HyperDrive configuration to do hyperparameter tuning using model parameters, including C, max_iter, and penalty. For model metrics, I used Accuracy as primary metric. I used BanditPolicy as early stopping policy and set max_total_runs as 40 and max_concurrent_runs as 4.   

**What are the benefits of the parameter sampler you chose?**
I chose RandomParameterSampling as the parameter sampler. In random sampling, hyperparameter values are randomly selected from the defined search space, including discrete and continuous hyperparameters, whereas grid sampling only supports discrete hyperparameters. And comparing to Bayesian sampling which may require more budget and time to complete runs, Random sampling can save time and cost to find the best performing model hyperparameters.

**What are the benefits of the early stopping policy you chose?**
I chose BanditPolicy with evalualtion_interval as 1 and slack_factor as 0.01. It will terminate jobs that are not performing well and are not likely to yield a good model in the end. Compare to median stopping policy, BanditPolicy with a smaller slack can provide more savings with no loss on primary metric.

## AutoML
AutoML generated VotingEnsemble model which gave the best result among other classification models generated, such as LightGBM, XGBoostClassifier, SGD, etc. The parameters the best model used include algorithem as 'prefittedsoftvotingclassifier', min_sample_leaf as 0.03578, min_sample_split as 0.1505, n_estimators as 10, n_jobs as 1, and weights as [0.2, 0.133333, 0.4666667, 0.0666667, 0.0666667, 0.0666667].

## Pipeline comparison
The Logistic Regression model from Scikit-learn pipeline gave accuracy of 0.9098. The VotingEnsemble model generated by AutoML gave accuracy of 0.9165. In architecture, the data preprocessing step are basically same for both models. Scikit-learn pipeline applied one classification model with hyperparameter tuning. AutoML applied more than 50 different models with featurization and cross validation. 

Most of the Scikit-learn pipeline is based on manual work. With the help of HyperDrive, it saved time for hyperparameter tuning, and gave some freedom to finetune the search space for a better result. By contrast, AutoML used roughly same time to generate different models and a higher accuracy because of the automation process to iterate and develop models. AutoML also required a simpler programming work and allowed me to focus on the problem solving. 

## Future work
For scikit-learn pipeline, it will be helpful to try other classification algorithms and re-define hyperparameters for model tuning. I also want to try different parameter sampling and early stopping policy to find a better performing model. For AutoML, there are some space to explore AutoML configuration parameters to improve model. 


