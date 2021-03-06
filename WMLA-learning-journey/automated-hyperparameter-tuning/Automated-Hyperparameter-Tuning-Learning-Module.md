

## Summary

One of the first steps in developing a new AI model is to define the characterization of the training cycle. The variables which describe these elements of the model configuration are called hyperparameters.

For the same model, hyperparameters may be varied based on the training data set to achieve the optimal training result.

Watson ML Accelerator Auto Hyper-parameter Tuning feature helps reducing the number of experimental iterations and accelerate arriving at the optimal parameters.    It automates hyper-parameter search with  defined ranges of searching space and running these searches in parallel across available resources in the cluster,  with Data Scientist's choice of search algorithm including Random, Tree-based Parzen Estimator, Bayesian or Hyperband, or even bringing in their own Algorithm.      The search engine returns the most promising combination of hyper-parameters to the data scientist.

Afterwards, data scientists can kick off training with the optimal values of hyperparameters found during this automated tuning.


## Description
In this module we will use multiple notebooks to walk through the process of taking the an PyTorch model from the community, making the needed code changes and identifying optimal hyper-parameter with Watson ML Accelerator Hyperparameter Optimization, including:

-  Introduction of Watson ML Accelerator Hyperparameter Optimization -> https://github.com/IBM/wmla-assets/blob/master/HPO-demonstration/WMLA-HPO-state.ipynb
-  How to define custom search space -> https://github.com/IBM/wmla-assets/blob/master/HPO-demonstration/WMLA-HPO-Custom-Experiment.ipynb
-  How to bring Your Own Algorithm -> https://github.com/IBM/wmla-assets/blob/master/HPO-demonstration/WMLA-HPO-Plugin-Search-Algorithm.ipynb
-  Distribute Hyperparameter Optimization tasks with Elastic Distributed Training -> https://github.com/IBM/wmla-assets/blob/master/HPO-demonstration/HPO-EDT/WMLA-HPO-Hyperband-EDT-external.ipynb


## Instructions

The detailed steps for this tutorial can be found in the associated xx.  
Learn how to:
- Make changes to your code
- Set up API end point and log on
- Submit HPO job via API
- Monitor running HPO job
- Debug any issues


## Changes to your code

Note that the code sections below show a comparison between the "before" and "HPO enabled" versions of the code using `diff`.

1. Import the dependent libararies:

&nbsp;
&nbsp;
![image1](https://raw.githubusercontent.com/IBM/wmla-assets/zhuangxy-patch-1/WMLA-learning-journey/automated-hyperparameter-tuning/shared_images/hpo_update_model_0.png)
&nbsp;
&nbsp;

2. Get the WMLA cluster DLI_DATA_FS, RESULT_DIR and LOG_DIR for the hpo training job. The DLI_DATA_FS could be used for shared data placement, the RESULT_DIR can be used for final model saving, and the LOG_DIR can be used for user logs and monitoring.

&nbsp;
Note: DLI_DATA_FS is set when install DLI cluster; RESULT_DIR and LOG_DIR is generated by WMLA for each HPO experiment.

&nbsp;
&nbsp;
![image1](https://raw.githubusercontent.com/IBM/wmla-assets/zhuangxy-patch-1/WMLA-learning-journey/automated-hyperparameter-tuning/shared_images/hpo_update_model_1.png)
&nbsp;
&nbsp;

3. Replace the hyperparameter definition code by reading hyperparameters from the `config.json` file. the `config.json` is generated by WMLA HPO, which contains a set of hyperparameter candidates for each tuning jobs. The hyperparameters and the search space is defined when submitting the HPO task. For example, here the hyperparameter learning_rate is set to tune:

&nbsp;
&nbsp;
![image2](https://raw.githubusercontent.com/IBM/wmla-assets/zhuangxy-patch-1/WMLA-learning-journey/automated-hyperparameter-tuning/shared_images/hpo_update_model_2.png)

&nbsp;
Then you could use the hyperparameter you get from `config.json` where you want:
&nbsp;
![image2](https://raw.githubusercontent.com/IBM/wmla-assets/zhuangxy-patch-1/WMLA-learning-journey/automated-hyperparameter-tuning/shared_images/hpo_update_model_2_2.png)
&nbsp;
&nbsp;

4.  Write the tuning result into `val_dict_list.json` under RESULT_DIR. WMLA HPO will read this file for each tuning job to get the metric values. Define a test_metrics list to store all metric values and pass the epoch parameter to the test function. Then you could add the metric values to the test_metrics list during the training test process. Please note that the metric names should be specified when submitting the HPO task, and be consitent with the code here.
&nbsp;
For example, the HPO task submit request, `loss` will be used as the objective metric the tuning will try to minimize the `loss`:
```
'algoDef': # Define the parameters for search algorithms  
{
    # Name of the search algorithm, one of Random, Bayesian, Tpe, Hyperband  
    'algorithm': 'Random',   
    # Name of the target metric that we are trying to optimize when searching hyper-parameters.
    # It is the same metric name that the model update part 2 trying to dump.
    'objectiveMetric' : 'loss',
    # Strategy as how to optimize the hyper-parameters, minimize means to find better hyper-parameters to
    # make the above objectiveMetric as small as possible, maximize means the opposite.
    'objective' : 'minimize',
    ...
}
```
&nbsp;
The code change:

&nbsp;
&nbsp;
![image2](https://raw.githubusercontent.com/IBM/wmla-assets/zhuangxy-patch-1/WMLA-learning-journey/automated-hyperparameter-tuning/shared_images/hpo_update_model_3.png)
&nbsp;
&nbsp;

5. After the training complete, write the metric list into the `val_dict_list.json` file. 
&nbsp;
&nbsp;
![image2](https://raw.githubusercontent.com/IBM/wmla-assets/zhuangxy-patch-1/WMLA-learning-journey/automated-hyperparameter-tuning/shared_images/hpo_update_model_5.png)
&nbsp;
&nbsp;

