
# Anypoint Template: Salesforce to Salesforce, Workday, SAP and Database Account Broadcast

+ [License Agreement](#licenseagreement)
+ [Use Case](#usecase)
+ [Considerations](#considerations)
	* [DB Considerations](#dbconsiderations)
	* [SAP Considerations](#sapconsiderations)
	* [Salesforce Considerations](#salesforceconsiderations)
	* [Workday Considerations](#workdayconsiderations)
+ [Run it!](#runit)
	* [Running on premise](#runonopremise)
	* [Running on Studio](#runonstudio)
	* [Running on Mule ESB stand alone](#runonmuleesbstandalone)
	* [Running on CloudHub](#runoncloudhub)
	* [Deploying your Anypoint Template on CloudHub](#deployingyouranypointtemplateoncloudhub)
	* [Properties to be configured (With examples)](#propertiestobeconfigured)
+ [API Calls](#apicalls)
+ [Customize It!](#customizeit)
	* [config.xml](#configxml)
	* [businessLogic.xml](#businesslogicxml)
	* [endpoints.xml](#endpointsxml)
	* [errorHandling.xml](#errorhandlingxml)


# License Agreement <a name="licenseagreement"/>
Note that using this template is subject to the conditions of this [License Agreement](AnypointTemplateLicense.pdf).
Please review the terms of the license before downloading and using this template. In short, you are allowed to use the template for free with Mule ESB Enterprise Edition, CloudHub, or as a trial in Anypoint Studio.

# Use Case <a name="usecase"/>
As a Salesforce admin I want to synchronize accounts from a Salesforce Org to another Salesforce Org, Workday, SAP and Database instances.

This Anypoint Template should serve as a foundation for setting an online sync of accounts from a Salesforce instance to many destination systems, using the Publish-subscribe pattern. Every time there is a new account or a change in an already existing one, the integration will poll for changes in the Salesforce source Org, publish the changes to a topic and each subscriber will be responsible for updating the accounts in the target systems.

Requirements have been set not only to be used as example, but also to establish a starting point to adapt the integration to your requirements.

As implemented, this Anypoint Template leverages the [Batch Module](http://www.mulesoft.org/documentation/display/current/Batch+Processing).
The batch job is divided into Input, Process and On Complete stages.

The integration can be triggered in two different ways: by a poll defined in the endpoints file or by an outbound notification generated by Salesforce. Either way, the application queries/receives newest Salesforce updates/creations matching a filter criteria and adds them to a JMS topic.

The application has four different batch jobs consuming this JMS topic, one for migrating the changes to each target system (the second Salesforce Org, Workday, SAP and the DB). During the Process stage, each Salesforce account will be matched with an existing account in the target system by Name. The last step of the Process stage will group the accounts and create/update them in target systems.

Finally during the On Complete stage the Anypoint Template will log output statistics data into the console.

# Considerations <a name="considerations"/>

To make this Anypoint Template run, there are certain preconditions that must be considered. All of them deal with the preparations in both source and destination systems, that must be made in order for all to run smoothly. **Failling to do so could lead to unexpected behavior of the template.**

1. The batch will fail, if you do not provide correct value for Workday Customer Category Id. You will need to extend the custom mapping, contained in **categoryLookupFlow**, if accounts with a different than five mapped Salesforce Industry / Workday Customer Category values are updated.

## DB Considerations <a name="dbconsiderations"/>

There may be a few things that you need to know regarding DB, in order for this template to work.

This Anypoint Template may be using date time/timestamp fields from the DB in order to do comparisons and take further actions.
While the template handles the time zone by sending all such fields in a neutral time zone, it can not handle **time offsets**.
We define as **time offsets** the time difference that may surface between date time/timestamp fields from different systems due to a differences in the system's internal clock.
The user of this template should take this in consideration and take the actions needed to avoid the time offset.


### DB as destination of data

There are no particular considerations for this Anypoint Template regarding DB as data destination.
## SAP Considerations <a name="sapconsiderations"/>

There may be a few things that you need to know regarding SAP, in order for this template to work.


### As destination of data

There are no particular considerations for this Anypoint Template regarding Sap as data destination.
## Salesforce Considerations <a name="salesforceconsiderations"/>

There may be a few things that you need to know regarding Salesforce, in order for this template to work.

In order to have this template working as expected, you should be aware of your own Salesforce field configuration.

###FAQ

 - Where can I check that the field configuration for my Salesforce instance is the right one?

    [Salesforce: Checking Field Accessibility for a Particular Field][1]

- Can I modify the Field Access Settings? How?

    [Salesforce: Modifying Field Access Settings][2]


[1]: https://help.salesforce.com/HTViewHelpDoc?id=checking_field_accessibility_for_a_particular_field.htm&language=en_US
[2]: https://help.salesforce.com/HTViewHelpDoc?id=modifying_field_access_settings.htm&language=en_US

### As source of data

If the user configured in the template for the source system does not have at least *read only* permissions for the fields that are fetched, then a *InvalidFieldFault* API fault will show up.

```
java.lang.RuntimeException: [InvalidFieldFault [ApiQueryFault [ApiFault  exceptionCode='INVALID_FIELD'
exceptionMessage='
Account.Phone, Account.Rating, Account.RecordTypeId, Account.ShippingCity
^
ERROR at Row:1:Column:486
No such column 'RecordTypeId' on entity 'Account'. If you are attempting to use a custom field, be sure to append the '__c' after the custom field name. Please reference your WSDL or the describe call for the appropriate names.'
]
row='1'
column='486'
]
]
```

### As destination of data

There are no particular considerations for this Anypoint Template regarding Salesforce as data destination.


## Workday Considerations <a name="workdayconsiderations"/>


### As destination of data

There are no particular considerations for this Anypoint Template regarding Workday as data destination.





# Run it! <a name="runit"/>
Simple steps to get Salesforce to Salesforce, Workday, SAP and Database Account Broadcast running.
See below.

## Running on premise <a name="runonopremise"/>
In this section we detail the way you have to run you Anypoint Temple on you computer.

Once your app is all set and started, there is no need to do anything else. The application will poll Account to know if there are any newly created or updated objects and synchronice them.


### Where to Download Mule Studio and Mule ESB
First thing to know if you are a newcomer to Mule is where to get the tools.

+ You can download Mule Studio from this [Location](http://www.mulesoft.com/platform/mule-studio)
+ You can download Mule ESB from this [Location](http://www.mulesoft.com/platform/soa/mule-esb-open-source-esb)


### Importing an Anypoint Template into Studio
Mule Studio offers several ways to import a project into the workspace, for instance: 

+ Anypoint Studio generated Deployable Archive (.zip)
+ Anypoint Studio Project from External Location
+ Maven-based Mule Project from pom.xml
+ Mule ESB Configuration XML from External Location

You can find a detailed description on how to do so in this [Documentation Page](http://www.mulesoft.org/documentation/display/current/Importing+and+Exporting+in+Studio).


### Running on Studio <a name="runonstudio"/>
Once you have imported you Anypoint Template into Anypoint Studio you need to follow these steps to run it:

+ Locate the properties file `mule.dev.properties`, in src/main/resources
+ Complete all the properties required as per the examples in the section [Properties to be configured](#propertiestobeconfigured)
+ Once that is done, right click on you Anypoint Template project folder 
+ Hover you mouse over `"Run as"`
+ Click on  `"Mule Application"`


### Running on Mule ESB stand alone <a name="runonmuleesbstandalone"/>
Complete all properties in one of the property files, for example in [mule.prod.properties] (../master/src/main/resources/mule.prod.properties) and run your app with the corresponding environment variable to use it. To follow the example, this will be `mule.env=prod`. 


## Running on CloudHub <a name="runoncloudhub"/>
While [creating your application on CloudHub](http://www.mulesoft.org/documentation/display/current/Hello+World+on+CloudHub) (Or you can do it later as a next step), you need to go to Deployment > Advanced to set all environment variables detailed in **Properties to be configured** as well as the **mule.env**. 

Once your app is all set and started, there is no need to do anything else. Every time an account is created or modified, it will be automatically synchronised to Salesforce Org B, Workday and SAP systems and the Database as long as it matches the filtering criteria.


### Deploying your Anypoint Template on CloudHub <a name="deployingyouranypointtemplateoncloudhub"/>
Mule Studio provides you with really easy way to deploy your Template directly to CloudHub, for the specific steps to do so please check this [link](http://www.mulesoft.org/documentation/display/current/Deploying+Mule+Applications#DeployingMuleApplications-DeploytoCloudHub)


## Properties to be configured (With examples) <a name="propertiestobeconfigured"/>
In order to use this Mule Anypoint Template you need to configure properties (Credentials, configurations, etc.) either in properties file or in CloudHub as Environment Variables. Detail list with examples:
### Application configuration
+ poll.frequency `60000`
+ poll.start.delay `0`
+ watermark.defaultExpression `YESTERDAY`
+ trigger.policy `push` | `poll`

**Note**: the property `trigger.policy` can asume only one of the values above either *push* or *poll*

**Database Connector configuration**

+ database.url `jdbc:mysql://localhost:3306/mule?user=mule&password=mule`

If it is required to connect to a different Database there should be provided the jar for the library and changed the value of that field in the connector.

**Salesforce Connector configuration for company A**

+ sfdc.a.username `bob.dylan@orga`
+ sfdc.a.password `DylanPassword123`
+ sfdc.a.securityToken `avsfwCUl7apQs56Xq2AKi3X`
+ sfdc.a.url `https://login.salesforce.com/services/Soap/u/32.0`

**Salesforce Connector configuration for company B**

+ sfdc.b.username `joan.baez@orgb`
+ sfdc.b.password `JoanBaez456`
+ sfdc.b.securityToken `ces56arl7apQs56XTddf34X`
+ sfdc.b.url `https://login.salesforce.com/services/Soap/u/32.0`

**SAP Connector configuration**

+ sap.jco.ashost `your.sap.address.com`
+ sap.jco.user `SAP_USER`
+ sap.jco.passwd `SAP_PASS`
+ sap.jco.sysnr `14`
+ sap.jco.client `800`
+ sap.jco.lang `EN`

+ sap.default.accountGroup `ZAG2`

**WorkDay Connector configuration**

+ wday.user `user1@mulesoft_pt1`
+ wday.password `ExamplePassword565`
+ wday.endpoint `https://services1.workday.com/ccx/service/acme/Human_Resources/v20`
+ category.biotechnology `CUSTOMER_CATEGORY-6`
+ category.financialServices `CUSTOMER_CATEGORY-1`
+ category.hospitality `CUSTOMER_CATEGORY-4`
+ category.manufacturing `CUSTOMER_CATEGORY-4-8`
+ category.technology `CUSTOMER_CATEGORY-5`

A Category group defines CUSTOMER_CATEGORY_ID for each category as identified in Workday. The values are used for mapping Salesforce Account Industry field to Workday Customer Category.

# API Calls <a name="apicalls"/>
Salesforce imposes limits on the number of API Calls that can be made. Therefore calculating this amount may be an important factor to consider. The Anypoint Template calls to the API can be calculated using the formula:

***1 + X + X / 200***

Being ***X*** the number of Accounts to be synchronized on each run. 

The division by ***200*** is because, by default, Accounts are gathered in groups of 200 for each Upsert API Call in the commit step. Also consider that this calls are executed repeatedly every polling cycle.	

For instance if 10 records are fetched from origin instance, then 12 api calls will be made (1 + 10 + 1).


# Customize It!<a name="customizeit"/>
This brief guide intends to give a high level idea of how this Anypoint Template is built and how you can change it according to your needs.
As mule applications are based on XML files, this page will be organized by describing all the XML that conform the Anypoint Template.
Of course more files will be found such as Test Classes and [Mule Application Files](http://www.mulesoft.org/documentation/display/current/Application+Format), but to keep it simple we will focus on the XMLs.

Here is a list of the main XML files you'll find in this application:

* [config.xml](#configxml)
* [endpoints.xml](#endpointsxml)
* [businessLogic.xml](#businesslogicxml)
* [errorHandling.xml](#errorhandlingxml)


## config.xml<a name="configxml"/>
Configuration for Connectors and [Properties Place Holders](http://www.mulesoft.org/documentation/display/current/Configuring+Properties) are set in this file. **Even you can change the configuration here, all parameters that can be modified here are in properties file, and this is the recommended place to do it so.** Of course if you want to do core changes to the logic you will probably need to modify this file.

In the visual editor they can be found on the *Global Element* tab.


## businessLogic.xml<a name="businesslogicxml"/>
A functional aspect of this Anypoint Template implemented in this XML is to create or update objects in the destination system for a represented use case. You can customize and extend the logic of this Anypoint Template in this XML to more specifically meet your needs.



## endpoints.xml<a name="endpointsxml"/>
This is file is conformed by a two Flows.

The first one we'll call it **push** flow. This one contains an HTTP endpoint that will be listening for notifications from Salesforce . Each of them will be processed and thus update/create Accounts, and then executing the batch job process.

The second one we'll call it **poll** flow. This one contains the Poll endpoint that will periodically query Salesforce for updated/created Accounts that meet the defined criteria in the query, and then executing the batch job process.

The property **trigger.policy** is the one in charge of defining from which endpoint the Template will recieve the data. The property can only assume one of two values `push` or `poll` any other value will result in the Template ignoring all messages.



## errorHandling.xml<a name="errorhandlingxml"/>
This is the right place to handle how your integration will react depending on the different exceptions. 
This file holds a [Choice Exception Strategy](http://www.mulesoft.org/documentation/display/current/Choice+Exception+Strategy) that is referenced by the main flow in the business logic.



