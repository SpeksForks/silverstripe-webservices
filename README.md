
# Web Services module

A module for exposing defined business logic via URLs, in web consumable
formats, and for providing access to consumers of these webservices via 
token access. 

## Maintainer Contacts

*  Marcus Nyeholt <marcus@silverstripe.com.au>

## Requirements

* SilverStripe 2.4+

## Installation

* Place this directory in the root of your SilverStripe installation. 
* Run dev/build 
* Creates your service class, which will be accessible by both controller 
  code and directly via the web
* Access your service via the appropriate URL, eg

	jsonservice/SampleService/sampleMethod?param=something&SecurityID=what_ever_goes_here

* Use the response

## Documentation

The webservices module will automatically translate a url based request to a
system service from URL + parameters to a method and variables, then 
automatically convert the response into a web accessible format (JSON and XML
supported, though XML is not yet complete). Part of its goal is to encourage
service oriented architectures in SilverStripe projects so that business logic
is encapsulated in a layer away from controllers (an all too common problem). 

URLs are decomposed as follows

	(type)/(service)/(method)?(paramName)=(value)&token=(token)[&SecurityID=(securityID)]

* type: either jsonservice or webservice, indicates the desired output format
* service: the name of a service class (see below)
* method: the name of the method to call on the service
* paramName/value: key/value pairs representing parameters. The paramName MUST
  match the name of the parameter in the method declaration. 
  * See the note below for information on how to pass DataObjects
* token | SecurityID: Each request must be verified; this is either through
  the use of a token parameter (which matches up with a user's API token) OR
  by passing the security ID for a user who has already logged in. 

### Passing DataObjects

In some cases you'll be wanting to pass data objects to your service method. 
To allow that from URL parameters, you can instead pass an ID and Type parameter
which will be converted for use appropriately. For example, this method signature

	public function myMethod(DataObject $page) {

	}

can be accessed via

	jsonservice/Service/myMethod?pageID=1&pageType=Page

The module will take care of converting that to the relevant object type

### Allowing access to services and their methods

To be able to call methods on a service, the service has to be marked as being
accessible - this is done in one of two ways

* Implement the `WebServiceable` interface
* Implement the `webEnabledMethods` method, which returns an map of method names
  and the request type that they support (GET or POST only supported currently).
  Note that to perform user level access control, you should perform the checks
  in the method directly, OR wrap user checking logic around the array this
  method generates. 

So, by default, it's not possible to arbitrarily call any service. Also, the
`webEnabledMethods` call does NOT use SilverStripe's hasMethod construct (as
it is not expected that a service carry the overhead of inheriting from 
'Object') so at the moment this is not overrideable using extensions. There will
be ways to update this via extensions at a later date. 


### Tokens

The module will automatically add a 'Token' field to user objects, and
generate a random token when a user account is created. This can be used
to access the API without being logged in first. 

Otherwise, if the user is logged in, they can pass through the security token
as a parameter (appropriately named, by default SecurityID) with the request
which will grant the user access. This can be disabled by setting

	WebServiceController::$allow_security_id = false;










