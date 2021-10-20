Title: Data Vault's Biggest Value Add
Date: 2021-10-18
Category: how tos
Status: draft

It might be safe to say that every data practitioner has heard about the star schema for data modeling, and perhaps even the snowflake schema. These approaches to data modeling "normalize" the database by reducing redundant data in each table, so that it lives separately. 

For example, here's a simple snowflake model for a commerce application.  

### Enter... the Data Vault
The data vault approach supercharges these normalized schemas by allowing for historical tracking of records.   

- Rather than fact tables with dimensions, we have hubs with satellites, each with a unique link. The satellites can be further normalized, and the links are used to keep track of their mutability.
- _All_ records are stored, both good and bad, so that no information is lost.
- Transformations of the data are made on the way out of the vault rather than when it comes in. (Sound familiar?)
- And finally, hubs and satellites have a field for the **loaded time stamp**, as well as the **source** of the record.  

There's certainly a lot more to chew on with this approach, which you can read about [here](http://danlinstedt.com/solutions-2/data-vault-basics/). Let's redesign that snowflake commerce model to be more in line with the data vault's historical tracking.  

#### ... It's saved my butt before!
Recently I was working on a project with a batch etl pipeline. One of our users raised a red flag to us that they were seeing unexpected behavior with one of the entities in our system, that could very well have been going on for a while. 

Using some of the example units provided, our team was able to identify the individual records that were likely causing the issue, as well as their respective sources & when they entered the warehouse. It became clear quite quickly that there was a pattern in the type of sources that were causing the issue!

This approach is just one way to bring some much needed traceability to our modern data pipelines.