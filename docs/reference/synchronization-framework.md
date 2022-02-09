# Synchronization Framework

WebDSL provides possibility to generate code for a synchronization framework combined with mobl. Nevertheless, the webservices are open to be used by other applications.

## Required steps

The steps that are required for the synchronization framework are the following:

* A WebDSL application that contains a (complete) model
* Additional settings for the generation
* Generation of the framework
* Importing of the generated framework
* A mobl or other external application that uses the synchronization


## Application

The framework is meant as an extension to a WebDSL application. This means that it requires at least a full model in the application to generate a working framework.

## Additional Settings

The synchronization framework requires and allows some adaption by additional settings. Those settings can be added in the synchronization settings for each entity. 

    entity Example{
        synchronization configuration{
        }     
    }

The following settings can be configured:

* TopLevel entity(required)
* Access control rules for objects
* Restricted properties

### TopLevel Entity

The data synchronization requires a Toplevel Entity to enable the data partitioning. This is simple a flag that specifies that objects of this type represent a data partition. Additionally, this setting requires a String property that can be used to represent this object. 

    entity Car{
        registrationIdentifier :: String
        
        synchronization configuration{
            toplevel name property : registrationIdentifier
        }     
    }

### Access Control

The data synchronization framework enable external sources to read and modify data on the server with the web application. The framework allows control over which data can be accessed by who. This can only be specified when the a principal is defined in the web application. There are three different levels that can be specified for each entity: read, write and create. It is recommended to specify those rules for each entity.

    entity Dummy{
        name :: String
        
        synchronization configuration{
            access read: true
            access write: Logedin()
            access create: principal().isAdmin()
        }     
    }


### Restricted Properties

The last setting that can be configured is that of restricted properties. It allows to simplify the data model that you want to use on synchronization. The properties that are specified in this configuration are removed from the shared data and also for the calculation of data partitioning.


    entity Person{
        surName :: String
        firstName :: String
        fullName :: String
        
        synchronization configuration{
            restricted properties : surName, firstName
        }     
    }

## Generation of the framework

Generation of the framework is easy. After specifying the settings, open the main application file in the IDE. Then select the generate synchronization framework from the Transform menu.

## Importing of the framework

The framework is generated in the folder webservices. To enable the synchronization framework inside the web application you need to include the main file of the framework.

    application TestApp
    imports webservices/services/interface

## mobl or other remote application

The framework generates code for mobl that enable synchronization in a mobl application. However, it still needs a full mobile application. 

Other applications can use the available webservices to synchronize with the application.

## What is generated

The framework generates a lot of files, but what does it contain:

* WebDSL
    - Synchronization core
    - Webservices
    - Mappers
    - Serializers
    - Data Partitioning
    - Access Control
    - Authentication
* mobl
    - Model
    - Mappers
    - Integration functions
    - Authentication
    - Data Browser

## Synchronization core

The core of the synchronization contains functions that overlook the main functionality of the synchronization. Identification, detection and resolution of updates.

## Webservices

The webservices are used for communication with mobl of other applications. This is a layer on the core of the synchronization.
All services are called by post request to the url:

     http://<websiteurl>/webservice/<webservicename>

The following services are available and should be used in that order:

* getTopLevelEntities
* getTimeStamp
* syncNewObjects
* syncDirtyObjects
* sync<EntityName>

## WebDSL mappers

The mappers are meant for mapping the updates to local values. The also have some additional statements for checking validity of the input.
There are two mappers, one for modification and one for creation. Currently, they contain the same code. This is done so they can be overwritten separately.

* mapperEdit<EntityName>
* mapperNew<EntityName>

### Serializers

The values in the database are not in a format that can be send through webservices. Therefor, the framework has 3 functions for each entity

* toJSON: a full representation of the object in JSON 
* toSimpleJSON: a JSON representation of the objects only containing simple properties 
* toMinimalJSON: a JSON representation of object meant as reference. Only contains the id.


### Data Partitioning

The synchronization framework uses data partitioning to reduce the amount of data for mobile applications. This solution chooses to use object relations to determine if objects are linked to the TopLevel entity. 
This requires that each entity has a function to calculate the related objects. 

The main function of data partitioning gets a closure of a data partitioning by calling the related functions until there are no new objects any more.

### Access Control

As mentioned before you can specify three rules for the access control of objects. Those rules are turned into functions named:

* mayReadSynchronize
* mayModifySynchronize
* mayCreateSynchronize

### Authentication

The access control requires that remote applications can login to the application. To improve security a device can register itself and get a devicekey. Which then can be used to authenticate instead of using the password. Those keys are stored as an additional property of the principal and if removed the device is de-authenticated.


## Model

A big part of the data synchronization is about the model. The model is basically a copy of that of WebDSL only with other mobl types. It should also be used for developers that try to understand what model is expected for the webservices. The following sections are additional notes to the creation of the model.

### Restricted types

Mobl has a more restricted set of types. This let to the choice to not support all types. properties with the following types are removed:

* Secret
* File
* Image
* Patch

### class Hierarchy

Mobl does not support class Hierarchy. To support all entities from the application the synchronization framework has flatten the hierarchy.
The influence can be found in the renamed properties that now have a prefix of there original class name. And a additional property that tells the actual type: Typefield


### TopLevel properties

The data partitioning requires some additional information. This is stored in the property sync and lastSynced.

### Search annotations

The search annotations in mobl are expensive and better can be removed from the model.


## Mappers

Mobl also needs some mappers of the values. However the limited difference between mobile and JSON representation, allows it to use the function generated by the mobl compiler.

### Integration functions

There are some integration functions for mobl that can be used to call synchronization processes. It can be seen as the core of the synchronization for mobl applications.

### Authentication

The authentication are some functions to enable the devicekey setup.
It has the following functions:

* authenticate
* registerDevice
* logoutDevice

The logging out of the device also cleans the database for security reasons.


### Data Browser

As a start the generated framework has a data browser included to have easy start with the application. 

It has a page for every entity, namely: show<EntityName>Simple

Those pages allow to click through the data stored locally.


## Additional notes


### version number

The send version numbers of each objects can be used to change the protocol of resolution of outdated objects. Giving it an high number will interpret that the object is newer than that of the system.


### Collections

mobl doesn't have difference between set and lists, it only supports collections. The biggest problem is that the ordering can not be trust.
