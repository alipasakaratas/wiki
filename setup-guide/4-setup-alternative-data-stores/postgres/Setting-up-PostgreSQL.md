<a name="top" />

[**HOME**](Home) » [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) » [**Step 4: setting up alternative data stores**](Setting-up-alternative-data-stores) » Setup PostgreSQL

## Contents

1. [Setting up PostgreSQL on a EC2 instance](#ec2)
2. [Setting up PostgreSQL on Debian / Ubuntu](#debian)
3. [Creating the Snowplow events table in PostgreSQL](#events-table)
4. [Next steps](#next-steps)

<a name="ec2" />

## 1. Setting up PostgreSQL on EC2

**Note**: We recommend running all Snowplow AWS operations through an IAM user with the bare minimum permissions required to run Snowplow. Please see our [IAM user setup page](IAM-setup) for more information on doing this.

<a name="1.1" />

### 1.1 Setup an EC2 instance to run PostgreSQL server

If you do not have a server / EC2 instance already available to run PostgreSQL, you will need to create one. (If you do have one, skip to the next step, [installing PostgreSQL](#1.2)).

Log into the AWS console, navigate to the EC2 section and go either to **EC2 Dashboard** or **Instances**:

[[/setup-guide/images/postgresql/aws-ec2-console.jpg]]

#### Choose AMI

Press the **Launch Instance** button. You should be presented with **Quick Start** wizard:

[[/setup-guide/images/postgresql/ec2-quick-launch-wizard.jpg]]

Choose to launch an **Amazon Linux AMI** instance, and press the **Select** button.

#### Instance type

[[/setup-guide/images/postgresql/choose-instance-type.jpg]]

Select a suitable instance type. Amazon offers a [range of instances](https://aws.amazon.com/ec2/instance-types/), with different costs and different configurations. Depending on your the number of events you expect to be tracking per day, it may make sense to select a larger, more powerful instance. Bear in mind though that these cost more.

Click on **Review and Launch** button.

[[/setup-guide/images/postgresql/review-instance-launch.jpg]]

#### Security settings

We recommend that you create a new security group for your PostgreSQL instance. To do this, click on **Edit Security Groups**:

[[/setup-guide/images/postgresql/create-security-group.jpg]]

Enter a group name and its description. Now add two rules, one enabling SSH access from `0.0.0.0/0`, and a custom rule to enable access on PostgreSQL 5432 port.

Click on **Review and Launch** button.

#### Launch your instance

When you are happy with the settings, click **Launch**. Note that in the example above, we are using a `t1.micro` instance for testing purposes.

At this point you should be prompted to either select an existing key pair or create a new one (or even not having one which is not recommended). The key file allows you to securely SSH into your instance.

You have to download the private key file (*.pem file) before you can continue. Store it in a secure and accessible location. You will not be able to download the file again after it's created.

Once downloaded (in the form of a `.pem` file), you will need to make sure it is not publicly viewable by executing the following at the command line:

	$ chmod 400 $key-pair-name.pem

[[/setup-guide/images/postgresql/create-key-pair.jpg]]

Click on **Launch Instances** button to complete the setup and start the instance.

<a name="1.2" />

### 1.2 Install PostgreSQL

#### 1.2.1 SSH into your EC2 instance

To install PostgreSQL, you need to SSH into the instance that you want to set up PostgreSQL on, and install Postres. In this tutorial, we're going to connect via a standalone SSH client, but you can also connect through the web browser.(For full instructions on the different connection options, see the [Amazon guide][amazon-emr-guide].)

To do that, go into the EC2 section of the AWS dashboard. Click on **Instances** link on the left-hand menu. Right click on the instance you want to set up PostgreSQL on and click **Connect**. In the dialogue box that appears, ensure to select **A standalone SSH client** in ***I would like to connect with*** section:

[[/setup-guide/images/postgresql/connect-with-standalone-ssh-client.jpg]]

Copy the text that Amazon instructs you to enter at the command line to SSH into the instance. Then paste it into the command line:

	$ ssh -i postgres-test.pem ec2-user@ec2-54-246-27-243.eu-west-1.compute.amazonaws.com

You should see something like this:

	$ ssh -i postgres-test.pem ec2-user@ec2-54-246-27-243.eu-west-1.compute.amazonaws.com
	The authenticity of host 'ec2-54-246-27-243.eu-west-1.compute.amazonaws.com (54.246.27.243)' can't be established.
	RSA key fingerprint is 55:88:a8:9e:7d:fe:d1:23:8f:d3:15:06:d7:c5:29:19.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'ec2-54-246-27-243.eu-west-1.compute.amazonaws.com,54.246.27.243' (RSA) to the list of known hosts.

	       __|  __|_  )
	       _|  (     /   Amazon Linux AMI
	      ___|\___|___|

	https://aws.amazon.com/amazon-linux-ami/2013.03-release-notes/
	There are 6 security update(s) out of 11 total update(s) available
	Run "sudo yum update" to apply all updates.
	[ec2-user@ip-10-34-176-225 ~]$


#### 1.2.2 Download and install PostgreSQL on the instance

Amazon helpfully keeps a range of packages including PostgreSQL in repos so that EC2 users can install them directly, using `yum`. To install PostgreSQL from one of these repos, enter the following at the command line:

	$ sudo yum install postgresql postgresql-server postgresql-devel postgresql-contrib postgresql-docs

Initialize a database:

	$  sudo postgresql-setup initdb

Then edit your `pg_hba.conf` file:

	$ sudo vim /var/lib/pgsql9/data/pg_hba.conf

Update the bottom of the file, which will read something like this, by default:

	# TYPE  DATABASE        USER            ADDRESS                 METHOD

	# "local" is for Unix domain socket connections only
	local   all             all                                     ident
	# IPv4 local connections:
	host    all             all             127.0.0.1/32            ident
	# IPv6 local connections:
	host    all             all             ::1/128                 ident

To read this:

	# TYPE  DATABASE        USER            ADDRESS                 METHOD

	# "local" is for Unix domain socket connections only
	local   all             all                                     trust
	# IPv4 local connections:
	host    all             power_user      0.0.0.0/0               md5
	host    all             other_user      0.0.0.0/0               md5
	host    all             storageLoader   0.0.0.0/0               md5
	# IPv6 local connections:
	host    all             all             ::1/128                 md5

Now that we've updated the authorization settings, we need to update PostgreSQL to enable remote connections to the database. At the command line enter:

	$ sudo vim /var/lib/pgsql9/data/postgresql.conf

Uncomment line 59:

	#listen_addresses = 'localhost'          # what IP address(es) to listen on;

And update the line to enable connections from any IP address:

	listen_addresses='*'

And uncomment line 63:

	#port = 5432

So it reads

	port = 5432

Now start the server:

	$ sudo service postgresql start

Log into the server:

	$ sudo su - postgres
	$ psql -U postgres

And add a password for your PostgreSQL admin:

	ALTER USER postgres WITH PASSWORD '$password';

Now we need to create user credentials for our different users. Power users will be able to do anything (these are really admins.) "Other users" will be suitable for analysts who wish to query the data. We also create a particular user with limited access for the storageloader - these credentials will be used just to load Snowplow data into Postgres.

```sql
CREATE USER power_user SUPERUSER;
ALTER USER power_user WITH PASSWORD '$poweruserpassword';
CREATE USER other_user NOSUPERUSER;
ALTER USER other_user WITH PASSWORD '$otheruserpassword';
CREATE DATABASE snowplow WITH OWNER other_user;
CREATE USER storageloader PASSWORD '$storageloaderpassword';
```

We can now exit from Postgres with `\q`. Setup is complete: we are ready to connect to our database remotely.

<a name="security-group" />

#### 1.2.3 Ensure that EC2 enables remote connections to the PostgreSQL port

Before you can connect to PostgreSQL remotely, you need to ensure that you have enabled access to the port in your EC2 security groups settings.

Log into the AWS console, and navigate to the EC2 section. Click on the **Security Groups** settings in the **Network & Security** section. Select the security group which applies to your EC2 instance and select the **Inbound** tab:

[[/setup-guide/images/postgresql/security-group-1.jpg]]

If you haven't done it yet, create a **Custom TCP rule** - set the port range to your Postgres port (most likely `5432`) and provide an IP Address (or range) from which you will grant users remote access. Then click the **Add rule** button. Make sure that afterwards you click the **Save** button, to ensure that the rule is active.


#### 1.2.4 Connect to your PostgreSQL instance remotely

Now that we have PostgreSQL up and running on our EC2 instance, we are in a position to connect from a remote computer using a PostgreSQL client. We'll be using Navicat in this tutorial, but any PostgreSQL compatible client (there are 100s) should work in a similar way.

Fire up Navicat, and under **Connection** select **PostgreSQL**. A dialogue box appears, enabling us to enter details to connect to PostgreSQL:

[[/setup-guide/images/postgresql/navicat-1.png]]

Give the connection a suitable name. You can identify the EC2 public IP address (to enter in the **Host name/IP Address** field, by clicking on the instance in the AWS console and scrolling down in the properties section to **Public DNS**:

[[/setup-guide/images/postgresql/navicat-2.jpg]]

Copy and paste the value (in our case) `ec2-54-216-22-100.eu-west-1.compute.amazonaws.com` into the host field in Navicat.

Select either the username `power_user` and associated password you created in the previous step, or `other_user`. (Depending on the permissions you wish to grant.)

[[/setup-guide/images/postgresql/navicat-3.png]]

You should now be able to either test the connection or click **OK** to save the connection. You can then double click it to go into the database.

You are now ready to [setup the Snowplow events table and views](#events-table).

Back to [top](#top).

<a name="debian" />

## 2. Setting up PostgreSQL on Debian / Ubuntu

*Thanks to [Simon Rumble][simon-rumble] for providing the following instructions:

Download PostgreSQL:

	$ sudo apt-get install postgresql

Update `pg_hba.conf` as described in the previous section:

	$ sudo vim /etc/postgresql/9.1/main/pg_hba.conf

Update postgresql.conf as in the previous section (as if we were installing on Amazon Linux):

	$ sudo vim /etc/postgresql/9.1/main/postgresql.conf

Then restart PostgreSQL:

	$ sudo /etc/init.d/postgresql restart

Log into PostgreSQL:

	$ sudo su - postgres
	$ psql

Update your Postres user credentials:

Now we need to create user credentials for our different users. Power users will be able to do anything (these are really admins.) "Other users" will be suitable for analysts who wish to query the data. We also create a particular user with limited access for the storageloader - these credentials will be used just to load Snowplow data into Postgres.

```sql
CREATE USER power_user SUPERUSER;
ALTER USER power_user WITH PASSWORD '$poweruserpassword';
CREATE USER other_user NOSUPERUSER;
ALTER USER other_user WITH PASSWORD '$otheruserpassword';
CREATE DATABASE snowplow WITH OWNER other_user;
CREATE USER storageloader PASSWORD '$storageloaderpassword';
\q
```

Now log back into Postgres and with your new user credentials:

	psql -U power_user snowplow

You can now Snowplow events table as described in the [next step](#events-table).

Back to [top](#top).

<a name="events-table" />

## 3. Create the Snowplow events table and views in PostgreSQL

Now that PostgreSQL has been setup, we need to create the table for the Snowplow events.

First, let's create the `atomic.events` table, where the actual Snowplow data will live. The SQL for creating the atomic schema and table can be found [here][postgres-table-def]. Either copy and paste that SQL into PSQL / Navicat, or you can run that file into PSQL at the command line. To do this, navigate to your Snowplow repo, then:

	$ cd 4-storage/postgres-storage/sql
	$ psql -h <HOSTNAME> -U power_user -d snowplow -p <PORT> -f atomic-def.sql

You'll need to substitute in your credentials for `<HOSTNAME>` and `<PORT>`. Make sure you use the `power_user` credentials to create new tables etc.

Finally, you need to grant access relevant access permissions to your different users.

We need to make sure that the `StorageLoader` user can write to the `atomic.events` table:

	$ psql -h <HOSTNAME> -U power_user -d snowplow -p <PORT>
	GRANT USAGE ON SCHEMA atomic TO storageloader;
	GRANT INSERT ON TABLE   "atomic"."events" TO storageloader;

Then we need to grant access to the `atomic` schema to `other_user`:

```sql
GRANT USAGE ON SCHEMA	atomic	 TO other_user;
```

Now we need to grant access to all the events table to `other_user`:

```sql
GRANT SELECT ON 	atomic	.	events	 TO other_user;
```

Now you should be able to connect to the database as `other_user` and use the events table.

Back to [top](#top).

<a name="next-steps" />

## 4. Next steps

Now you have setup PostgreSQL, you are ready to [setup the StorageLoader][setup-storageloader] to automate the regular loading of Snowplow data into the PostgreSQL events table.

[amazon-emr-guide]: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html
[setup-storageloader]: 1-Installing-the-StorageLoader
[postgresql-table-def]: https://github.com/snowplow/snowplow/blob/master/4-storage/postgres-storage/sql/atomic-def.sql
[simon-rumble]: https://github.com/shermozle
