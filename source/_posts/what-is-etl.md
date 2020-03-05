---
title: What is ETL?
url: 1209.html
id: 1209
categories:
  - Methodology
date: 2016-04-29 08:00:34
tags:
---

In this post, we will go over the general idea being ETL, what the steps are, and the general idea behind how it is tested.  ETL stands for Extract, Transform, and Load.   _Extract_refers to collecting data from data sources.  _Transform_ is process of formatting the data such that it is in the correct structure for querying and analysis.  Finally, _load_refers to loading the clean and formatted data into the target system. This process is often used in data warehousing and data modeling.  A _d__ata warehouse _(also known as DW or DWH) is a system used for reporting and analyzing data.  Other acronyms include ETT (extract, transform, transport) and ETM (extract, transform, move).

### Example

Lets say you are in charge of a large property management firm which leases out many types of buildings (retail, restaurants, and apartments).  One person is in charge of all of the retail buildings, a different person in charge of all of the restaurant buildings, and a third person in charge of all of the apartment buildings.  Each of these people set up separate databases to store all of their information.  These people might have used different database technologies, they may have chosen different naming conventions for their tables and columns, etc. Since you are the manager over _all_ of the buildings, you are interested in analyzing historical data and generating reports from all of these different data sources.  An ETL tool will extract all of this data, transform it (by doing things like calculations, joining fields, removing data fields, etc), and will finally load it into your Data Warehouse.  After this is all completed, you can use your preferred Business Intelligence (BI) tool to generate your report (maybe [Qlik](http://www.techtrek.io/an-introduction-to-qlik-and-sqlite/) or [SSRS](http://www.techtrek.io/an-introduction-to-sql-server-reporting-services-ssrs/)).

### Common Tools

Some common ETL tools include [SAP BO Data Services (BODS)](http://help.sap.com/bods40), [PowerCenter](https://www.informatica.com/products/data-integration/powercenter.html#fbid=4aTQ6nFbQXs), and [SQL Server Integration Services (SSIS)](https://msdn.microsoft.com/en-us/library/ms141026.aspx). Common Business Intelligence tools include [SAP Business Objects](https://www.sapbi.com/), [SAP Lumira](http://go.sap.com/product/analytics/lumira.html), [Microsoft BI Platform](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwj42Z_t4KrMAhXnuoMKHbx9BWYQFggpMAA&url=https%3A%2F%2Fpowerbi.microsoft.com%2F&usg=AFQjCNExvoS5uwYbcnONyLUPclWQN7HzFg&sig2=iT4vbU0pCsiB96SLNF52jQ), and [Tableau](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjr_Kn14KrMAhWJn4MKHRdZCHwQFgg7MAA&url=http%3A%2F%2Fget.tableau.com%2Fcampaign%2Fbusiness-intelligence.html&usg=AFQjCNFHBuOxTuoF9zMa8YeCH7jJV9nXTA&sig2=1_pHOc7ptqXanJ5lT_NqZw&bvm=bv.120551593,d.amc).

### ETL - Extract

The first step in the ETL process is to extract the data.  The extraction process will vary depending on the ETL tool and the data source in question, but it is usually done by running scheduled jobs during off-business hours.  For our example, this might mean running the job over night or in the early morning hours.

### ETL - Transform

The next step is to transform the data so that it can easily be loaded up into our Data Warehouse.  For example, we might have first name and last name of each landlord for each of our buildings stored in separate columns.  A transformation operation might be to concatenate these columns before we load the data so that on the other end we just have the full names of all of our landlords. It is important to note that not all of the data will have an operation applied to it.  If we don't transform the data, it is called a **direct move** or **pass through data**.

### **ETL - Load**

The last operation is to load.  This usually happens over three layers:

*   Staging Layer
    *   Also known as the _staging database_, this area is used to store the data that we extracted from the different sources
*   Data Integration
    *   In this layer, the data is moved into a database where it is arrange into groups (also known as **dimensions**) and into **facts** and **aggregate facts**.  A fact table holds the data that will be analyzed and the dimension table stores the ways that the data in the facts table can be analyzed.  The combination of these facts and dimensions make up the **schema**.
*   Access
    *   In this layer, end-users can retrieve the data so that they can analyze or report the information.

### Testing

**ETL testing** is done to try to identify and mitigate data defects and errors that might occur when a person tries to analyze or report on the data.  It is usually done on data in the data warehouse.  Some general tasks that occur during ETL testing might include:

*   Validate the data movement from the source to the target
*   Verify the data count in the source and the target
*   Verify that the data extraction and transformations are what you expect
*   Verify that the table relationships (joins, primary keys, etc) are preserved

Some tools that are used for this include QuerySurge and Informatica. **Database testing** also involves data validation but it is different than ETL testing.  With database testing, we usually are testing data where the data sources come from different applications and into a transactional database.  We will typically be looking for more accuracy with this.  The general tasks that occur might include:

*   Verify that the primary and foreign keys are preserved
*   Verify that the columns have valid data values
*   Verify the accuracy in the data and that it all makes sense
*   Verify missing values (looking for NULL values where they should or shouldn't be).

Some tools for this include [Selenium](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwju8KSijLTMAhVF6CYKHb5HBU0QFggcMAA&url=http%3A%2F%2Fwww.seleniumhq.org%2F&usg=AFQjCNG0l4NC_Rls4vtRgDkPoYl1H5FG4A&sig2=xghQZfgDF_tuoa-kSvg7ww&bvm=bv.120853415,d.eWE) and [QTP](http://www.tutorialspoint.com/qtp/qtp_overview.htm). _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._