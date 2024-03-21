# AZURE-DATAENGINEERING-END-TO-END-PROJECT

The tools that are covered in this project are,

1. SQL server migration 
2. Azure Data Factory
3. Azure Data Lake Storage Gen2
4. Azure Databricks
5. PYSPARK
6. SPARK SQL
7. Microsoft Power BI

In Azure Cloud, there are many ways to import data depending upon the source of data, size of data, specific service you prefer to use, existing infrastructure, specific requirements, and preferences.
**Step 1 **

**CREATE DATABASE IN My SQL WorkBench**
CREATE DATABASE sales;
USE sales;

DATABASE NAME: sale
right CLICK>  Expand Databases, right-click a database (test in the example below), point to Tasks, and click Import Flat File above Import Data.

SELECT * FROM sale.pizza_sales;(SelectPizzasale.png)
"The data needs to be transferred to cloud storage."
Now we need to open azure cloud 
**signup Azure account First **
https://azure.microsoft.com/en-us/free/cloud-services/search/?ef_id=_k_Cj0KCQjwqdqvBhCPARIsANrmZhPaFblMgmBumsootUo-hhTKu2VrwcztAWwHpFkGJKungy3xGAR9eloaAr3TEALw_wcB_k_&OCID=AIDcmm5edswduu_SEM__k_Cj0KCQjwqdqvBhCPARIsANrmZhPaFblMgmBumsootUo-hhTKu2VrwcztAWwHpFkGJKungy3xGAR9eloaAr3TEALw_wcB_k_&gad_source=1&gclid=Cj0KCQjwqdqvBhCPARIsANrmZhPaFblMgmBumsootUo-hhTKu2VrwcztAWwHpFkGJKungy3xGAR9eloaAr3TEALw_wcB
Sign up Start Free> You can use Microsoft email to log in>next >You have to insert your card number address and every detail asked by Microsoft Azure then you will be able to log in once confirmed by Microsoft Azure. 

*****************************************************************************************************************************************************************
**Create Storage Account, Container, upload a file**
**************************************************************************************************************************************************************
There are 4 storage services>blob, file, table, and queue.
Based on requirements we used this storage service.
************************************************************************************************************************************************************************
**blob storage**>Requiremnent where data is unstructured format such as video file, audio file, txt file, doc
**File Storage**(Shared location)> E.g. many teams working together on the same project but from different locations such as Nepal, India, USA, etc. You want to share location files where everyone can access upload, and make changes.
**Table Storage** :Structure Data 
**Queue Storage** Put files in the queue and process them one by one. That kind of scenario uses Queue storage.
***********************************************************************************************************************************************************************

**Account type**
**Blob
General purpose v1
General purpose v2(Azure recommendation)**

***************************************************************************************************************************************************************************

**Access Tier**
**Hot tier:** access data frequently, we store files in the hot tier.

**Cold Tier**: Infrequently

**Archive Tier:** Rarely
***************************************************************************************************************************************************************************

**Step 2**

Go to the Search button in  https://portal.azure.com/#home type storage >click on storage account>click create button 
Create a storage account
https://github.com/alizajaz/AZURE-DATA-ENGINEERING-PROJECT/blob/main/AzureStoragewithresourcesstoragename.png
add resources group if you do not have one>storage account name >Review+Create>deployment process will happen >click on> go to resource>you can view an overview of storage >click on container >click on +> create the container>add name and you can give access to private, public>click create  and once a container is successfully created upload a file.>click on file >upload a file >click on file name>edit>preview


**Step 3**

**Create a Data Factory 
**
search data factory >create data factory >fill name,resource group>once deployment is completed go to resource>open Azure Data Factory 

**Step 3**

**Create a Pipeline
**
Open Azure Data Factory >pipelines>pipeline1>copy data 1 >Source>Source dataset>new >choose sql server>new linked service >>Connect via integration runtime*
>Server name*>Username>Password>create

**You can connect with SQL Server.
**
Now we have to connect with Azure Bob storage. (store file as a CSV file )
go to sink >new> azure bob storage>linked list >set properties>file path(I choose output)
My source and sink are ready >publish >add trigger>trigger now>go to monitor(left round kind of blue logo ) >refresh

This pipeline will transfer SQL  server data to bob storage in Azure Cloud.

go to Home Microsoft Azure> storage account> Data Factory>file >go to container>click on file (my is output)>you can find  dbo.pizza_sales.txt

Databricks
https://www.databricks.com/try-databricks/thank-you-community-edition
sign up (enter name and phone number)

After that, we going to connect Databricks
mount data brick to the blob storage 
compute>create the cluster
after that mount data bricks to bob storage
create>notebook>rename notebook file>

dbutils.fs.mount(
    source="wasbs://<container-name>@storage-account-name>.blob.core.windows.net",
    mount_point="/mnt/iotdata",
    extra_configs={"fs.azure.account.key.<storage-account-name>.blob.core.windows.net":dbutils.secrets.get(scope="<scope-name>",key="<key-name>")})
)


for container name: go to the storage account >(storagefir)my storage name>go to container
change storage-name >access key, copy key, and pass name 
once the cluster is ready you can run your code

dbutils.fs.mount(
    source="wasbs://output@storagefir.blob.core.windows.net",
    mount_point="/mnt/output3",
    extra_configs={""})

True


dbutils.fs.ls("/mnt/output3/")
Out[9]: [FileInfo(path='dbfs:/mnt/output3/dbo.pizza_sales.txt', name='dbo.pizza_sales.txt', size=9152440, modificationTime=1710963664000)]


(We are able to fetch our data successfully )

Now let's create data frame 

display(df)

df.createOrReplaceGlobalTempView("pizza_sales_analysis")

# %sql
# select * from pizza_sales_analysis
spark.sql("SELECT * FROM global_temp.pizza_sales_analysis").show()
%sql 
select

count(distinct order_id)order_id,
sum(quantity) quantity,
date_format(order_date,'MMM') month_name,
date_format(order_date,'EEEE') day_name,
hour(order_time) order_time,
sum(unit_price) unit_price,
sum(total_price) total_sales,
pizza_size,
pizza_category,
pizza_name

from global_temp.pizza_sales_analysis
group by 3,4,5,8,9,10
after that you can download file or connect directly from databricks.




















