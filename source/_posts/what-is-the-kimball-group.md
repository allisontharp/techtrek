---
title: What Is The Kimball Group?
tags:
  - datawarehouse
url: 1753.html
id: 1753
categories:
  - Methodology
date: 2016-09-27 08:00:50
---

The Kimball Group is a team of people that specialize in designing data warehouses for business intelligence.  They came up with a methodology for designing data warehouses and other general business intelligence methodologies.

### What Is A Data Warehouse?

A data warehouse takes data from a company's databases and optimizes it for analytics.  Data warehouses are needed because databases are not written or structured to do analytics efficiently (instead, they are structured to handle a lot of transactions).  Data warehouses store current and historical data from one or multiple sources with the end goal of doing analysis and generating reports.

### Kimball DW/BI Lifecycle Methodology

The Kimball Lifecycle Methodology is similar to [Agile Software Development](http://www.techtrek.io/what-is-agile-software-development/) for business intelligence and data warehouses.  At its core, the methodology strives for the following:

*   Focus on adding business value across the enterprise
*   Dimensionally structure the data that's delivered to the business
*   Iteratively develop the data warehouse / business intelligence environment in manageable lifecycle increments rather than one big step

In general, when working on developing a data warehouse, the first step will be getting the project launched.  During this **project planning** step, the scope, justification, and staffing issues will be discussed and determined. Next, the **business requirements** will need to be defined.  This is a key step in the Kimball Lifecycle because the findings in this step will drive a lot of the decisions later on.  During these meetings, the team will try to determine what key factors impact the business and their users today and potentially in the future.  These requirements should be detailed and used to build the data warehouse (instead of simply asking the business 'what do you want to be in your new data warehouse?'). After the business requirements are addressed, three tracks will occur simultaneously:

*   **Technical Architecture Design** \- This track starts with a system architecture design so that the team can get an idea of what capabilities will be needed.  After this is determined, the team will select and install the products.
*   **Dimensional Modeling** \-  In this track, the team will design a dimensional model that addresses the business' needs.  There are always at least two goals: ease of use for the user and fast query performance.  The dimensional model will then be converted to a physical design.  Once in a physical design, performance tuning may occur and [ETL](http://www.techtrek.io/what-is-etl/) tasks begin to occur.
*   **BI Application Design **\- In this track, team members are focused on designing standardized reports, parameterized queries, dashboards, etc to help view the end data.

At the end of these three concurrent tracks are **Deployment**, **Growth**, and **Maintenance**.  The goal of the Kimball methodology, again, is to do this in small pieces.  During the Growth step, the team will likely go back to the first step (Program Planning) and redo all of the steps.  The following illustration, from the Kimball website, illustrates the lifecycle: ![](http://www.kimballgroup.com/wp-content/uploads/2012/06/kimball-core-concepts-021.png "kimball-core-concepts-02") The [Kimball website](http://www.kimballgroup.com/) has a lot of resources if you or your team are interested in developing or improving a data warehouse. I am interested in learning more about the strategies to creating a good data warehouse because I think it will naturally lead to understanding how to design better databases.  I think the Kimball website will be an integral tool in that journey.   _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._