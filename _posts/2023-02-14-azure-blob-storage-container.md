---
layout: post
title: "Create an Azure Blob Storage and Container"
author: tom
tags: [azure, blob storage]
comments: true
---

How to create an Azure Blob Storage and containers.

## Create a Storage Account

1. Go to the Azure portal
2. Create resource
3. Search for `Storage account`
4. Click on `Create`
5. Select resource group
6. Add a storage account name
7. Select Region closest to you
8. Performance: `Standard`
9. Redundancy: `Locally-redundant storage (LRS)`
10. Create

## Create a container

* Once the storage account is deployed
* Create `Container`
* Enter a name and OK
* Upload a CSV dataset to this container

**More about containers**

* You cannot rename a container. You would have to create a new container and move the data from one to the other
* You cannot directly add a folder inside a container. But you can enter a folder name while uploading the files.

**Preview the CSV file from the container**

* If the file is too big to be previewed from the container. You get this error:
  * Go to the Container
  * Click on the CSV file
  * On the right menu, click `Edit`
  * Error example: `File size of 108.MB exceeds max supported file size of 2.1MB`


[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)