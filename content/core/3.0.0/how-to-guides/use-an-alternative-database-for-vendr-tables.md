---
title: Use an Alternative Database for Vendr Tables
description: How-To Guide to configure using an alternative database for the tables of Vendr, the eCommerce solution for Umbraco
---

By default Vendr will use the same database as Umbraco to store it's data in, but as e-commerce and content management have very different database needs, it may be benefitial to house the Vendr database tables in an alternative database.

To do this, you can configure a Vendr specific connection string in your app settings `ConnectionStrings` section using the `vendrDbDSN` prefix.

````json
{
    ...
    "ConnectionStrings": {
        "umbracoDbDSN": "Server=umbracoServerAddress;Database=myUmbracoDb;User Id=myUsername;Password=myPassword;",
        "umbracoDbDSN_ProviderName": "Microsoft.Data.SqlClient",
        "vendrDbDSN": "Server=vendrServerAddress;Database=myVendrDb;User Id=myUsername;Password=myPassword;",
        "vendrDbDSN_ProviderName": "Microsoft.Data.SqlClient"
    },
    ...
}

````

Now when Vendr runs, it will perform all of it's migrations and opperations against this database instead of the default Umbraco database.

