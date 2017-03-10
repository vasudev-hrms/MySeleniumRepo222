var jasmine = require("./jasmine");
var config = require("./config");

var jasmineCore = jasmine.jasmineRequire.core(jasmine.jasmineRequire);
var env = jasmineCore.getEnv();
var describe = env.describe;
var to = env.to;
var xdescribe = env.xdescribe;
var it = env.it;
var xit = env.xit;
var fit = env.fit;
var beforeEach = env.beforeEach;
var afterEach = env.afterEach;
var beforeAll = env.beforeAll;
var afterAll = env.afterAll;
var expect = env.expect;
var pending = env.pending;
var fail = env.fail;
var spyOn =env.spyOn;

var any = jasmineCore.any;
var anything = jasmineCore.anything;
var objectContaining = jasmineCore.objectContaining;
var arrayContaining = jasmineCore.arrayContaining;
var stringMatching = jasmineCore.stringMatching;
var execute = env.execute;

var name = request.parameters["testName"];
if(name == null){
  name = "Test-" + Date.now();
}


for(var i =0;i < config.reporters.length;i++){
  
  var module = require(config.reporters[i]);
  var reporter = new module.Reporter({"name" :name});
  console.log("Using reporter " + config.reporters[i]);
  env.addReporter(reporter);
}

for(var i=0; i < config.matchers.length;i++){
  var matcher = config.matchers[i];
  var module = require(matcher);
  jasmineCore.Expectation.addCoreMatchers(module.matchers);
}

