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

<h3>1 - Configuration: </h3> <br>
<h4>Add a Source:</h4>
You can either follow this tutorial from the onboarding or through the UI, where you can first navigate to the Sources tab on the left bar. <br>
Our demo source will pull data from an external API, which will pull down the information on one specified Pokémon. <br>
To set it up, just follow the instructions on the screenshot below. <br><br>

<img width="924" alt="image" src="https://user-images.githubusercontent.com/32913011/210249245-d773b0a5-d3fa-46dd-85bc-4b9dfc19f605.png">

Note 1: Can't find the connectors that you want? Try your hand at easily building one yourself using our Python CDK for HTTP API sources! <br> 

Note 2: To this POC it was used like source the SQL-Server <br><br>
<img width="1671" alt="image" src="https://user-images.githubusercontent.com/32913011/210250046-ac658efa-b38a-4120-aab0-6d1c0e2220b7.png">


