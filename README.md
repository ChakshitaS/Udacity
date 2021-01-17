# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.
![Pipeline Architecture](https://video.udacity-data.com/topher/2020/September/5f639574_creating-and-optimizing-an-ml-pipeline/creating-and-optimizing-an-ml-pipeline.png)
## Summary
The dataset represents Bank marketing campaign of a bank. The data indicates the potential customer likely for the deposit of the bank represented by the column ("y").We use Classification method to determine the result.

We will use two machine learning experiments, with primary metric as "Accuracy".

1) Scikit-learn  with Logistic Regression Classifier, Script Run config as an estimator using the Sklearn env and hyperdrive run for hyperparameter tuning.

2) AutoML experiment by selection of classification task and their hyper parameters.

Best models selected under each of the pipelines are as follows:

i) Scikit-learn: Logistic Regression model with 90.08% Accuracy and with parameter values: C: uniform(0.05, 2), max_iter:  choice(range(10,200,20)).

ii) Azure AutoML: VotingEnsemble classifier with 91.679 % Accuracy.
Hence Azure Automl is the best experiment to run to achieve maximum accuracy.

## Scikit-learn Pipeline
**Explain the pipeline architecture, including data, hyperparameter tuning, and classification algorithm.**
1) To initiate any experiment to run , we have to spin a compute for faster execution of the pipeline. In this experiment, we have used computecluster with a VM size of STANDARD_D2_V2 have max_nodes set to 4. 
2) Run the python script to make sure all the defined parameters have been validated with the dataset set to tabular format and splitting the data to train and test datasets.
Note: If the indentation is not followed in the pythin script. The script will throw an exception while running any experiment.
3) Create a conda dependencies file if there are any particular versions/packages of azure ml/python to be used.
4)The Hyperparameter tuning requires the following parameters to be defined:
 i)Parameter Sampler: Random Paramter sampling technique was used.
 ii)Estimator: We have define a run script config which defines the SKlearn estimator with the environment defined.
 iii)Policy: Bandit Policy has been used as a early stopping policy
 
**What are the benefits of the parameter sampler you chose?**
Random Parameter Sampling uses random values for unbiased selection. In this method, we have defined "--C": uniform(0.05, 2), "--max_iter": choice(range(10,200,20))  as the parameter for continuous and discrete selection of values for our dataset. 
The benefits of choosing the random sampling method are:
In the deinfed Hyperparameter Search space:
 1) It Supports and improvises the search for better results with the parameters defined. 
 2) In our case, We have defined our class with the defined datapoints in a particular range to restrict the use of compute resource.
**ps = RandomParameterSampling({"--C": uniform(0.05, 2), "--max_iter": choice(range(10,200,20)) })**
 3) This helps to retrieve and select best random defined values which estimates the maximum accuracy.
 4) It also supports the early termination policy for low-performance runs

**What are the benefits of the early stopping policy you chose?**
The configuration used for Bandit Policy is  **BanditPolicy(evaluation_interval=2, slack_factor=0.1, slack_amount=None, delay_evaluation=0)**
This policy will terminate if the primary metric is not defined within the slackfactor/amount in this case the slack_factor is 0.1 which will yield the best model.

Lastly, we use Logistic regression as our classification type for our best model  to get the best accuracy score for the hyperdrive run experiment.

## AutoML
**In 1-2 sentences, describe the model and hyperparameters generated by AutoML.**
AutoML entitles to cleaning the dataset and specifying the configuration settings through Automl config. We use here Classification task  to depict our accuracy. Azure AutoMl
runs 29 different models in our experiment and choosing the Voting Ensemble with the best accuracy level.
**Voting Ensemble Algorithm**

1) This ensemble model has presented the max. accuracy in our model.
![Voting_Ensemble](https://github.com/ChakshitaS/Udacity/blob/master/Screenshot%202021-01-18%20080233.png)
2) Our classification algorithm has the predicted class probabilities with the column Y as the output which contibutes to choose the Voting model comoared to the stacking. As Voting Ensemble model predicts from multiple models.
3)  Voting ensemble offers better results with lower variance  in the predictions compared to individual models.
4)  Most effective when combining the different fits of model with different hyperparameters.
 AutoMl parameters generated gives us the description of the best model used for estimating the accuracy of the experiment in given period of time.
These provide the intuition towards the model choice and hyperparameter configs.

The Hyperparameters generated by AutoMl are:
1)Accuracy
0.91679
AUC macro
0.94631
AUC micro
0.98027
AUC weighted
0.94631
Average precision score macro
0.82376
Average precision score micro
0.98107
Average precision score weighted
0.95512
Balanced accuracy
0.77436
F1 score macro
0.78345
F1 score micro
0.91679
F1 score weighted
0.91528
Log loss
0.18625
Matthews correlation
0.56792
Norm macro recall
0.54871
Precision score macro
0.79411
Precision score micro
0.91679
Precision score weighted
0.91419
Recall score macro
0.77436
Recall score micro
0.91679
Recall score weighted
0.91679
Weighted accuracy
0.95222

2)Accuracy
0.91647
AUC macro
0.94830
AUC micro
0.98088
AUC weighted
0.94830
Average precision score macro
0.82620
Average precision score micro
0.98166
Average precision score weighted
0.95590
Balanced accuracy
0.73840
F1 score macro
0.76515
F1 score micro
0.91647
F1 score weighted
0.91142
Log loss
0.17885
Matthews correlation
0.53771
Norm macro recall
0.47680
Precision score macro
0.80350
Precision score micro
0.91647
Precision score weighted
0.90936
Recall score macro
0.73840
Recall score micro
0.91647
Recall score weighted
0.91647
Weighted accuracy
0.96074

The Best run model gives us the interpretation of the best model during the training data.
The fitted model can give us more explanation of the featured data from our test or train datasets and findable classes for labelled column "y" for clasification algorithm.

![Automl1](https://github.com/ChakshitaS/Udacity/blob/master/Screenshot%202021-01-18%20101605.png)
![Automl4](https://github.com/ChakshitaS/Udacity/blob/master/Screenshot%202021-01-18%20102428.png)
The following screenshot explains the metrics for best fit and run model defined in our Automl config.
## Pipeline comparison
**Compare the two models and their performance. What are the differences in accuracy? In architecture? If there was a difference, why do you think there was one?**
| Pipeline  | Accuracy |
| ------------- | ------------- |
| Hyperdrive run  | 90.86% |
| AutoML Run | 91.67%  |

AutoMl is better than hyperdrive as we need to define the parameters in the config for a succesfull iteration by definining the samplers , early stop policy and the most important is algorithm which is a preselected algorithm on a choice.
In Automl, the task defined choses the best suited algorithm with the accuracy defined and also maximizes the performance on a mentioned problem.

## Future work
**What are some areas of improvement for future experiments? Why might these improvements help the model?**
1) Use two different sampling to compare the results /accuracy in the hyperdrive run pipeline.
2) Perform different Iterations to record the best accuracy.
3) AutoMl to use regression model as we use logistic regression as our algorithm model
4) Increase the timeout to extra algorithms to choose the best model
5) Use the concept of deep learning/neural networks which is known for giving the most accurate results.

## Proof of cluster clean up
**If you did not delete your compute cluster in the code, please complete this section. Otherwise, delete this section.**
**Image of cluster marked for deletion**
cpu_cluster_name.delete() to delete the compute in the code.
![compute](https://github.com/ChakshitaS/Udacity/blob/master/Screenshot%202021-01-18%20101648.png)
