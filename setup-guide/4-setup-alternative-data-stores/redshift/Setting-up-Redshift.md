<a name="top" />

[**HOME**](Home) > [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) > [**Step 4: setting up alternative data stores**](Setting-up-alternative-data-stores) > Setup Redshift

Setting up Redshift is a 9 step process:

1. [Launch a Redshift cluster](#launch)
2. [Authorize client connections to your cluster](#authorise)  
3. [Connect to your cluster](#connect)
4. [Set up the Snowplow database and events table](#db)
5. [Set up user access on Redshift](#user)
6. [Generate Redshift-format data from Snowplow](#generate)
7. [Update the search path for your Redshift cluster](#search_path)
8. [Automate the loading of Snowplow data into Redshift](#load)

**Note**: We recommend running all Snowplow AWS operations through an IAM user with the bare minimum permissions required to run Snowplow. Please see our [IAM user setup page](IAM-setup) for more information on doing this.

<a name="launch" />
## 1. Launch a Redshift Cluster

Go into the Amazon webservices console and select "Redshift" from the list of services.

[[/setup-guide/images/redshift-setup-guide/1.png]]

Click on the "Launch Cluster" button:

[[/setup-guide/images/redshift-setup-guide/2.png]]

Enter suitable values for the cluster identifier, database name (e.g. 'snowplow'), port, username and password. Click the "Continue" button.

[[/setup-guide/images/redshift-setup-guide/3.png]]

We now need to configure the cluster size. Select the values that are most appropriate to your situation. We generally recommend starting with a single node cluster with node type i.e. a `dw1.xlarge` or `dw2.large` node, and then adding nodes as your data volumes grow.

You now have the opportunity to encrypt the database and and set the availability zone if you wish. Select your preferences and click "Continue".

[[/setup-guide/images/redshift-setup-guide/4.png]]

Amazon summarises your cluster information. Click "Launch Cluster" to fire your Redshift instance up. This will take a few minutes to complete.

<a name="authorise" />
## 2. Authorize client connections to your cluster

You authorize access to Redshift differently depending on whether the client you're authorizing is an EC2 instance or not

2.1 [EC2 instance](#ec2)  
2.2 [Other client](#other)

<a name="ec2" />
### 2.1 Granting access to an EC2 instance

TO WRITE

<a name="other" />
### 2.2 Granting access to non-EC2 boxes

To enable a direct connection between a client (e.g. on your local machine) and Redshift, click on the cluster you want to access, via the AWS console:

[[/setup-guide/images/redshift-setup-guide/5.png]]

Click on "Security Groups" on the left hand menu.

[[/setup-guide/images/redshift-setup-guide/6.png]]

Amazon lets you create several different security groups so you can manage access by different groups of people. In this tutorial, we will just update the default group to grant access to a particular IP address.

Select the default security group:

[[/setup-guide/images/redshift-setup-guide/7.png]]

We need to enable a connection type for this security group. Amazon offers two choices: an 'EC2 Security Group' (if you want to grant access to a client running on EC2) or a CIDR/IP connection if you want to connect a clieint that is not an EC2 instance.

In this example we're going to establish a direct connection between Redshift and our local machine (not on EC2), so select CIDR/IP. Amazon helpfully guesses the CIDR of the current machine. In our case, this is right, so we enter the value:

[[/setup-guide/images/redshift-setup-guide/8.png]]

and click "Add".

We should now be able to connect a SQL client on our local machine to Amazon Redshift.

**Note:** Amazon has moved to launching Redshift clusters in a VPC instance by default. In this case, the process for adding IP addresses or EC2 instances to a security group is very similar, but rather than being done in the `Redshift > Security Groups` section of the AWS console, it is done in the `EC2 -> VPC security groups` section of the AWS management console.

<a name="connect" />
## 3. Connect to your cluster

There are two ways to connect to your Redshift cluster:

4.1 [Directly](#directly)  
4.2 [Via SSL](#ssl)  

<a name="directly" />
### 3.1 Directly connect

Amazon has helpfully provided detailed instructions for connecting to Redshift using [SQL Workbench] [sql-workbench-tutorial]. In this tutorial we will connect using [Navicat](http://www.navicat.com/), a database querying tool which we recommend (30 day trial versios are available from the [Navicat website](http://www.navicat.com/)).

**Note: Redshift can be accessed using PostgreSQL JDBC or ODBC drivers. Only specific versions of these drivers work with Redshift**. These are:

* JDBC [[http://jdbc.postgresql.org/download/postgresql-8.4-703.jdbc4.jar]]
* ODBC [[http://ftp.postgresql.org/pub/odbc/versions/msi/psqlodbc_08_04_0200.zip]] or [[http://ftp.postgresql.org/pub/odbc/versions/msi/psqlodbc_09_00_0101-x64.zip]] for 64 bit machines

We've found that many other Postgres clients generally do work with Redshift for *most* queries. (Especially where those queries are being hand crafted rather than generated by the client itself, so that the user can make sure any SQL is Redshift-compatible.)

Open Navicat, select "Connection" -> "PostgreSQL" to establish a new connection:

[[/setup-guide/images/redshift-setup-guide/9.png]]

Give your connection a suitable name. We now need to enter the host name, port, database, username and password. With the exception of password, these are all available directly from the AWS UI. Go back to your browser, open the AWS console, go to Redshift and select your cluster:

[[/setup-guide/images/redshift-setup-guide/10.png]]

Copy the endpoint, port, database name and username into the relevant fields in Navicat, along with the password you created when you setup the cluster:

[[/setup-guide/images/redshift-setup-guide/11.png]]

Click "Test Connection" to check that it is working. Assuming it is, click "OK".

[[/setup-guide/images/redshift-setup-guide/12.png]]

The Redshift cluster is now visible on Navicat, alongside every other database it is connected to.

<a name="ssl" />
### 3.2 Connect via SSL

TO WRITE

<a name="db" />
## 4. Set up the Snowplow database and events table

Now that you have Redshift up and running, you need to create the Snowplow database (if you didn't do this as part of the process of firing up your Redshift cluster) and creating your Snowplow events table.

To create a new database on Redshift, right click on the new connection and select 'New database'. Give your database a suitable name and click OK.

The Snowplow events table definition for Redshift is available on the repo [here] [redshift-table-def]. Execute the queries in the file - this can be done using psql as follows:

Navigate to your snowplow github repo:

	$ cd snowplow

Navigate to the sql file:

	$ cd r-storage/redshift-storage/sql

Now execute the `atomic-def.sql` file:

	$ psql -h <HOSTNAME> -U {{ admin_username }} -d snowplow -p <PORT> -f atomic-def.sql

Where `{{ admin_username }}` is the username you created when you setup the Redshift cluster.

If you prefer using a GUI (e.g. Navicat) rather than `psql`, you can do so. These will let you either run the files directly, or you can simply copy and paste the queries in the files into your GUI of choice, and execute them from there.

<a name="user" />
## 5. Set up user access on Redshift

We recommend you setup access credentials for at least three different users:

1. [The StorageLoader](#storageloader-user)
2. [A user with read-only access](#read-only-user)  to the data, but write access on his / her own schema
3. [A power user](#power-user) with admin privilages

<a name="storageloader-user" />
### 5.1 Creating a user for the StorageLoader

We recommend that you create a specific user in Redshift with *only* the permissions required to load data into your Snowplow events table, and use this user's credentials in the StorageLoader config to manage the automatic movement of data into the table. (That way, in the event that the server running StorageLoader is hacked and the hacker gets access to those credentials, they cannot use them to do any harm to your data.)

To create a new user with restrictive permissions, log into Redshift, connect to the Snopwlow database and execute the following SQL:

```sql
CREATE USER storageloader PASSWORD '$storageloaderpassword';
GRANT USAGE ON SCHEMA atomic TO storageloader;
GRANT INSERT ON TABLE "atomic"."events" TO storageloader;
```

You can set the user name and password (`storageloader` and `$storageloaderpassword` in the example above) to your own values. Note them down: you will need them when you come to setup the storageLoader in the next phase of the your Snowplow setup.

**Note:** the StorageLoader user created does **not** have permissions to perform 'vacuum' or 'compupdates' operations in Redshift. (Only the admin user created with the Redshift cluster has these permissions.) So we recommend you switch from using that user to the StorageLoader user after you've completed your setup and do not need to perform those operations.  

<a name="read-only-user" />
### 5.2 Creating a read-only user

To create a new user who can read Snowplow data, but not modify it, connect to the Snowplow database and execute the following SQL:

```sql
CREATE USER read_only PASSWORD '$read_only_user';
GRANT USAGE ON SCHEMA atomic TO read_only;
GRANT SELECT ON TABLE "atomic"."events" TO read_only;
```

Now we need to give the user access to the schemas with the different views in:

```sql
GRANT USAGE ON SCHEMA	cubes_pages	 TO read_only;
GRANT USAGE ON SCHEMA	recipes_basic	 TO read_only;
GRANT USAGE ON SCHEMA	recipes_catalog	 TO read_only;
GRANT USAGE ON SCHEMA	cubes_visits	 TO read_only;
GRANT USAGE ON SCHEMA	cubes_ecomm	 TO read_only;
GRANT USAGE ON SCHEMA	recipes_customer	 TO read_only;
```

Now give the user `SELECT` access on the indiviudal views:

```sql
GRANT SELECT ON 	atomic	.	events	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	uniques_and_visits_by_day	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	pageviews_by_day	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	events_by_day	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	pages_per_visit	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	bounce_rate_by_day	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	fraction_new_visits_by_day	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	avg_visit_duration_by_day	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	visitors_by_language	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	visits_by_country	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	new_vs_returning	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	behavior_frequency	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	behavior_recency	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	engagement_visit_duration	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	engagement_pageviews_per_visit	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	technology_browser	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	technology_os	 TO read_only;
GRANT SELECT ON 	recipes_basic	.	technology_mobile	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	uniques_and_pvs_by_page_by_month	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	uniques_and_pvs_by_page_by_week	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	add_to_baskets_by_page_by_month	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	add_to_baskets_by_page_by_week	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	purchases_by_product_by_month	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	purchases_by_product_by_week	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	all_product_metrics_by_month	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	time_and_fraction_read_per_page_per_user	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	pings_per_page_per_month	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	avg_pings_per_unique_per_page_per_month	 TO read_only;
GRANT SELECT ON 	recipes_catalog	.	traffic_driven_to_site_per_page_per_month	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	id_map_domain_to_network	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	id_map_domain_to_user	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	id_map_domain_to_ipaddress	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	id_map_domain_to_fingerprint	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	id_map_network_to_user	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	id_map_network_to_ipaddress	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	id_map_network_to_fingerprint	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	id_map_user_to_ipaddress	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	id_map_user_to_fingerprint	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	id_map_ipaddress_to_fingerprint	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	clv_total_transaction_value_by_user_by_month	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	clv_total_transaction_value_by_user_by_week	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	engagement_users_by_days_p_month_on_site	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	engagement_users_by_days_p_week_on_site	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	engagement_users_by_visits_per_month	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	engagement_users_by_visits_per_week	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_dfn_by_month_first_touch_website	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_dfn_by_week_first_touch_website	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_dfn_by_month_signed_up	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_dfn_by_week_signed_up	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_dfn_by_month_first_transact	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_dfn_by_week_first_transact	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_dfn_by_paid_channel_acquired_by_month	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_dfn_by_paid_channel_acquired_by_week	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_dfn_by_refr_channel_acquired_by_month	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_dfn_by_refr_channel_acquired_by_week	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	retention_by_user_by_month	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	retention_by_user_by_week	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_retention_by_month_first_touch	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_retention_by_week_first_touch	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_retention_by_month_signed_up	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_retention_by_week_signed_up	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_retention_by_month_first_transact	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_retention_by_week_first_transact	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_retention_by_month_by_paid_channel_acquired	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_retention_by_week_by_paid_channel_acquired	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_retention_by_month_by_refr_acquired	 TO read_only;
GRANT SELECT ON 	recipes_customer	.	cohort_retention_by_week_by_refr_acquired	 TO read_only;
GRANT SELECT ON 	cubes_pages	.	pages_basic	 TO read_only;
GRANT SELECT ON 	cubes_pages	.	views_by_session	 TO read_only;
GRANT SELECT ON 	cubes_pages	.	pings_by_session	 TO read_only;
GRANT SELECT ON 	cubes_pages	.	complete	 TO read_only;
GRANT SELECT ON 	cubes_visits	.	basic	 TO read_only;
GRANT SELECT ON 	cubes_visits	.	referer_basic	 TO read_only;
GRANT SELECT ON 	cubes_visits	.	referer	 TO read_only;
GRANT SELECT ON 	cubes_visits	.	entry_and_exit_pages	 TO read_only;
GRANT SELECT ON 	cubes_visits	.	referer_entries_and_exits	 TO read_only;
GRANT SELECT ON 	cubes_ecomm	.	transactions_basic	 TO read_only;
GRANT SELECT ON 	cubes_ecomm	.	transactions_items_basic	 TO read_only;
GRANT SELECT ON 	cubes_ecomm	.	transactions	 TO read_only;
GRANT SELECT ON 	cubes_ecomm	.	transactions_with_visits	 TO read_only;
```

Lastly, we may want to let create a schema in Redshift where the read-only user can create his/ her own tables for analytics purposes, for example:

```sql
CREATE SCHEMA scratchpad;
GRANT ALL ON SCHEMA scratchpad TO read_only;
```

<a name="power-user" />
### 5.3 Creating a power user

To create a power user that has super user privilages, connect to the Snowplow database in Redshift and execute the following:

```sql
create user power_user createuser password '$poweruserpassword';
GRANT ALL ON DATABASE snowplow TO power_user;
GRANT ALL ON SCHEMA atomic TO power_user;
GRANT ALL ON TABLE atomic.events TO power_user;
```

Note that now you've created your different users, we recommend that you no longer use the credentials you created when you created the Redshift cluster originally.

<a name="etl" />
## 6. Generate Redshift-format data from Snowplow

Assuming you are working through the setup guide sequentially, you will have already  ([setup EmrEtlRunner] [emr-etl-runner]). You should therefore have Snowplow events in S3, ready for uploading into Redshift.

If you have not already [setup EmrEtlRunner] [emr-etl-runner], then please do so now, before proceeding onto the [next stage](#load).

<a name="search_path" />
## 7. Update the search path for your Redshift cluster

The `search path` specifies where Redshift should look to locate tables and views that are specified in queries submitted to it. This is important, because the Snowplow events table is located in the "atomic" schema, whilst different recipe views are located in their own schemas (e.g. "customer_recipes" and "catalog_recipes"). By adding these schemas to the Redshift search path, it means that when you connect to Redshift from different tools (e.g. Tableau, SQL workbench), those tools can identify tables and views in each of those schemas, and present them as options for the user to connect to.

Updating the search path is straightforward. In the AWS Redshift console, click on the **Parameters Group** menu item on the left hand. menu, and select the button to **Create Cluster Parameter Group**:

[[/setup-guide/images/redshift-setup-guide/13.png]]

Give your parameter group a suitable name and click **Create**. The parameter group should appear in your list of options.

Now open up your parameter group, by clicking on the magnifying glass icon next to it, and then selecting **Edit** in the menu across the top:

[[/setup-guide/images/redshift-setup-guide/14.png]]

Update the **search_path** section to read the following:

	atomic,  cubes_visits, cubes_pages, recipes_basic, recipes_customer, recipes_catalog

Note: you can choose to add and remove schemas. Do note, however, that if you include a schema on the search path that does not exist yet on your database, you will cause Redshift to become very unstable. (For that reason, it is often a good idea to leave the `search_path` with the default settings, and only update it once you've setup the relevant schemas in Redshift.)

Save the changes. We now need to update our cluster to use this parameter group. To do so, select **Clusters** from the left hand manu, select your cluster and click the modify button. Now you can select your new parameter group in the **Cluster Parameter Group** dropdown:

[[/setup-guide/images/redshift-setup-guide/15.png]]

Click the **Modify** button to save the changes. We now need to reboot the cluster, so that the new settings are applied. Do this by clicking the **Reboot** button on the top menu.


<a name="load" />
## 8. Automate the loading of Snowplow data into Redshift

Now that you have your Snowplow database and table setup on Redshift, you are ready to [setup the StorageLoader to regularly upload Snowplow data into the table] [storage-loader]. Click [here] [storage-loader] for step-by-step instructions on how.

[Back to top](#top).


[emr-etl-runner]: 1-Installing-EmrEtlRunner
[storage-loader]: 1-Installing-the-StorageLoader
[sql-workbench-tutorial]: http://docs.aws.amazon.com/redshift/latest/gsg/getting-started.html
[redshift-table-def]: https://github.com/snowplow/snowplow/blob/master/4-storage/redshift-storage/sql/atomic-def.sql
