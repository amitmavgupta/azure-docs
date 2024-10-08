---
title:  "Convert to CSV: Component Reference"
titleSuffix: Azure Machine Learning
description: Learn how to use the Convert to CSV component in Azure Machine Learning designer to convert a dataset into a CSV file that can be reused later.
services: machine-learning
ms.service: azure-machine-learning
ms.subservice: core
ms.topic: reference

author: likebupt
ms.author: keli19
ms.date: 10/22/2019
---

# Convert to CSV component

This article describes a component in Azure Machine Learning designer.

Use this component to convert a dataset into a CSV format that can be downloaded, exported, or shared with R or Python script components.

### More about the CSV format 

The CSV format, which stands for "comma-separated values", is a file format used by many external machine learning tools. CSV is a common interchange format when working with open-source languages such as R or Python.

Even if you do most of your work in Azure Machine Learning, there are times when you might find it handy to convert your dataset to CSV to use in external tools. For example:

+ Download the CSV file to open it with Excel, or import it into a relational database.  
+ Save the CSV file to cloud storage and connect to it from Power BI to create visualizations.  
+ Use the CSV format to prepare data for use in R and Python. 

When you convert a dataset to CSV, the csv is saved in your Azure Machine Learning workspace. You can use an Azure storage utility to open and use the file directly. You can also access the CSV in the designer by selecting the **Convert to CSV** component, then select the histogram icon under the **Outputs** tab in the right panel to view the output. You can download the CSV from the Results folder to a local directory.  

## How to configure Convert to CSV


1.  Add the Convert to CSV component to your pipeline. You can find this component in the **Data Transformation** group in the designer. 

2. Connect it to any component that outputs a dataset.   
  
3.  Submit the pipeline.

### Results
  

Select the **Outputs** tab in the right panel of **Convert to CSV**, and select on one of these icons under the **Port outputs**.  

+ **Register dataset**: Select the icon and save the CSV file back to the Azure Machine Learning workspace as a separate dataset. You can find the dataset as a component in the component tree under the **My Datasets** section.

 + **View output**: Select the eye icon, and follow the instruction to browse the **Results_dataset** folder, and download the data.csv file.

## Next steps

See the [set of components available](component-reference.md) to Azure Machine Learning. 