#Ember Components - Build Reuseable Legos For Your Ember Application

##Hello, Components

Web components promise to bring reusability and encapsulation to the next version of JavaScript. However, until mainstram browsers fully implment the ECMAScript 6 specification the power of Web Components is out of reach.  Fournately for Ember.js developersthe power of web components can be leveraged today.  Ember components share more than a similar name with Web Components; Ember's components aim to adhere as closely to the W3C Web Component spec as possiable.

##Brief Introduction To Ember

Before we dig into components a brief intorduction to Ember.js is needed.  If you are already familiar with Ember you can skip ahead to the _Adding Components To The Story_ section.  Ember.js is an ambitious web application development framework and covering it in detail is out of the scope of this article, however the following section should give you the introduction needed to the building blocks of an Ember that is needed to start talking about components.  

At a very high level Ember applications are composed of templates, routes, and controllers.  These parts are automatically wired together by Ember.js by sticking to strict naming conventions.  For example, a foo object would have a template named `foo`, a route named `FooRoute`, and a controller named `FooController`.  Our application would have a template named XX, a route named XXRoute, and a controller named XXController.  Lets take a minute to talk about each of these parts. 

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

Models in Ember applications can be plain JavaScript objects or representations of the much more robust ember-data framework.  For the sake of this article we'll use Plain Old JavaScript Objects to represent our model data. 

##Adding Components To The Story

Ember components allow developers to abstract out common display elements and reuse them in other parts of the application or even in other applications. Components are discrete chunks of your application's view with encapsulated actions and properties.  Because of this data must be explicitly passed into a compoentnt and actions must be explicitly passed out of the component into it's enclusing controller.  

In this article we'll build a simple backlog management application. This application will manage two backlogs: the active backlog for current work items and an archive backlog that will store completed items. Since each of these lists will be displaying backlog items as cards we'll create a reuseable Ember component to represent each card.  

###It all starts with a template.  

Ember components start with a template object that can be injected into another view using it's component name.  Like any Ember view a component's markeup is written using the  Gandlebars templating language.  Like evertying else in Ember, there is a strict naming convention that is used to define a component: it's template name must start with the word `component\`.  Following the [W3C spec](http://www.w3.org/TR/2013/WD-components-intro-20130606/#custom-element-section) Ember component names must include a hypen.   This is done to avoid potential naming conflicts with other HTML tags.  With this in mind let's create our scrum card template and give it the name of `components\scrum-card`:

```
  <script type="text/x-handlebars" data-template-name="components/scrum-card">
    <article>
        <h1 class="card-title">{{ card.title }}</h1>
        <ul>
        {{ #each card.tasks }}
          <li>
            <div>
              <div class="inline" >{{ input type="checkbox" checked=completed }}</div>
              <div {{ bind-attr class=":inline completed:isCompleted" }}>
                <div><strong>{{ title }}</strong></div>
                <div><label>Assigned to: </label>{{ assignedTo }}</div>
              </div>
            </div>
          </li>
        {{ else }}
        	<li>There are no tasks for this card.</li>
        {{ /each }}
        </ul>
    </article>
  </script>
```

####Breaking down the template

Ther are a lot of things going on in this template so let's spend some time going over them before we continue.  One of the first thing's you'll notice is that it is refrencing properties of a `card` object.  **This object must be explicitly passed into the component, the component has no access to the data that is available to it's parent view.**  

A few lines down this template is making use of the `{{#each}}` handlebars helper  This helper will output the html between the opening and closing each tags one time for each item in an array. Near the end of the loop there is also the `{{else}}` tag, it's content's will be displayed if the array passed into the each block is empty.  

A few lines into our `{{#each}}` statement there is an Ember {{input}} view helper object: `{{input type="checkbox" checked=completed }}`.  This piece of code is particularlay interesting becuase it will reach out of the templates encapsulation and update a property on the object that is being passed into the template.  The next line includes a bit of Ember magic as well by using the `{{bind-attr}}` helper to bind an elements class name to the property of an object.  In this case if the object's completed property is true the `div` will get a class of `isCompleted`.  
 

####Adding the Component Into Templates

Once the component template is defined inserting it into a template is as easy as adding a variable to your template; just add insert it using the handlebars variable syntax.  To insert our scrum card component, we'd use the following syntax `{{scrum-card}}`.  Again, becuase components are encapsulated they do not have direct access to the variables that their containing  template has access to.  

Properties are passed into components by adding them to the component's helper tag as key/value pairs.  Looking back at our component's template, it makes heavy use of a object variable called `card`.  In order to pass the data for a card into our component we'd simpley add a `card=value` inside the `{{scrum-card}}` helper.  _Note: the name of the variable that has the data outside of the component can be named anything, it just has to be passed into the component with the correct name.  

Below is an example of looping through an ArrayController's model and passing in `card` information into the `scrum-card` component:

```
  <script type="text/x-handlebars" data-template-name="index">
    <h1>Active Backlog</h1>
    {{ #each pbi in model }}
      {{ scrum-card card=pbi }}
    {{ /each }}
  </script>
```


###The Component Object

Perhaps by this point you've asked yourself a few questions.  What element will ember wrap my component in when it's inserted into the DOM?  How do I get my components to respond to user interactions?  

The `Ember.Component` object solves 
Because components are encapsulated, actions triggered on views do not bubble past the boundries of the view.  The `Ember.Component` object solves this issue.  In addition when Ember inserts a component into the DOM it is wrapped in a `<div>` element by default

The component object is created by extending the `Ember.Component` object.  You won't be surprised to know that the name of this object is determined by Ember's conventions, a component's object should be named: <i>ComponentName</i>Component (note that the hyphen based naming convention is replaced by a camel cased naming convention).  Looking back again at our `scrum-card` component example the Component Object that would back it would be named `ScrumCardComponent`.  

```
App.ScrumCardComponent = Ember.Component.extend( {
   …
} );
```

###Responding To Actions

In order to send actions to the component object the `{{action 'actionName' }}` helper is used.  Action helpers are nested inside of HTML tags, for example to send an action called _itWasClicked_ to the a component when a user clicks on a `<div>` I'd add the following to my handlebars template: `<div {{action 'itWasClicked'}}>`.  

In order to have our Component Object respond to actions it must have a property named `actions` (see the [ember documentation]((http://emberjs.com/guides/templates/actions/) for more information about implementing actions in templates and controllers ).  Each action property will have the same name as defined in the action with the value of it being a function that will get executed when the action is triggered. 

To demonstrate this let's add a button to our application that will allow us to mark a card as "done".  First we need to add mark up to our template, after the `ul` that makes up our tasks list.  

```
{{ #unless card.complete }}
  <button {{ action 'done' card }}>Done</button>
{{ /unless }}
```      
In addition to our action helper  the end of our template's markup we use the `{{#unless}}` block helper.  This tells handlebars to display the contents between the `{{#unless}}` and `{{/unless}}` tags only if the property passed into the `{{unless}}` helper is `false`.  If we wanted to display the markup if the property was `true` we could have used the Ember `{{#if}}` helper.  

###Passing Events To Controllers

There are a number or situations where an application's controllers need respond to events from a component. In order to do this we need to explicitly tell our templates what actions to expect by setting an `action` attribute in our template declaration.  Events won't be sent by the component until the component object calls the `sendAction()` method.   

```
 {{ scrum-card card=card action="markCardDone" }}
```

In the scrum card application our controllers will need additional information attached to an event, in this case exactly what card was clicked on.  In order to do this we have to call our `sendAction` method with at least two parameters; the first one is the string of `action` and any additional parameters are passed after.  Controllers can intercept and respond to actions by adding them to their actions object.  Lets look at how our scrum card application sends "done" events to the controller object and then along to a controller.

First we add the action to our Object Controller:

```
App.ScrumCardComponent = Ember.Component.extend({
  actions : {
    done : function(){
      this.sendAction( 'action', this.get( 'card' ) );
    }
  }
} );
```

Second we rrespond to the action in our application's controller:

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

####Block Form Templates 


###Demo

A working demo of the scrum card application can be found here: 

[http://emberjs.jsbin.com/USoHuju/1/edit](http://emberjs.jsbin.com/USoHuju/1/edit)




