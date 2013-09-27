#Ember Components - Legos For Your Ember Applications

##Hello, Components

Web components promise to bring reusability and encapsulation to the next version of JavaScript.  Fournately if you are writing an Ember.js application the power of web components is in your hands today. In fact Ember's components aim to adhere as closely to the W3C web components spec as possiable.   

##Brief Introduction To Ember

Ember.js is a very ambitious web development framework and covering it in detail is out of the scope of this article, however it is important to know about its building blocks before we talk about components.  Ember applications are composed of templates, routes, controllers, and models.  Instead of having to wire all of these parts together, Ember conviently wires these parts together by sticking to strict naming conventions.  For example, lets say we want to create a part of our application the displays a XX.  Our application would have a template named XX, a route named XXRoute, and a controller named XXController.  Ember will automatically wire these parts of the application together for you.

###Handlebars Templates

The Handlebars ([http://www.handlebarsjs.com](http://www.handlebarsjs.com)) templating framework is used heavily in Ember applications.  Simple helpers and variables in handlebars are surrounded by double curley braces like so: {{this-is-a-handlebars-variable}}.  Handlebars also has block level helpers that can be applied to a block of a template and have opening and closing tags, the {{#each}} opening and {{/each}} helper is a good example of a block level helper.  

###Routes

Routes in Ember manage an applications state through the browser's url. A route tells the application which model, or data, to populate it's templates with.  Each unique url in your application is controlled by a route.  

###Controllers

Controllers are used to manage the view state of an application. Ember applications have three types of controllers available to them: generic controllers, array controllers, and object controllers.  Each type of controller is disginguished by the way the underlying models are represented.  

Views that have their data stored in Array controllers have their models represented as an array of models and can use the {{#each}} handlebars helper to iterate through each item in the underlying array.  

Views that have their data stored in object controllers proxy data to thye model that is associted with the controller. For example, a view that refrences a property called {{name}} will have a `name` property on the controller which will by synconrized with a `name` property on the controllers model.  

Controllers also respond to the actions in a view.  

###Models

Models in Ember applications can be plain JavaScript objects or representations of the much more robust ember-data framework.  

###Adding Components To The Story

Ember components are a way to take parts of your Ember application and allow them to be reused in different parts of the application or in different applications alltogether.  Components do not have access to the properties of the view they are contained in, data is explicitly passed into a compoonent in it's property.

_Ember component names must include a dash in them, this is to adhere as closely to the W3C spec as possible.  This is done to avoid potential tag naming conflicts._

####The Component Object

Component views are backed by a Component object.  

####Block Form Templates 

####Attribute Bindings

####Actions

###Demo

[http://emberjs.jsbin.com/eQUmOfE/5/edit](http://emberjs.jsbin.com/eQUmOfE/5/edit)




