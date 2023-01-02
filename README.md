<h1 align="center"> POC - Airbyte </h1>
<br>
The purpose of this POC is to validate the Airbyte tool functionalities, mainly the Change Data Capture (CDC) functionality.
<br>
To better describe the necessary steps, this document is divided into sub-tasks:
<br>
<h3>1 - Installation: </h3> <br>
Deploying Airbyte Open-Source just takes two steps.

Install Docker on your workstation (see instructions). Make sure you're on the latest version of docker-compose.
Run the following commands in your terminal: <br>

&nbsp;&nbsp;&nbsp; git clone https://github.com/airbytehq/airbyte.git <br>
&nbsp;&nbsp;&nbsp; cd airbyte <br>
&nbsp;&nbsp;&nbsp; docker-compose up <br>

Once you see an Airbyte banner, the UI is ready to go at http://localhost:8000! You will be asked for a username and password. By default, that's username airbyte and password password. Once you deploy airbyte to your servers, be sure to change these in your .env file.

Alternatively, if you have an Airbyte Cloud invite, just follow these steps. <br>
source: https://docs.airbyte.com/quickstart/deploy-airbyte/?_ga=2.22120568.252537617.1670875979-1797733114.1669813361

<h3>2 - Configuration: </h3> <br>
<h4>Add a Source:</h4>
You can either follow this tutorial from the onboarding or through the UI, where you can first navigate to the Sources tab on the left bar. <br><br>
Our demo source will pull data from an external API, which will pull down the information on one specified Pokémon. <br><br>

To set it up, just follow the instructions on the screenshot below. <br><br>

<img width="924" alt="image" src="https://user-images.githubusercontent.com/32913011/210249245-d773b0a5-d3fa-46dd-85bc-4b9dfc19f605.png">

Note 1: Can't find the connectors that you want? Try your hand at easily building one yourself using our Python CDK for HTTP API sources! <br> 

Note 2: To this POC it was used like source the SQL-Server <br><br>
<img width="1671" alt="image" src="https://user-images.githubusercontent.com/32913011/210250046-ac658efa-b38a-4120-aab0-6d1c0e2220b7.png">

<h4>Add a Destination:</h4>
The destination we are creating is a simple JSON line file, meaning that it will contain one JSON object per line. Each objects will represent data extracted from the source. <br><br>

The resulting files will be located in /tmp/airbyte_local/json_data <br><br>

To set it up, just follow the instructions on the screenshot below. <br><br>

<img width="961" alt="image" src="https://user-images.githubusercontent.com/32913011/210250661-c02bbb52-11ae-4da3-9501-83b944e7fc55.png">
<br>
<img width="939" alt="image" src="https://user-images.githubusercontent.com/32913011/210250740-304effb8-5900-40af-bc28-7711ba04ecbe.png">
<br>
Note 1: To this POC it was used like destination the BigQuery <br><br>
<img width="1669" alt="image" src="https://user-images.githubusercontent.com/32913011/210250860-b6e386f6-cd7c-48de-b586-ca3e69ec1cd8.png">
<br>
<h4>Set up a Connection:</h4>
When we create the connection, we can select which data stream we want to replicate. We can also select if we want an incremental replication, although it isn't currently offered for this source. The replication will run at the specified sync frequency.<br><br>

To set it up, just follow the instructions on the screenshot below.<br><br>
<img width="942" alt="image" src="https://user-images.githubusercontent.com/32913011/210251361-38867a3b-1906-40ba-9878-f8a885ff07e0.png">
<br><br>
<h4>Check the logs of your first sync</h4>
After you've completed the onboarding, you will be redirected to the source list and will see the source you just added. Click on it to find more information about it. You will now see all the destinations connected to that source. Click on it and you will see the sync history.<br><br>

From there, you can look at the logs, download them, force a sync and adjust the configuration of your connection.<br><br>
<img width="960" alt="image" src="https://user-images.githubusercontent.com/32913011/210251573-f7c085ac-4e6d-4daa-b7b2-04683fea2e80.png">
<br><br>
<h4>Check the data of your first sync</h4>
Now let's verify that this worked:

&nbsp;&nbsp;&nbsp;**cat /tmp/airbyte_local/json_data/_airbyte_raw_pokemon.jsonl** <br><br>
You should see a large JSON object with the response from the API, giving you a lot of information about the selected Pokemon.<br><br>

If you have jq installed, let's look at some of the data that we have replicated about charizard. We'll pull its abilities and weight:<br><br>
&nbsp;&nbsp;&nbsp;**cat _airbyte_raw_pokemon.jsonl |**
&nbsp;&nbsp;&nbsp;**jq '._airbyte_data | {abilities: .abilities, weight: .weight}'**<br><br>
And there you have it. You've pulled data from an API directly into a file, with all of the actual configuration for this replication only taking place in the UI.<br><br>

Note: If you are using Airbyte on Windows with WSL2 and Docker, refer to this tutorial or this section in the local-json destination guide to locate the replicated folder and file.<br><br>

Final configuration:<br><br>
<img width="1661" alt="image" src="https://user-images.githubusercontent.com/32913011/210252126-af5aadf7-5213-4df5-976b-9250c0039a23.png">
<br><br>
Sync and strategy:<br><br>
<img width="1638" alt="image" src="https://user-images.githubusercontent.com/32913011/210252261-c4282143-a31e-4558-b67f-586039600d16.png">
<br><br>
Sync history:<br><br>
<img width="1644" alt="image" src="https://user-images.githubusercontent.com/32913011/210252364-0625755f-f9bf-4532-b2a4-030be3caa6da.png">
<br><br>
Transfer strategy:<br><br>
<img width="1563" alt="image" src="https://user-images.githubusercontent.com/32913011/210252444-e8f5d996-178c-4547-a30a-87e07b3fa8c2.png">
<br><br>
<h4>Setting up CDC for MSSQL:</h4>
MS SQL Server provides some built-in stored procedures to enable CDC.<br><br>
To enable CDC, a SQL Server administrator with the necessary privileges (db_owner or sysadmin) must first run a query to enable CDC at the database level.<br><br>
&nbsp;&nbsp;&nbsp;USE {database name}<br>
&nbsp;&nbsp;&nbsp;GO<br>
&nbsp;&nbsp;&nbsp;EXEC sys.sp_cdc_enable_db<br>
&nbsp;&nbsp;&nbsp;GO<br><br>
Evidence that stored procedure was executed.<br><br>
<img width="451" alt="image" src="https://user-images.githubusercontent.com/32913011/210253272-d436c537-0d21-4ba8-bb76-faa96555df4b.png">
<br><br>
<h3>3 - Test and validation: </h3> <br>
<h4>Test 1: CDC Test</h4>
I ran a test to validate the functionality of the CDC where I changed a specific field in the SQL Server database and this change was reflected in Big Query.<br><br>
It was observed that the test was successful and the data was published in Big Query.<br><br>
<h4>Test 2: Synchronization Strategies </h4>
In this test scenario, I validated some Airbyte synchronization strategies, where I changed the strategy model and observed the behavior of the tool.
<br><br>
<h4>Test 3: Usability</h4>
In this test scenario, I analyzed the usability of the tool and observed that it has good usability, with several features that are easy to absorb, it is possible in the usual way to follow the entire journey of the data and the logs for this analysis are easy to understand.<br><br>
#1 Step

Initial data (raw data)

SQL-Server:
<img width="1566" alt="image" src="https://user-images.githubusercontent.com/32913011/210253890-918021fd-c4b1-4ecd-832e-92a6786f0043.png">


Bigquery:
<img width="1648" alt="image" src="https://user-images.githubusercontent.com/32913011/210253934-c14b62f4-5694-491f-91f3-325db77bee63.png">


#2 Step

In this step I changed the value from "0" to "2" for the "stops" attribute
<img width="1435" alt="image" src="https://user-images.githubusercontent.com/32913011/210253999-c0ec2b26-4c7f-4d5a-bd02-1904030594b1.png">


Sync is running:
<img width="1660" alt="image" src="https://user-images.githubusercontent.com/32913011/210254023-26207208-be9d-42b6-854f-e54b72eb10a1.png">


Sync succeed:
<img width="1623" alt="image" src="https://user-images.githubusercontent.com/32913011/210254053-7bf6aea5-b1c9-4cf5-b821-58ae4a1bd2f2.png">


Change reflected in Bigquery:
<img width="1671" alt="image" src="https://user-images.githubusercontent.com/32913011/210254081-907b3ee9-b840-4012-9d89-2fc2fff180f9.png">







  

