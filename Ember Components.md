#Ember Components - Build Reuseable Legos For Your Ember Application

##Hello, Components

Web components promise to bring reusability and encapsulation to the next version of JavaScript. The bad news is that until mainstram browsers fully implment the ECMAScript 6 standards that make up web components, their power is out of reach.  Fournately for Ember.js developers the power of web components can be leveraged today in the form of Ember components.  Ember's components share more than a similar name with Web Components; Ember's components aim to adhere as closely to the W3C Web Component spec as possiable.

##Brief Introduction To Ember

Before we dig into Ember components a brief intorduction to Ember.js is needed (if you are already familiar with Ember you can skip ahead to the _Adding Components To The Story_ section). Ember.js is an ambitious web application development framework and covering it in detail is out of the scope of this article, however the following section should give you the knowledge of Ember's building blocks of an Ember that is needed to start talking working with components.  

At a very high level Ember applications are composed of templates, routes, and controllers.  Ember automatically wires all of these components together by sticking to strict naming conventions.  For example, a `foo` object would have a template named `foo`, a route named `FooRoute`, and a controller named `FooController`.  Let's take a look at each of these parts. 

###Handlebars Templates

Templates in Ember applications are written with The Handlebars ([http://www.handlebarsjs.com](http://www.handlebarsjs.com)) templating framework. Variables and helpers in handlebars templates are surrounded by double curly braces; `{{` and `}}`. In addition to the helpers that come with Handlebars Ember extends Handlebars with some Ember specific helpers, a few that you'll see a lot of in an Ember application are:  `action` helpers which send actions to routes and controlers, `link-to` helpers which are used to modify an appliations state through an applications routes, and `bind-atr` helpers that bind attributes to the properties of a controller. 

###Routes

Routes in Ember manage the application's state through the url.  At a very high level a route tells the application which view information to display and what model (or data) to use to popluate it with.  Routes and views are bound through ember's naming conventions.  A view with a name of `Foo` will have a corresponding route named `FooRoute`, if this route does not exist Ember will create it for you, in memory, behind the scenes. 

###Controllers

Controllers are used to add logic and events to an application's views. Controllers also act as proxies for a view's model data. There are three types of controllers in Ember: base controllers, array controllers, and object controllers.  Each type of controller is disginguished by the way the underlying models are represented.  

Array controllers back views that have their model data represented as an array.  Array controllers and the views backed by them have special methods available to them.  For example, a view backed by an ArrayController can use the `{{each}}` helper to loop through each of the models represented in a controller.  Array controllers also have direct access to Ember's Array methods, for example calling `this.filter()` on an ArrayController will run the filter on each of the controller's models.  

Views that have their data stored in object are represented by `ObjectControllers`.  What makes `ObjectControllers` unique is that they proxy data to thye model that is associted withit. For example, a view that refrences a variable called {{name}} will get its `name` value from the controller which will look for a `name` property on it's model.  

Controllers are also used to respond to events triggered on a view.  In ember events are represented by `actions`.  Views use the `{{action}}` helper to send events to the controller and the controller uses an `actions` property to store callback functions for events.  

###Models

Models in Ember applications can be plain JavaScript objects or representations of the much more robust ember-data framework.  

##Back to Components

Ember components allow developers package together common display elements and functionality in an encapsulated mannor that allows them to be urused throughout an application or even in other applications. Because of this encapsulation data must be explicitly passed into a compoentnt and actions must be explicitly passed out of the component into it's enclusing controller. This is an important concept to grasp so let me put it another way.  All data that is used by a view must be maunally passed into the view through the components tag.  In addition for a component to have an event that transends the boundries of that compoennt the programmer must manually do so from the component's code. 

In this article we'll build a simple scrum backlog management application that utilizes the power of component.  To display this power this application will manage two different backlogs (if you are unfamiliar with the concept of a back log, just think of it as a list of items that need to be done): the active backlog which has current work items and an archive backlog that will store completed items.  In this article, I'll call each item in a backlong a "card".

###Get Boostraped

The quickest way to get started building an Ember application is with the [Ember Starter Kit] (https://github.com/emberjs/starter-kit/archive/v1.0.0.zip).  This is a zip file that includes Ember and all of it's dependencies.  There is also a very convienent custom JS Bin that comes with the Ember Starter Kit already configured at [http://emberjs.jsbin.com/](http://emberjs.jsbin.com/).  We'll use the JS Bin version of the starter kit in this article.

###It all starts with a template.  

All Ember components must have a template. Like evertying else in Ember, there is a strict naming convention that must be adhered to.  The first part of this convention says that the template's name must start with the text `component\`, followed by the name of the component.  The second part of this convention is that the name of the component **must include a hyphen**.  This convention follows the [W3C spec](http://www.w3.org/TR/2013/WD-components-intro-20130606/#custom-element-section), which also declares that a web compoennt name must include a hyphen. This convention is in place to avoid potential naming conflicts with other HTML tags.  

Keeping the naming conventions in mind its time to create the scrum card template and give it the name of `components\scrum-card`.  Add the following code to your HTML tab:

```
  <script type="text/x-handlebars" data-template-name="components/scrum-card">
    <article>
        <h1 class="card-title">{{card.title}}</h1>
        <ul>
        {{#each card.tasks}}
          <li>
            <div>
              <div class="inline" >{{input type="checkbox" checked=completed }}</div>
              <div {{bind-attr class=":inline completed:isCompleted"}}>
                <div><strong>{{title}}</strong></div>
                <div><label>Assigned to: </label>{{assignedTo}}</div>
              </div>
            </div>
          </li>
        {{else}}
          <li>There are no tasks for this item</li>
        {{/each}}
        </ul>
        {{#unless card.complete}}
        <button {{action 'done' card }}>Done</button>
        {{/unless}}
    </article>
  </script>
```

####Lets break that down

There are a lot of things going on in this template so let's spend some time going over each of them before we continue.  One of the first thing's you'll notice is that the handlebars expressions are refrencing properties of a `card` object. **This object must be explicitly passed into the component upon creation, it has no access to the data that is available to it's parent view.**  This is different than any other Ember view where a view has access to the properties of it's controller.  

A few lines down this template is making use of the `{{#each}}` handlebars helper.  This is an Array helper and will loop through all the items in the array displaying the HTML between the opening and closing `each` tags once for each item in an array. Near the end of the loop there the template also uses  `{{else}}` tag.  The content inside the `else` tag only will be displayed if the array passed into the each block is empty or does not exist.

This view also makes use of a special type of input tag called an Ember input view helper : `{{input type="checkbox" checked=completed }}`.  Ember view helpers differ from standard html input tags becuase they allow developers to easily bind properties to an existing model.  In this case, the models `completed` property is bound to the checkbox's `checked` property.  This piece of code is also particularlay interesting becuase it seemingly breaks the components tight encapsulation and will update the values of the object that was passed into the component.

The next line includes another Ember specific Handlebars helper, `{{bind-attr}}`.  This helper to binds an elements class name to the property of an object. In this case the `div` will get a class of `isCompleted` when the model's completed property is true.  Looking back to the explanation of the Ember input view helper you'll see that when the input is checked the value of the completed property will change which will also change the class of the div.  That was a mouthful, perhaps loking at the appplication [here](http://emberjs.jsbin.com/USoHuju/1/edit) will give you a clearer picture.  
 

####Adding the Component Into Templates

Once the component is defined inserting it into a template is as easy as adding an expression to a Handlebars template. In order to add scrum-card component to a tempate simply add the following code in a handlebars template:  `{{scrum-card}}`.  Since the component does not have access to the same controller data that it's parent template has the component will not have any of it's expressions replaced by valid data. 

In order to provide our component with data properties are passed into a component by adding them to the component's expression as attributes.  Looking back at our component, it makes heavy use of a variable called `card`.  In order to pass the card data into the component simpley add a `card=` attribute inside the `scrum-card` helper and set it's value to a valid card object.  _Note: the name of the variable that holds the data does not have to be the same name as the variable used in the component.  The right hand side of the attribute must be the name of the variable used in the component and the left hand side of the attribute can be any valid variable._  

The example below loops through an ArrayController's model and passing in the required `card` information into the `scrum-card` component (Notice how the card information passed into the controller is in a variable called pbi):

```
  <script type="text/x-handlebars" data-template-name="index">
    <h1>Active Backlog</h1>
    {{ #each pbi in model }}
      {{ scrum-card card=pbi }}
    {{ /each }}
  </script>
```


###The Component Object

Perhaps by this point you've asked yourself a few questions. Can I control what element Ember will use to wrap my component when it's inserted into the DOM?  How do I get my components to respond to events?  Where are properties that only my controller uses stored?  The answer to all of these questions lies in the `Ember.Component` object.  

In order to create a `Ember.Component` object for a component just extend `Ember.Component`. As with most things in Ember, naming conventions will determin this object's name, it is a camel case representation of the component's name. The follwing code creates the component object for our scrum card.

```
App.ScrumCardComponent = Ember.Component.extend( {
   …
} );
```

####Sending and Responding to Actions

In order to send actions to the component object the `{{action 'actionName' }}` helper is used.  Action helpers are nested inside of HTML tags.  For example, to send an action called _itWasClicked_ to the a component's object when a user clicks on a specific `<div>` the following markup should be added a component's template: `<div {{action 'itWasClicked'}}>`.  

In order to have our Component Object respond to actions it must have a property named `actions` which will be an object (see the [ember documentation]((http://emberjs.com/guides/templates/actions/) for more information about implementing actions in templates and controllers ).  Each property of the action object will have the same name as the action defined in the action helper and the value of each property is a function that will be executed when the event is triggered.

To demonstrate this let's add a button to our scrum card component that will allow us to mark a card as "done". In order to do this we'll add a `<button>` to our template that will send the "done" action to our component. In order need to tell the action handler know what card we are marking as done we'll pass the card variable to the action by adding it as an attribute to the action helper.

In order to make our UI as simple to use as possiable we'll hide the done button for tasks that have already been completed. To do this Handlebars provides us with the `{{#unless}}` block helper.  This helper tells Handlebars to display the contents between the `{{#unless}}` and `{{/unless}}` tags only if the value of the property passed into the  helper is `falsy`<sup>1</sup>.  The mirror image of `{{unless}}` is the `{{if}}` and `{{/if}}` helpers which display information only if the value or the property passed into the helper is `truthy`<sup>2</sup>.

Using what we just learned lets add the done button to our scrum-card by adding the following markup:
```
{{ #unless card.complete }}
  <button {{ action 'done' card }}>Done</button>
{{ /unless }}
```      

We'll also create our object controller and tell it to respond to `done` actions by adding the following JavaScript to our application:

`
App.ScrumCardComponent = Ember.Component.extend({
  actions : {
    done : function(){
      this.sendAction( );
    }
  }
`

####Passing Events To Controllers

There are a number or situations where an application's controllers need respond to events from a component. In order to do this we need to explicitly tell the template that includes the component what actions the component will send by setting an `action` attribute in our template invocation. These events are then sent to the controller through a `sendAction()` method implmemented on the component's object which is then responded to by a method in the controller's action object.     

Lets start to add this functionality to our scrum card component by adding a `action` attribute to our invocation markup and giving it a value of `markCardDone` (it will be up to the parent template's controller to respond to the `markdCardDone` event): 

```
 {{ scrum-card card=card action="markCardDone" }}
```

Before we can tell the controller to respond to the `markCardDone` action will also need to know what card was clicked on. In order to do this  the `done` method needs to be refactored to add two parameters to `sendAction`: the first one is a string with the text `action` and the second one is the card that was clicked on. To send more than one parameter into the controller simply add them as additional parameters to the `sendAction` method.  Controllers can intercept and respond to actions by adding them to their actions object.  Lets look at how our scrum card application sends "done" events to the controller object and then along to a controller.

First we add the action to our component's object:

```
App.ScrumCardComponent = Ember.Component.extend({
  actions : {
    done : function(){
      this.sendAction( 'action', this.get( 'card' ) );
    }
  }
} );
```

Second we will rspond to the action in our application's controller, accepting the `card` information as an argument:

```
App.IndexController = Ember.ArrayController.extend({
  actions : {
    markCardDone : function( card ){
        this.set( 'model', this.without( card ) );
        archiveItems.push( card );
    }
  }
});
```

####Changing the Component's HTML Element

Lets say we want to wrap our component in a more semantic HTML tag like `<section>`.  To do this simply add a `tagName` property to the component object like so:

```
App.ScrumCardComponent = Ember.Component.extend({
	tagName : 'section',
	…
} );
```

##There's Much More to Learn

This tutorial covered many of the basic use caes of Ember components, but its far from an exhaustive review.  For example you can build components that allow users to pass in their own markup for part of all of the component using block level compoonent tags or bind differnet class information to our component based on the values of it's properties.  To read more on components check out the excellent documentation on the Ember.js website at [http://emberjs.com/guides/components/](http://emberjs.com/guides/components/).  I'd also recommend you take a few hours and walk through the [Getting Started](http://emberjs.com/guides/getting-started/) guide which will walk you through building a complete Todo application using components.


###Demo

A working demo of the scrum card application can be found here: 

[http://emberjs.jsbin.com/abOviQu/1/edit](http://emberjs.jsbin.com/abOviQu/1/edit)





