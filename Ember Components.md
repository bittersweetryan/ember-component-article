#Ember Components - Legos For Your Ember Applications

##Hello, Components

Web components promise to bring reusability and encapsulation to the next version of JavaScript.  Fournately if you are writing an Ember.js application the power of web components is in your hands today. In fact Ember's components aim to adhere as closely to the W3C web components spec as possiable.   

##Brief Introduction To Ember

Ember.js is an ambitious framework and covering it in detail is out of the scope of this article, however it is important to know about it's building blocks before we start talking about components.  

At a very high level Ember applications are composed of templates, routes, and controllers ( add).  Instead of having to wire all of these parts together, Ember conviently wires these parts together by sticking to strict naming conventions.  For example, lets say we want to create a part of our application the displays a XX.  Our application would have a template named XX, a route named XXRoute, and a controller named XXController.  Ember will automatically wire these parts of the application together for you.

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

[http://emberjs.jsbin.com/eQUmOfE/28/edit](http://emberjs.jsbin.com/eQUmOfE/28/edit)




