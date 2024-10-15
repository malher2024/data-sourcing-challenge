# Data Sourcing Challenge for NOAA Space Weather Prediction Center #

## Overview ##

You have been tasked with preparing a dataset to help the NOAA Space Weather Prediction Center predict Geomagnetic Storms (GSTs). 
Geomagnetic Storms are caused by Coronal Mass Ejections (CMEs), which are massive bursts of plasma emitted from the Sun.
These storms can affect electronic devices such as satellites and GPS systems, and can also create the Northern and Southern Lights.

This dataset will be used to improve prediction accuracy for these storms, which can help mitigate their impact on critical infrastructure.

However, these predictions are far from perfect, as you can see from this table showing the prediction accuracy of the forecasters responsible for NOAA’s Space Weather PredictionLinks to an external site..

For this purpose, you'll extract data from the NASA API, specifically from two sources—its GST data and its CME data—then merge the data together and compute the average time it takes for a CME to cause a GST. Later, this data can be used with Machine Learning models to create predictions.


## Files ##
Download the following files to help you get started:

Module 6 Challenge filesLinks to an external site.

# Before You Begin #
Before starting the Challenge, be sure to complete the following steps:

Create a new repository for this project called data-sourcing-challenge. Do not add this homework assignment to an existing repository. When creating your new repository, under the "Add .gitignore" option, make sure you select "Python". This will prevent you from accidentally uploading your API keys in your .env file, exposing them to the world.

Clone the new repository to your computer.

Inside your local Git repository, add the starter files retrieve_data_solution.ipynb and example.env from your file downloads.

Rename example.env to .env and add your API key to the file.

Ensure the .env file is not listed when you perform a git status check on the repo, before performing your git add action.

Push these changes to GitHub or GitLab.

# note #
The CSV file included in the output folder in your starter files is to help you identify how your final CSV file should be structured. Do not copy this file to your own repo. You will instead upload the CSV file you create as part of the Challenge.

## Instructions ##
This Challenge has three parts, and must be completed in order:

Part 1: Request CME data from the NASA API.

Part 2: Request GST data from the NASA API.

Part 3: Merge and Clean the Data for Export.

The starter code includes importing the required dependencies and your API key from your .env file, but you will need to ensure your API key is added to that file.

# Part 1: # Request CME data from the NASA API
The base URL is included in the starter code, along with the search string and query dates. Consult the NASA API documentationLinks to an external site. to help you build your query_url using these variables.

If you accidentally delete these variables, they are:

# Set the base URL
base_url = "https://api.nasa.gov/DONKI/"

# Set the specifier for CMEs:
specifier = "CME"

# Search for CMEs between a begin and an end date
startDate = "2013-05-01"
endDate   = "2024-05-01"
Make a GET request for the CME URL and store it in a variable named cme_response, then convert the response variable to JSON and store it as a variable named cme_json.

Preview the first results in JSON format using json.dumps with the argument indent=4 to format the data.

Convert cme_json to a Pandas DataFrame and keep only the activityID, startTime, and linkedEvents columns.

The linkedEvents column allows us to identify the corresponding GST. Remove the rows with missing 'linkedEvents', since we won't be able to assign these to GSTs.

Note that the linkedEvents column sometimes contains multiple events per row. Write a nested for loop that first iterates over each row in the cme DataFrame (using the index), and then iterates over the values in linkedEvents and adds the elements individually to a list of dictionaries, where each row is one element.

# Initialize an empty list called expanded_rows to store the expanded rows #
expanded_rows =
# Iterate over each index in the DataFrame #
for i in cme.index:
                             # Get the corresponding value from row i in 'activityID'
                             # Get the corresponding value from row i in 'startTime'        
                             # Get the list of dictionaries from row i in 'linkedEvents'
    
    # Iterate over each dictionary in the list
    for item in linkedEvents:
        # Append a new dictionary to the expanded_rows list for each dictionary item and corresponding 'activityID' and 'startTime' value
         
# Create a new DataFrame from the expanded row #
Create a function called extract_activityID_from_dict that takes a dict as input, such as in linkedEvents, and verify below that it works as expected, using one row from linkedEvents as an example. Be sure to use a try-except block to handle errors:

    def extract_activityID_from_dict(input_dict):
        try:

            return
        except (ValueError, TypeError) as e:

            return 

    extract_activityID_from_dict(cme.loc[0,'linkedEvents'])
Apply this function to each row in the linkedEvents column (you can use apply() and a lambda function) and create a new column called GST_ActivityID using loc indexer:

Remove rows with missing 'GST_ActivityID', since we can't assign them to GSTs.

Convert the GST_ActivityID column to string format. Convert startTime to datetime format and rename it startTime_CME.

Rename startTime to startTime_CME and activityID to cmeID. Drop linkedEvents.

We are only interested in CMEs related to GSTs, so keep only the rows in which the GST_ActivityID column contains 'GST'. Use the method contains() from the str library.

# Part 2: # Request GST data from the NASA API
The base URL is included in the starter code, along with the search string and query dates. Consult the NASA API documentationLinks to an external site. to help you build your query_url using these variables.

If you accidentally delete these variables, they are:

# Set the base URL #
base_url = "https://api.nasa.gov/DONKI/"

# Set the specifier for Geomagnetic Storms (GST): #
specifier = "GST"

# Search for GSTs between a begin and end date #
startDate = "2013-05-01"
endDate   = "2024-05-01"
Make a GET request for the GST URL and store it in a variable named gst_response, and then convert the response variable to JSON and store it as a variable named gst_json.

Preview the first results in JSON format using json.dumps with the argument indent=4 to format the data.

Convert gst_json to a Pandas DataFrame and keep only the activityID, startTime, and linkedEvents columns.

The linkedEvents column allows us to identify the corresponding CME. Remove the rows with missing 'linkedEvents', since we won't be able to assign them to CMEs.

Note that the linkedEvents column sometimes contains multiple events per row. Use the explode() method to spread those events out into individual rows.

Apply the previously created extract_activityID_from_dict, as before, to each row in the linkedEvents column (you can use apply() and a lambda function) and create a new column called CME_ActivityID using the loc indexer.

Remove the rows with missing 'CME_ActivityID', since we can't assign them to CMEs.

Convert the gstID column to string format, then convert startTime to datetime format and rename it startTime_GST.

Rename startTime to startTime_GST and drop linkedEvents.

We are only interested in GSTs related to CMEs, so keep only the rows in which the CME_ActivityID column contains 'CME'. Use the method contains() from the str library.

#Part 3: Merge and Clean the Data for Export
Now that you've collected the data for both events, you need to merge the two DataFrames, clean the data, and then export it for future use. Notice that both DataFrames have observations linked to multiple events, i.e., each CME can be linked to multiple GSTs and each GST can be linked to multiple CMEs. Each DataFrame will thus have duplicate observations to account for this type of relationship. In order to merge these DataFrames correctly, we need to merge on all four ID columns (you can verify this by conducting the merge on a small subset).

Merge both datasets using gstID and CME_ActivityID for GST, and GST_ActivityID and cmeID for cme.

Verify that the new DataFrame has the same number of rows as the cme and gst DataFrames.

Compute the time difference between startTime_GST and startTime_CME by creating a new column called timeDiff.

Use describe() to compute the mean and median time.

Export data to a CSV file without the DataFrame's index.


