# Setting up a WordPress blob on Azure (Late 2019)
I'm going to set up a personal blob running wordpress hosted on Azure. I have a couple of goals. The site needs to be relatively performant, cost under $50 a month, and have automated deployment from github.

I'll be writing this post as I go through the process of setting up the website. The last time I set up a wordpress blob was while I was in highschool, so its been over 10 years. I work with Azure daily but most of my recent experience has been with data pipelines and bib data systems. This will be a learning experience for both of us.

## Software I'm using for this
- VS Code (Mac)
- MySQL Workbench https://www.mysql.com/products/workbench/
- GitHub Desktop Client (Mac) 

## Other needed things
- Wordpress code (5.2.4) https://wordpress.org/download/
- Azure Subscription https://azure.microsoft.com/
- Github account 

That should be about it.

# Setting up Azure Environment
I'll only be setting up one environment. Normally I'd have a dev and production environment with automated deployment using ARM templates. However given this is a personal website, we won't go that far yet.

First step is creating a resource group. Log into portal.azure.com and click the hamburger menu. From there click create a resource. In the search box type resource group.

For this we'll name it "last-resort-prod" and create it within the US Central region.

### A note on resource groups
Resource groups can be thought of as folders in Azure. They're just a logical grouping of resources. The region the resource group is deployed in is not super important and will not have any impact on the performance of the resources within it. The resources within it can also be created in regions different than the resource group itself. The resource groups do store some metadata about the resources within them, so if there is an outage in the region the resource group is deployed to, it can prevent you from managing the resources within it, however the underlying resources will not be affected by the outage if thier region is not having an outage.

After the resource group is created we'll follow the same flow but this time after clicking create resource, in the search box type "Web App", then create.

The settings are listed below. I went with linux and selected the lowest price tier which has the always "Always On" setting. Without always on Azure will shut down the app service in periods of downtime, when this happens Azure is forced to start the app service again when a request is recieved. This can result in 10+ second delays when someone tries to load the site for the first time after a period of not being visted. You can see my settings below.

Finally we'll create the MySQL DB. There are cheaper options available for MySQL servers hosted on Azure, but I am looking for the easiest to manage option so I will be using Azure Database for MySQL.

Again, click create resource, this time searching for "Azure Database for MySQL," click create. Select the resource group you previously created. Give your MySQL server a name. Select your size, I'll be using the most basic plan with the most limited storage and VCore options. You can always upgrade this later if needed. Here are the settings below (security related fields are blocked out for obvious reasons.)

## Installing Wordpress
Now that I have all my Azure resources I'm going to get started installing Wordpress.

First I'll create a database on the MySQL server for use with WordPress.

I'm using MySQLWorkbench as a GUI for managing the database. Once the application is open click add connection.

You'll need the username and password you used when creating the SQL server. Open the MySQL server in the portal to get the host name and and server admin login.

Use these values in the add connection screen in MySQLWorkbench. Leave the port value as-is. Click test connection to verify the values are correct.

### If test connection failes (How to add a firewall rule for your client)
If the connection fails the most likely reason is you need to add a firewall rule for yourself. Go back to your MySQL server in Azure and click connection security. From there click Add client IP. This will add your computers IP to the list of allowed IPs. Once your IP is added click save. Test the connection again.

Once you have a connection open the query window and execute 
CREATE DATABASE **yourWordPressDbNameHere**;

