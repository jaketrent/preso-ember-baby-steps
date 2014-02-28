
title: Agenda
class: fill nobackground

<br /><br /><br /><br /><br />

- Out the door
  <br /> (Ember Objects)
- Going sailing
  <br />(Ember Object Internal APIs)
- Finding friends
  <br />(APIs to Interface with Other Ember Objects)

![Shirt](images/bob/psych.jpg)

---

title: Getting Out the Door
subtitle: The Ember Objects
class: fill nobackground

![Germophobia](images/bob/tissue.png)

---

title: The Ember Way
subtitle: MVC via Route, Model, Controller, View, Component
class: segue centered

---

title: Ember Objects

![Ember objects](images/bob/emberobjects.png)

---

title: Route

- Define endpoints
- Fetch data
- Pass to controller

---

title: Model

- Data wrapper
- Knows and does things

---

title: Controller

- Display-related state
- Feels like a view model

---

title: View

- UI abstraction
- Display
- User Events

---

title: Components

- UI abstraction
- Display
- User Events

---

title: View vs Components
subtitle: An aside
class: segue dark

---

title: View vs Components
subtitle: An aside

<table>
  <tr>
    <th>View</th>
    <th>Components</th>
  </tr>
  <tr>
    <td>
      <ul>
        <li>HTML tags only</li>
        <li>Shared state w/ controller</li>
      </ul>
    </td>
    <td>
      <ul>
        <li>Custom tags</li>
        <li>Doesn't share state w/ anything</li>
        <li>Define your own controller w/in Component definition</li>
      </ul>
    </td>
  </tr>
</table>

---

title: I'm Sailing
subtitle: Ember Object Internal APIs
class: nobackground fill

![Sailing](images/bob/sailing.jpg)

---

title: Route

### Map route to URL

<pre class="prettyprint" data-lang="coffee">
App = Ember.App.create()

App.Router.map ->
  @resource 'supplies', ->
    @resource 'supply', { path: ':id'}
</pre>

### Fetch data (w/ ember-data)

<pre class="prettyprint" data-lang="coffee">
App.ApplicationAdapter = DS.FixtureAdapter.extend()

App.SuppliesRoute = Ember.Route.extend
  model: ->
    @store.find 'supply'
</pre>

---

title: Route (2)

### Change rendering options

<pre class="prettyprint" data-lang="coffee">
App.SuppliesRoute.reopen
  renderTemplate: ->
    @render 'diffTemplateName',
      outlet: 'aNamedOutlet'
      controller: getArbitraryController()
</pre>

---

title: Model

### Define attributes

<pre class="prettyprint" data-lang="coffee">
App.Supply = DS.Model.extend
  description: DS.attr 'string'

  lose: -> # ...
  share: -> # ...
  wrapInTissue:- > # ...
</pre>

### Define fixture data

<pre class="prettyprint" data-lang="coffee">
App.Supply.FIXTURES = [
  { id: 1, description: 'Gill' }
  { id: 2, description: 'T-Shirt' }
  { id: 3, description: 'Tissues' }
]
</pre>

---

title: Controller

### Add UI-only properties

<pre class="prettyprint" data-lang="coffee">
App.SupplyController = Ember.ObjectController.extend
  isWrappedInTissue: false
</pre>

---

title: Template

### Display data

<pre class="prettyprint" data-lang="vacation-supplies.emblem">
ul
  each itemController="supply"
    li #{description} (#{isWrappedInTissue})
</pre>

---

title: View

### Abstract parts of the display, sharing state

<pre class="prettyprint" data-lang="coffee">
App.SupplyController = Ember.ObjectController.extend
  isWrappedInTissue: false

App.TissueWrappableView = Ember.View.extend
  templateName: 'germophobe'
</pre>

<pre class="prettyprint" data-lang="vacation-supplies.emblem">
each itemController="supply"
  App.TissueWrappableView
</pre>

<pre class="prettyprint" data-lang="germophobe.emblem">
li #{description} (#{isWrappedInTissue})
</pre>

<p hidden>http://emberjs.jsbin.com/zotewedi/2/edit</p>

---

title: Component

### Abstract parts of the display, isolating state

<pre class="prettyprint" data-lang="coffee">
App.TissueWrappableComponent = Ember.Component.extend
  tagName: 'li'
  wrapped: false
</pre>

<pre class="prettyprint" data-lang="vacation-supplies.emblem">
each itemController="supply"
  tissue-wrappable wrapped=isWrappedInTissue
</pre>

<pre class="prettyprint" data-lang="components/tissue-wrappable.emblem">
#{description} (#{wrapped})
</pre>

<p hidden>http://emberjs.jsbin.com/loboyecu/1/edit</p>

---

title: Finding Friends
subtitle: APIs to Interface with Other Ember Objects
class: fill nobackground

![Friends](images/bob/talking.jpg)

---

title: Ember Objects

![Ember objects](images/bob/emberobjects.png)

---

title: Route -> Controller

### Access a different controller

<pre class="prettyprint" data-lang="coffee">
  App.SupplyRoute = Ember.Route.extend
    setupController: (controller, supply) ->
      @_super controller, supply
      @controllerFor('supplies').set 'currentSupply', supply
</pre>

---

title: Controller -> Controller

### Get to parent controller from item controller

<pre class="prettyprint" data-lang="coffee">
App.SuppliesController = Ember.ArrayController.extend
  suppliesCategory: "For germophobes"

App.SupplyController = Ember.ObjectController.extend()
</pre>

<pre class="prettyprint" data-lang="emblem">
ul
  each itemController="supply"
    li #{description} (cat: {{parentController.suppliesCategory}}
</pre>

<p hidden>http://emberjs.jsbin.com/xuyalolu/2/edit</p>

---

title: Controller -> Controller (2)

### Make dependencies from one controller to another
<pre class="prettyprint" data-lang="coffee">
App.SuppliesController = Ember.ArrayController.extend
  numSupplies: -> # ...

App.SupplyController = Ember.ObjectController.extend
  needs: ['supplies']
</pre>

<pre class="prettyprint" data-lang="supply.emblem">
{{controllers.supplies.numSupplies}}
</pre>

<p hidden>http://emberjs.jsbin.com/nuxiqaja/11/edit</p>

---

title: View -> View

### Reference parent view
<pre class="prettyprint" data-lang="coffee">
App.TissueWrappableView = Ember.View.extend
  templateName: 'tissue-wrappable'
  probablyContaminated: true

App.InnerPeaceView = Ember.View.extend
  templateName: 'inside-the-wrapper'
</pre>

<pre class="prettyprint" data-lang="tissue-wrappable.emblem">
p Wrapping!
App.InnerPeaceView
</pre>

<pre class="prettyprint" data-lang="inside-the-wrapper.emblem">
p Inside!
p= view.parentView.probablyContaminated
</pre>

<p hidden>http://emberjs.jsbin.com/belowimo/6/edit</p>

---

title: View -> Template

<pre class="prettyprint" data-lang="coffee">
App.IsHandShuckedView = Ember.View.extend
  domDidChange: (->
    $ears = @$ 'input[type=checkbox]'
    # ...
  ).on 'change'
</pre>

---

title: Component -> Controller

<pre class="prettyprint" data-lang="coffee">
App.BabyStepsController = Ember.ArrayController.extend
  actions:
    breathe: -> # ...
    callElevator: -> # ...

App.BabyStepComponent = Ember.Component.extend
  actions:
    take: ->
      @sendAction 'step'
</pre>

<pre class="prettyprint" data-lang="baby-steps.emblem">
baby-step step="breathe"
</pre>

<pre class="prettyprint" data-lang="components/baby-step.emblem">
p Take it!
button click='step'
</pre>

<p hidden>http://emberjs.jsbin.com/dobagike/1/edit</p>

---

title: Misc -> Misc

Route -> (Route's own) Controller
<pre class="prettyprint">@get 'controller'</pre>

View -> View
<pre class="prettyprint">@get ‘childViews’ </pre>

Component -> Template
<pre class="prettyprint">@get('element')</pre>

---

title: Misc -> Misc (2)

Template -> Controller
<pre class="prettyprint">controllers.neededController.property</pre>

Template -> View
<pre class="prettyprint">view.property</pre>

Template -> Model
<pre class="prettyprint">
this
property
model.property
</pre>

---

title: Baby Steps?
class: fill nobackground
build_lists: true

- Use tissues, if needed, but start
- Lots of APIs - Look at code, look at docs
- Write some code.  It's fun!
- Don't feel like you're committing forever
- There are many adventures to come!

![baby steps](images/bob/car.jpg)