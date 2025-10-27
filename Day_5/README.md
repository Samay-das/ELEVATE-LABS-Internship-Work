# Day 5 :  Create and Connect to a Cloud Database Instance

## Task 1. Create a Cloud SQL instance

1. From the Navigation menu (Navigation menu icon) click Cloud SQL.

2. Click Create Instance.

3. On the Choose your database engine panel of the Create an instance page, click Choose MySQL.

4. For Choose a Cloud SQL edition, select Enterprise edition.

5. For Preset choose Development (4 vCPU, 16 GB RAM, 100 GB Storage, Single zone).

> **Warning**
> : If you choose a preset larger than Development, your project will be flagged and your lab will be terminated.

6. Select the database version as MySQL 8.

7. Enter Instance ID as myinstance.

8. In the password field click on the Generate link and the eye icon to see the password. Save the password to use in the next section.

9. In Choose region and zonal availability section, region is already set to <REGION>

10. Set the Multi zones (Highly available) > Primary Zone field as <ZONE>.

11. Click CREATE INSTANCE.

It might take a few minutes for the instance to be created. Once it is, you will see a green checkmark next to the instance name.

12. Click on the Cloud SQL instance. The SQL Overview page opens.

## Task 2. Connect to your instance using the mysql client in Cloud Shell

1. In the Cloud Console, click the Cloud Shell icon in the upper right corner.

2. Click Continue.

3. At the Cloud Shell prompt, connect to your Cloud SQL instance by running the following:

        gcloud sql connect myinstance --user=root

Click Authorize.

4. Enter your root password when prompted. Note: The cursor will not move.

5. Press the Enter key when you're done typing.

You should now see the mysql prompt.

## Task 3. Create a database and upload data

1. Create a SQL database called guestbook on your Cloud SQL instance:

        CREATE DATABASE guestbook;

2. Insert the following sample data into the guestbook database:

        USE guestbook;
        CREATE TABLE entries (guestName VARCHAR(255), content VARCHAR(255),
        entryID INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(entryID));
        INSERT INTO entries (guestName, content) values ("first guest", "I got here!");
        INSERT INTO entries (guestName, content) values ("second guest", "Me too!"); 

3. Now retrieve the data:

        SELECT * FROM entries;

You should see:

    +--------------+-------------------+---------+
    | guestName    | content           | entryID |
    +--------------+-------------------+---------+
    | first guest  | I got here!       |       1 |
    | second guest | Me too!           |       2 |
    +--------------+-------------------+---------+
    2 rows in set (0.00 sec)
    mysql>

## Reference :

Google Skill Boost  :
[Cloud SQL for MySQL: Qwik Start](https://www.skills.google/course_templates/701/labs/503695)