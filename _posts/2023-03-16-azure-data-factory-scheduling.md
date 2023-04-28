---
layout: post
title: "Azure Data Factory Scheduling"
author: tom
tags: [azure, azure data factory]
comments: true
---

Scheduling jobs in Azure Data Factory.

![Azure Data Factory Scheduling](/assets/images/azure-data-factory-scheduling.png)

## Setup the resources for this tutorial

**Create resources**

* Create a Blob Storage
  * Create two containers `input` and `output`
  * Upload a CSV file to `input`
* Create a Data Factory
  * Create a Linked Service to Blob
  * Create a Dataset for Blob input with the filepath pointing at the CSV uploaded file
  * Create a Dataset for Blob output with filepath pointing at the output container
  * Create a pipeline with a Copy Activity with source/sink set to the input/output datasets
  * Click `Debug` to test the initial setup

**Set parameters for the datasets**

* In the Input dataset
  * Create a parameter `fileName` (String) and replace the `Connection` filepath
* In the Output dataset
  * Create a parameter `fileName` (String) and replace the `Connection` filepath
* In the Copy Activity
  * Set the source `fileName` value as the `file.csv` uploaded to the input blob
  * Set the sink `fileName` value as the same `file.csv` which will be copied to output blob
* In the Pipeline
  * Save all and Publish.
* Click `Debug` to test this setup

***

## Add a Schedule Trigger

Before creating the trigger, make sure the pipeline was Published.

On the pipeline UI, click `Add trigger`, then `New/Edit`

* Click on the `Choose trigger` field and then `New`
* Enter a name like `WeeklySchedule`
* Enter a description like `Run pipeline daily at 5AM UTC`
* Select as Type `Schedule`
  * Other options are `Tumbling window`, `Storage events`, and `Custom events`
* Enter a `Star date`
* In `Recurrence` select an interval
  * For example, Every `1 Day`
* In `Execute at these times`
  * A warning says `Hours must be in the range 0-23 and minutes in the range 0-59. The time specified follows the timezone setting above.`
  * Hours, enter `5`
  * Minutes, enter `0`
  * This populates the field `Schedule execution times 5:00`
* Optionally, select an `end date`
* Keep the default `Start trigger on creation`
* Click `OK`
* Click on `Save all` and `Publish` to take effect.
* The trigger can be found in the `Manage` hub, under `Triggers`

Be aware of the `Recurrence` as this will affect the billing cost of running the pipeline. See [Azure Data Factory Pricing](../azure-data-factory-pricing/).

***

## Add a Storage Event Trigger

You can execute a trigger as soon as files are loaded into Blob Storage.

**Register the resource in your Azure subscription**

* In Azure search for `subscription`
* Select your subscription
* On `Settings/Resource providers`
* Search for `EventGrid`
* Click on `Register`

**Set a Pipeline parameter**

* Click on the pipeline canvas
* Under `Parameters` create one called `fileName` (String)
* In the Copy Activity
  * In `Source`, for the `fileName` parameter, select dynamic content, then select the parameter
  * In `Sink`, do the same, which should be the expression `@pipeline().parameters.fileName`
* Click `Save all`, then `Debug` to test this setup. Then `Publish`

**Create the event trigger**

On the ADF pipeline UI, click `Add trigger`, then `New/Edit`

* Click on the `Choose trigger` field and then `New`
* Enter a name like `NewBlobEventTrigger`
* Enter a description like `Run pipeline as soon as new files are loaded to Storage Blob`
* Select as Type `Storage events`
* Select the `Azure subscription`
* Select the `Storage account name`
* Enter the `Container name` with leading and trailing forward slash such as `/your-container/`
* Enter a `Blob path begins` (optional)
  * `Blob path’ must begin with the path. For example: '2018/april/shoes.csv' or '2018/'`
  * In my case I had two folders in the container `input` and `output`
  * I entered the path as `input/`
* Enter a `Blob path ends` (optional)
  * `‘Blob path’ must end in a file name or extension. For example, 'shoes.csv' or '.csv'. Container and folder name are optional but when specified they must be separated by a '/blobs/' segment. For example '/orders/blobs/2018/april/shoes.csv'. To specify a folder in any container omit the leading '/' character. For example, 'april/shoes.csv'.`
  * I entered `.csv`
* Select the `Event`
  * `Choose which events are associated with this trigger`
  * Select `Blob created`
* Keep the default `Ignore empty blobs` as `Yes`
* Keep the default `Start trigger on creation`
* Click `OK`
* A new window shows `Data preview`
  * `Event trigger filters` show the setup
    * Container name: `scheduletrigger` (this is how I named my container)
    * Starts with: `input/`
    * Ends with: `.csv`
  * Below this said `3 blobs matched in "scheduletrigger"`
    * input/alabama-dbe.csv
    * input/alaska-dbe.csv
    * input/oklahome-dbe.csv
* Click `Continue`
* A new window shows `New trigger`
  * Enter value `@triggerBody().fileName`
    * The `Event Event Grid` API returns a `@triggerBody()`
    * As seen in the MS docs [here](https://learn.microsoft.com/en-us/azure/data-factory/how-to-create-event-trigger).
    * The storage event trigger captures the folder path and file name of the blob into properties
    * `@triggerBody().folderPath`
    * `@triggerBody().fileName`
      * In Synapse use `@trigger().outputs.body.fileName`
* Click `Save all` then `Publish`
* The trigger can be found in the `Manage` hub, under `Triggers`

**Event trigger activates on new blobs**

The trigger won't activate with blobs that were previously uploaded. It will trigger once a new blob with `.csv` is loaded to the container/directory `scheduletrigger/input`.

For example if a new file `file.csv` is added to `scheduletrigger/input`:

* `@triggerBody().fileName` has a value of `file.csv`
* This value is mapped to the pipeline parameter `fileName` in `@pipeline().parameters.fileName`

![Azure Data Factory Storage Event Trigger](/assets/images/azure-data-factory-storage-event-trigger.png)

**Upload a file to the container**

Test the trigger by uploading a `.csv` file to the blob container.

Then go to the `Monitor` hub to see if the pipeline was triggered.

![Azure Data Factory Storage Event Trigger Runs](/assets/images/azure-data-factory-event-trigger-runs.png)

**The trigger doesn't run**

* Make sure that you `Publish` the pipeline
* Check the pipeline parameter expression
* Check the `triggerBody` expression.


This tutorial is based on Adam's video [here](https://www.youtube.com/watch?v=uF3LOCVFHkw) where he explains how to use triggers in ADF.

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)