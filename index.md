## Introduction

Regression algorithms are a type of machine learning algorithms, we use it to predict the labels with the exact values. I have disccussed some regression algorithms in Azure studio in my repository AzureML-Regression. 

In this article we also should create the resource, the compute instances and targets. I have the articles that are very specifically talking about how to step by step create resources and machine learning instances. The respositories names are: AzureML-Startup and AzureML-AutoMLStartup, welcome to review them.

## 1. Setups to ML assignments

In Azure we can perform all of machine learning assignments. Before implementing the assignments, we should build up the platforms, which means that we need to create an Azure Machine Learnig workspace, create compute resources and compute targets. 

There are several configurations that should be set in advance.

To create an Azure machine learning workspace:

We need to sign into the Azure portal and create a resource, please configure for the following information:

![image](https://user-images.githubusercontent.com/71245576/115499085-c6ae9600-a23c-11eb-8d05-6bd4d886f925.png)

It could take a few minutes.

Then we need to create compute resources. To train and deploy models using Azure Machine Learning designer, you need compute on which to run the training process, and to test the trained model after deploying it.

There are several compute resource that you can create:

![image](https://user-images.githubusercontent.com/71245576/115499174-f8bff800-a23c-11eb-9553-8b97d1a0b458.png)

On the Compute Instances tab, add a new compute instance with the following settings, and you will use this as the workstation from which to test the models.

![image](https://user-images.githubusercontent.com/71245576/115499250-1beaa780-a23d-11eb-9307-3f3fed64764b.png)

While the compute instance is being created, switch to the Compute Clusters tab, and add a new compute cluster with the following settings. You'll use this to train a machine learning model:

![image](https://user-images.githubusercontent.com/71245576/115499291-33c22b80-a23d-11eb-8c52-1472bf1b7d73.png)

## 2. Exploring the data

To train a regression model we need a dataset that includes historical features and the label to predict.

Now let's create a pipeline. Specifically we need to view the Designer page which is under Author tab and select to create a new pipeline:

![image](https://user-images.githubusercontent.com/71245576/115499587-aa5f2900-a23d-11eb-91a9-6c9add27b042.png)

After opened the pipeline, change the Draft name which is Pipeline-Created-on-Date to Auto Price Training and select a compute target that created previously.

![image](https://user-images.githubusercontent.com/71245576/115499834-22c5ea00-a23e-11eb-8e9b-bdee0d68b1c4.png)

There is a dataset called Automabile price data(Raw) on the left of the interface, drag it to the canvas:

![image](https://user-images.githubusercontent.com/71245576/115499977-720c1a80-a23e-11eb-8f25-0a1a74b11fd8.png)

Right click the dataset in canvas, choose visulize, and click dataset output, we will see the dataset result visualization, we can see the distributions of the various columns as histograms and scroll to the right of the dataset we can see the Price column, which is the label to predict.

![image](https://user-images.githubusercontent.com/71245576/115500151-c2837800-a23e-11eb-95f2-297d6acde9db.png)

Check the details of various columns, for example, the horsepower column:

![image](https://user-images.githubusercontent.com/71245576/115500358-26a63c00-a23f-11eb-966c-658e55291911.png)

You also can select the normalized-losses column header and then review the statistics for this column, this column contains missing values, take care of it.

![image](https://user-images.githubusercontent.com/71245576/115500659-c19f1600-a23f-11eb-8998-7bc435052afd.png)

Other considerations are about data scale types and normalizations, i.e., stroke, peak-rpm and city-mpg columns. We can typically deal with it through normalization.

## 3. Data transformations

Data transformation is a process that includes typically in data ETL, sometimes we usually discuss it related and referred to data preparation and data preprocessing. 

In automatic machine learning, you can typically apply data transformations to prepare the data for model, as the tutorial said.

In the pane on the left, expand the Data Transofrmation section which contains a wide range of modules that we can use.

As I can see, there are 19 types of functionalities that we can choose to transform data:

![image](https://user-images.githubusercontent.com/71245576/115501091-923cd900-a240-11eb-9645-df54efc6452e.png)

Drag a Select Columns in Dataset module to the canvas and connect it to the Automobile price data(Raw) module. You can see this if connected:

![image](https://user-images.githubusercontent.com/71245576/115501758-c6fd6000-a241-11eb-9813-b28726002fe3.png)

After that, in Select Columns in Dataset module, select Edit column. Choose to edit by name, add all columns other than normalized-losses:

![image](https://user-images.githubusercontent.com/71245576/115587821-18d2d400-a29c-11eb-8e2a-967a4817f22e.png)

Now let's clean missing data, drag a Clean Missing Data modele from the Data Transformations section and place it under the Select Columns. Click Edit column in the settings pane and then select With rules, select Column names, in the box of column names enter bore, stroke and horsepower.

![image](https://user-images.githubusercontent.com/71245576/115589052-67cd3900-a29d-11eb-8b0c-12c7378602a9.png)

In the settings pane of Clean Missing Data module, set the minimum missing value ratio and maximum missing value ratio and cleaning mode.

![image](https://user-images.githubusercontent.com/71245576/115589573-fd68c880-a29d-11eb-88a3-52f10d398f49.png)

Cleaning is finished, now let's normalize the data, drag a Normalize Data module to the canvas, below the Clean Missing Data module. Then connect the left-most output from the Clean Missing Data module to the input of the Normalize Data module.

And select the column to be normalized:

![image](https://user-images.githubusercontent.com/71245576/115590459-fe4e2a00-a29e-11eb-9b03-db8d3f45c0dc.png)

Now your pipeline could be like this:

![image](https://user-images.githubusercontent.com/71245576/115590567-1c1b8f00-a29f-11eb-94d8-61bdc620d05e.png)

Run the pipeline, select Submit and run the pipeline as a new experiment named mslearn-auto-training on the compute cluster:

Wait for all assignments to be finalized:

![image](https://user-images.githubusercontent.com/71245576/115591946-ab757200-a2a0-11eb-8c4c-3bd87ac342b2.png)

View the transformed data. Select like the completed Normalize Data module and in its Settings pane on the right, select the Visualize icon for the transformed dataset. You could see the normalied data result visualization like this:

![image](https://user-images.githubusercontent.com/71245576/115592554-5b4adf80-a2a1-11eb-9fde-4b627b77681c.png)

## 4. Training data

After we transformed the data we can use a regression model to train the data:

It is common practice to train the model using a subset of the data, while holding back some data with which to test the trained model. Now drag a Split Data model onto the canvas under the Normalize Data module.

Configure the Split Data settings:

![image](https://user-images.githubusercontent.com/71245576/115592909-d613fa80-a2a1-11eb-9b53-156920c4b6b5.png)

Expand the Model Training section in the pane on the left and drag a Train Model module to the canvas under the Split Data module. Then connect the Result dataset1 (left) output of the Split Data module to the Dataset (right) input of the Train Model module.

Modify the settings of Train Model module to set the Label column to price:

![image](https://user-images.githubusercontent.com/71245576/115593383-6f431100-a2a2-11eb-9631-745dc624233f.png)

The price label the model will predict is a numeric value so that we need to train the model using a regression algorithm. Now drag a Linear Regression module to the canvas to the left of the Split Data module and above the Train Model module. Then connect its output to the Untrained model (left) input of the Train Model module.

To test the trained model we need to use it to score the validation dataset we held back when we split the original data. Expand the Model Scoring & Evaluation section and drag a Score Model module to the canvas below the Train Model module. Then connect Train Model module and the Score Model module.

The pipeline now is like this:

![image](https://user-images.githubusercontent.com/71245576/115594190-73236300-a2a3-11eb-9eb6-1d39956712b1.png)

Now submit the pipeline to run it:

![image](https://user-images.githubusercontent.com/71245576/115596754-7a983b80-a2a6-11eb-871d-ce1c4086a6e2.png)

When the experiment run has completed, select the Score Model module and in the settings pane, on the Outputs + logs tab, under Data outputs in the Scored dataset section, use the Visualize icon to view the results.

![image](https://user-images.githubusercontent.com/71245576/115596919-a9161680-a2a6-11eb-9374-0e51653c6ce7.png)

## 5. Evaluating the regression model

The model is predicting values for the price label, but how reliable are its predictions? To assess that, you need to evaluate the model.

Drag an Evaluate Model module to the canvas, under the Score Model module, and connect the output of the Score Model module to the Scored dataset (left) input of the Evaluate Model module.

Submit it and wait it for running the pipeline:

![image](https://user-images.githubusercontent.com/71245576/115597339-293c7c00-a2a7-11eb-9751-33a5c6724ce0.png)

Select the Evaluate Model module and in the settings pane, on the Outputs+logs tab use the Visualize icon to view metrics:

![image](https://user-images.githubusercontent.com/71245576/115598277-3c9c1700-a2a8-11eb-9824-4cf81e5af428.png)

## 6. Creating an inference pipeline

After running a pipeline to train the model you need a second pipeline that can use the trained model to predict for new observations, this is inferencing, now let's create and run an inference pipeline.

In the Create inference pipeline drop-down list, click Real-time inference pipeline. After a few seconds, a new version of your pipeline named Auto Price Training-real time inference will be opened.

Replace the Automobile price data (Raw) dataset with an Enter Data Manually module.

Enter the data in Enter Data Manually module, the data are 3 rows:

```excel
symboling,normalized-losses,make,fuel-type,aspiration,num-of-doors,body-style,drive-wheels,engine-location,wheel-base,length,width,height,curb-weight,engine-type,num-of-cylinders,engine-size,fuel-system,bore,stroke,compression-ratio,horsepower,peak-rpm,city-mpg,highway-mpg
3,NaN,alfa-romero,gas,std,two,convertible,rwd,front,88.6,168.8,64.1,48.8,2548,dohc,four,130,mpfi,3.47,2.68,9,111,5000,21,27
3,NaN,alfa-romero,gas,std,two,convertible,rwd,front,88.6,168.8,64.1,48.8,2548,dohc,four,130,mpfi,3.47,2.68,9,111,5000,21,27
1,NaN,alfa-romero,gas,std,two,hatchback,rwd,front,94.5,171.2,65.5,52.4,2823,ohcv,six,152,mpfi,2.68,3.47,9,154,5000,19,26
```

See the configuration:

![image](https://user-images.githubusercontent.com/71245576/115608904-d9fd4800-a2b4-11eb-8aea-dc2bf655cee9.png)

Connect the new Enter Data Manually module to the same dataset input of the Select Columns in Dataset module as the Web Service Input.

Now we have changed the schema of the incoming data to exclude the price field, we need to remove any explicit uses of this field in the remaining modules. Select the Select Columns in Dataset module and then in the settings pane, edit the columns to remove the price field.

The inference pipeline includes the Evaluate Model module, which is not useful when predicting from new data, so delete this module.

The output from the Score Model module includes all of the inpuut features and the label, to modify it we need to only include the prediction. 

Specifically, delete the connect between the Score Model module and the Web Service Output. Add an Execute Python Script module with the following code:

```python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    scored_results = dataframe1[['Scored Labels']]
    scored_results.rename(columns={'Scored Labels':'predicted_price'},
                        inplace=True)
    return scored_results
```
Connect the output from the Score Model module to the Dataset1 (left-most) input of the Execute Python Script, and connect the output of the Execute Python Script module to the Web Service Output.

Now your pipeline would be like this:

![image](https://user-images.githubusercontent.com/71245576/115608569-71ae6680-a2b4-11eb-99d3-3d3cdbbc7055.png)

Now submit it and create a new experiment named mslearn-auto-inference on the compute cluster, it will take a while. When the pipeline has completed, select the Execute Python Script module and in the settings pane on the Output+logs tab, visualize the Result dataset to see the prediced prices for the three cars in the input data:

![image](https://user-images.githubusercontent.com/71245576/115625116-99f49000-a2c9-11eb-8ded-6aea0fd6d2ca.png)


## 7. Deploying a predictive service

After you've created and tested an inference pipeline for real-time inferencing, you can publish it as a service for client applications to use.

View the Predict Auto Price inference pipeline you created in the previous unit. Select Deploy and deploy a new real-time endpoint, configure the settings:

![image](https://user-images.githubusercontent.com/71245576/115625518-2c952f00-a2ca-11eb-872a-dff339afc537.png)

Wait for finishing. After it has completed, we can test the service: on the Endpoints page, open the predict-auto-price real-time endpoint, view the Consume tab and note the REST endpoint and Primary Key for your service; Copy them to the clipboard.

![image](https://user-images.githubusercontent.com/71245576/115625904-b80ec000-a2ca-11eb-9ed3-a95fd4fd4243.png)

Open a second instance of Azure ML studio and view the Notebooks page. Under My files, create a new file and copy the following code to the file:

```python
endpoint = 'YOUR_ENDPOINT' #Replace with your endpoint
key = 'YOUR_KEY' #Replace with your key

import urllib.request
import json
import os

# Prepare the input data
data = {
    "Inputs": {
        "WebServiceInput0":
        [
            {
                    'symboling': 3,
                    'normalized-losses': None,
                    'make': "alfa-romero",
                    'fuel-type': "gas",
                    'aspiration': "std",
                    'num-of-doors': "two",
                    'body-style': "convertible",
                    'drive-wheels': "rwd",
                    'engine-location': "front",
                    'wheel-base': 88.6,
                    'length': 168.8,
                    'width': 64.1,
                    'height': 48.8,
                    'curb-weight': 2548,
                    'engine-type': "dohc",
                    'num-of-cylinders': "four",
                    'engine-size': 130,
                    'fuel-system': "mpfi",
                    'bore': 3.47,
                    'stroke': 2.68,
                    'compression-ratio': 9,
                    'horsepower': 111,
                    'peak-rpm': 5000,
                    'city-mpg': 21,
                    'highway-mpg': 27,
            },
        ],
    },
    "GlobalParameters":  {
    }
}
body = str.encode(json.dumps(data))
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ key)}
req = urllib.request.Request(endpoint, body, headers)

try:
    response = urllib.request.urlopen(req)
    result = response.read()
    json_result = json.loads(result)
    y = json_result["Results"]["WebServiceOutput0"][0]["predicted_price"]
    print('Predicted price: {:.2f}'.format(y))

except urllib.error.HTTPError as error:
    print("The request failed with status code: " + str(error.code))

    # Print the headers to help debug the error
    print(error.info())
    print(json.loads(error.read().decode("utf8", 'ignore')))
```

The predicted price is 14997.01:

![image](https://user-images.githubusercontent.com/71245576/115626375-6ca8e180-a2cb-11eb-936e-8e63c7fda987.png)


## Reference

Create a Regression Model with Azure Machine Learning designer, retrieved from https://docs.microsoft.com/en-us/learn/modules/create-regression-model-azure-machine-learning-designer/
