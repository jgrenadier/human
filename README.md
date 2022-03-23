# Tuning your models with AutoML

note: The code in this github project is from [Fergus O'Boyles Poverty Prediction using Machine Learning Methods project](https://github.com/FergusOBoyle/sustainable-dev-goals-forecasting). Please see the README.md for that project to understand
the code and the intent of that project.

This project uses that code as a starting point to gain some experience with Microsoft's AutoML on Azure.

Here's a scenario: you have a Machine Learning model implemented in a Python notebook.  You want to improve the optimization.  AutoML on the Microsoft Azure cloud may be able to help. While AutoML is a proprietary tool, you still may be able to get some benefit from it and end up with an improved python notebook that you can tweak on your own outside of AutoML.

I just used some weasel words like "maybe".  It's possible that the code generated by AutoML may not be useful outside of the Microsoft cloud today.  If so, you were probably going to host your model somewhere in the cloud for evaluation by an application anyway. If so, pay the man and get on to the next project.  Or, you will at least get some understanding of alternative methods to try on your own from the AutoML code.

So, here is the workflow that I used successfully with AutoML. Your mileage may vary.

1. Find where in your code where the fit method is called.  
   Example: 
            grid_model = GridSearchCV(model, param_grid = params, scoring=scorer,
                        n_jobs=4,iid=False, cv=5 ,return_train_score=True)
            grid_model.fit(X_train,y_train) 

2. Find or reassemble the test and training data into a single table and export to a simple CSV file. This data contains only the columns that you wanted to use as features for your model.  All the data clearing and absent value processing that you needed is already part of this table.

Your new snippet in your notebook will look something like:

import numpy as np
output_dir3 = '.\\..\\data\\'
np.savetxt(output_dir3 + r'combined_data3.csv', xy_combined, delimiter=",")

where xy_combined was your combined training and test tables.

3. Import the CSV data into AutoML on Azure.  Answer a few AutoML question such as whether you are 
   trying to solve a classification or optimization problem and how much computer resources you want 
   to throw at the problem and you are off to the races.
   
   Microsoft already has a tutorial on these steps: 
   https://docs.microsoft.com/en-us/azure/machine-learning/tutorial-first-experiment-automated-ml 
   
   While the azure cloud is chugging away trying a bunch of models, you can explore the models
   as they are built. In particular, you can view barcharts that show the relative importance of
   each feature.  This gives you a glimpse of the inner workings and 
   helps you make sense of the model.  If a logically unimportant feature has a large result 
   on y, then maybe you have more feature engineering to do.
   
   One minor annoyance: AutoML doesnt seem to me yet to provide easy ways of cleaning up the
   resources generated by AutoML. So, I (and Microsoft) recommend that you dedicate a "Resource Group" entirely
   to hold your work and when you are all done with your AutoML session, delete the resource group.
   
   If you want to stay in the Azure cloud and you are happy with your results, you can deploy your
   model and make it available as a web service endpoint.  If you want to try to use the generated
   python code that represents your best model, you can continue on to step 4.
   
4. You can extract the python code by clicking on the "Notebooks" tab as shown here:
   [Notebooks](/azurenotebook2.png)
   
   and download the python code for local editing.
   
5. As you can see above, the python code may contain microsoft proprietary methods.  In the case, 
   "PreFittedSoftVotingRegressor" was used.  Microsoft documentation exists but is currently sparse.
   Documentation can be found here: [PreFittedSoftVotingRegressor](https://docs.microsoft.com/en-us/python/api/azureml-automl-runtime/azureml.automl.runtime.shared.model_wrappers.prefittedsoftvotingregressor?view=azure-ml-py).
   
   I was able too use the more freely available 
   [VotingRegressor](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.VotingRegressor.html)
   with the wild assumption that it was a related set of code.
   
   You will also find that Microsoft's AutoML may generate code that uses some obsolete packages and methods 
   and properties may need either update their code to be compatible with the newer packages or downgrade your
   package references to match the Microsoft code.
   
   I was using Anaconda on my local machine.  In this case, be extremely careful to use CONDA to reference packages
   in your local environment. Using PIP in the wrong instance is the path to a worse DLL than Microsoft ever
   created with COM.
   When in doubt, be sure to restart completely after CONDA changes.
   
   [The python project here on Github](https://github.com/jgrenadier/human) shows how I integrated the Automl generated code into
   a project in the fromazureml.ipnyb code.  The github project is only slightly modified from 
   [Fergus O'Boyles Poverty Prediction using Machine Learning Methods project](https://github.com/FergusOBoyle/sustainable-dev-goals-forecasting).
   
   So, when all was said and done, did AutoML generate an improved model. I think so.  To begin with, the RMSE using O'Boyles
   original unchanged code did not replicate his documentation. I think the problem was that instead of 
   including the input data, he referred to the World Bank data and that data has likely changed over the years.
   But generally, the best models he found were still the best models when I ran his code and my modified model based on
   the AutoML data had a better RMSE than his best model.
   
   In conclusion,  using AutoML to investigate how to improve your existing Python based models is worthwhile.  If nothing else, 
   reviewing the automatically generated AutoML code could help us poor humans learn more how to make better Machine Learning models.
   
   




