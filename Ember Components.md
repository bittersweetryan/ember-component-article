#Ember Components - Legos For Your Ember Applications

###Introduction

Web components promise to bring reusability and encapsulation to the next version of JavaScript.  Fournately if you are writing an Ember.js application the power of web components is in your hands today. In fact Ember's components aim to adhere as closely to the W3C web components spec as possiable.   

###Brief Introduction To Ember

Ember.js is a very ambitious web development framework and covering it in detail is out of the scope of this article, however it is important to know about its building blocks before we talk about components.

####Handlebars Templates

The Handlebars ([http://www.handlebarsjs.com](http://www.handlebarsjs.com)) templating framework is used heavily in Ember applications.  Simple helpers and variables in handlebars are surrounded by double curley braces like so: {{this-is-a-handlebars-variable}}.  Handlebars also has block level helpers that can be applied to a block of a template and have opening and closing tags, the {{#each}} opening and {{/each}} helper is a good example of a block level helper.  

####Routes

Routes in Ember manage an applications state through the browser's url. A route tells the application which model, or data, to populate it's templates with.  Each unique url in your application is controlled by a route.  

####Controllers

Controllers are used to manage the view state of an application. 

####Models

###Parts Of A Component


