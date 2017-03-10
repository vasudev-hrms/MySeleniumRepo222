# Jasmine


A JavaScript Testing Framework

[Jasmine](https://github.com/jasmine/jasmine) is a Behavior Driven Development testing framework for JavaScript. It can now be used with Scriptr.


##Writing A Jasmine Test

Start by creating a scriptr script.

Require the jasmine boot 

`var jasmine = require("<path-to-jasmine-directory>/boot")`

Write the test using [jasmine's syntax](https://jasmine.github.io/2.5/introduction) . Note that the spy, asynch and timeout features are not yet supported in the scriptr scripting engine. 

```javascript
jasmine.describe("MyTest", function() {

  jasmine.beforeAll(function(){

  }); 
  
  jasmine.beforeEach(function() {
   
  });

  jasmine.it("should check something ", function() {
    //assert on something
  });
 
});
```

Finally you call 
```javascript
jasmine.execute();
```

Check the SampleTest under the test folder for a simple working example.

## Adding more matchers

You can add matchers at will by adding your matchers to the matchers.js file. Obviously, the function that compares the actual to the expected result has to follow the [jasmine matcher syntax](https://jasmine.github.io/2.0/custom_matcher.html). 


```javascript
  

var matchers =  {
  
  
  "isSuccessful": function(util, customEqualityTesters) {
  
  	return {
    
     compare: function(actual, expected) {
        if (expected === undefined) {
          expected = '';
        }
        var result = {}; 
        
        result.pass = (actual.metadata.status == "success") ;
        result.message = "Expected script to have executed successfully ";
        return result;
      }
    }
  },
  "hasErrorCode":function(util, customEqualityTesters) {
  
  	return {
    
     compare: function(actual, expected) {
        if (expected === undefined) {
          expected = '';
        }
        var result = {}; 
        
        result.pass = (actual.metadata.errorCode == expected) ;
        result.message = "Expected Error code  " + expected + " but received " + actual.metadata.errorCode +  ".";
        return result;
      }
    }
  },

  "hasErrorDetail":function(util, customEqualityTesters) {
  
  	return {
    
     compare: function(actual, expected) {
        if (expected === undefined) {
          expected = '';
        }
        var result = {}; 
        
        result.pass = (actual.metadata.errorDetail == expected) ;
        result.message = "Expected Error Detail  " + expected + " but received " + actual.metadata.errorDetail +  ".";
        return result;
      }
    }
  }
};   
    
```
So now you'd be able to write a test that tests the behavior of a scriptr script using the scriptr matchers 
```javascript
var jasmine = require("../boot");
var http = require("http");

var bearerToken = "UTBCMTdDMjdGQQ== ";


jasmine.describe("Included matchers:", function() {

  jasmine.it("Script execution should fail because of a permission denied ", function(){
    var result = executeScript("myScript");
    console.log(JSON.stringify(result));
    jasmine.expect(result).not.isSuccessful();
    jasmine.expect(result).hasErrorCode("PERMISSION_DENIED");
    jasmine.expect(result).hasErrorDetail("Not enough permissions to perform the requested action.");
  });
 	
             
 });


function executeScript(scriptName){
  var params = {"url":"https://api.scriptr.io/" + scriptName ,"headers":{"Authorization" : "bearer " + bearerToken} , "method":"POST"};
  var response = http.request(params);
  return JSON.parse(response.body).response;
}

jasmine.execute();

```
Note : You can write your own matchers.js file with the same structure and add it to matchers array in config.js.

## Getting The Results

Due to the fantastic design of Jasmine, every time a spec started or finished executing or a suite started or finished executing, the results are sent to a reporter  following a predefined interface. The reporter can do something based on the event. You can simply write a reporter or use the default scriptr reporters under the reporters folder that are enabled by default. 

### Configuring Reporters

You can configure which reporters you want to use within the config.js file. All you have to do just add or remove reporters from the array of reporters.

### Default Reporters

#### PubSubReporter 

It pushes the test results to a channel "jasmine", you can subscribe a script to the channel to immediately do something based  on a spec or suite result, the script could pick up the json message, and then create a ticket in zoho or JIRA or any other third party ticketing platform used by your team or perhaps push a notification (a little drastic) to your phone? Send an email? 

Of course you need to save a channel in scriptr called "jasmine", or you could alternatively use a different channel by changing the used channel in the reporter.

#### Console Reporter 

Nothing fancy there , It just prints the results in a human friendly format to your scriptr console and the logs.


#### Write Your Own Reporter

 You can write your own reporter by creating a script with the following interface 
```javascript
function Reporter() {
    
   this.jasmineStarted = function(options) {
   
   };

   this.suiteStarted = function(message) {
   
   };

   this.suiteDone = function(message) {
   
   };

   this.specStarted = function(message) {
   
   }; 

   this.specDone = function(message) {
   
   };

   this.jasmineDone = function(doneResult) { 
   
	 };
   
   return this;
}
```
After that you just add the path to your reporters to the reporters array in config.js and that's it .


## Setting Up Nightly Builds 

 You can just cron the testing script you created, and you got nightly builds combined with the PubSubReporter and your script that creates tickets.


# TODO 
* A nice UI using some customizable template on top of a templating engine that listens to the jasmine channel and builds the test results in real time. 
* Use the same UI template to generate reports into a file by nightly builds. 


Happy Test Automation.
