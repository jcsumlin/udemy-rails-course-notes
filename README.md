## Ruby views

```erb
<%= embedded ruby %>
<% %>
```


## Inheritance In Ruby:

```ruby
class ClassName < ParentClassName
end
```


## Routes

Routes are configured in the `config/routes.rb` file

```rb
Rails.application.routes.draw do
  root 'pages#home' # Rails auto adds _controller to the end of this string
  # adding a #hello means that rails is expecting a 'hello' method inside the application_controller
  get 'test', to: 'application#hello'
  get 'about', to: 'pages#about'
  resources :entries
  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
end
```

### Define a route that points to a `controller#action`

Have an appropriately named controller and action that describe the page being rendered
  - controller for static pages `pages_controller.rb`
  - action for homepage: 'def home'
  - If done this way Rails will expect a new folder in the 'views' folder named after the controller and an `.html.erb` file named after the action that exists within the controller.


We can generate controllers using the `rails` command line
 - `rails generate controller name_of_controller`
    - generates the `controller.rb` file and views folder for that controller

## Directory Structure of a Ruby on Rails Project
`app/assets`
  - images and stylesheets go here that we want to use in our pages
  - Also where configs live

`application.html.erb` >> root html page for rendering. (where header tags live)
`app/controllers` >> where all the controllers will live
 - `ApplicationController`: all other controllers we create will inherit this controller so it will house the default functionality

`app/helpers` >> where helper functions for view templates live
`app/javascript/packs/application.js` >> the main JS manifest file that makes JS available throughout the app
`app/models` >> database models, all the models we will create will extend the `application_record.rb` class
`app/views/layouts` >> where all the view templates live
`bin/` >> mostly ignore
`config/` >> where the app config files live, live env configs
`Gemfile` >> dependencies for the rails application
 - `bundle install` will install the dependencies and create the `Gemfile.lock`
 which describes the version numbers and dependencies of all your dependencies


### Deploying Rails Applications to Production

`git push heroku` <- pushes code to heroku and builds the website
`heroku login` <- login to heroku in cli


### Generate all the necessary files for a new database table

```shell
rails generate scaffold NameOfResource column:string column:text
```

Creates the:
- controllers
- views
- models
- routes
- test units
- helpers
- static assets
- and migrations


***This command is not really used in the fife project since its a mess***


```rb
resources :model_name
```
Gives us actions related to database models


```
> rails routes --expanded
will print all the routes for your application

> rails generate migration name_of_migration
This will generate a migration and time stamp it

> rails db:migrate
runs the rails migration files that are outstanding
The details of what the migrations made are contained in the /db/schema.sql file

> rails db:rollback
reverts the last migration file that was run
```

###### Example of a basic Migration file

```rb
def change
  add_column :name_of_tables, :created_at, :datetime
  add_column :name_of_tables, :updated_at, :datetime
end
```
**The create_at and updated_at columns are magic columns that are managed by rails**

## Naming conventions in Ruby on Rails

**Model name**: article

**Class name**: Article -> Capitalized A and singular, CamelCase

**File name**: `article.rb` -> singular and all lowercase, snake_case

**Table name**: articles -> plural of model name and all lowercase

### Minimal Model Example
```rb
class ModelName < ApplicationRecord
end
```
Models can communicate automatically with the database
Automatically inherits getters and setters to make and edit records

```
> rails console (or rails c) <- starts the rails console
```
You can test the connetion between the a Model and the database by typing the
name of the Model and .all
 - ModelName.all

### CRUD Operations in the Rails Console:
```
> ModelName.create(column1: "value")
```
- as long as you see the "commit transaction" message, the record was created

> Easily create new records by assigning the model to a new variable
```shell
>> model = ModelName.new
>> model.column1 = "this is a value"
```

To see the values stored in the model you just type the variable name in the console.

```shell
> model.save
```

This will save model to a new record using the data we set and set the variable to the returned row value (this will set created_at/updated_at values)

```shell
> exit
# Will pull you out of the rails console
```




##### More Examples of Rails console operations with models
```shell
  > Article.create(title: "first article", description: "Description of first article") # make sure Article is capitalized if using this method
  > article = Article.new
  > article.title = "second article"
  > article.description = "description of second article"
  > article.save
  > article = Article.new(title: "third article", description: "description of third article")
  > article.save
```


### RUD Operations using Rails Console

To get a specific row by id
```shell
> Article.find(1)
```
Read the first Article row
```shell
> Article.first
```
Read the last Article row
```shell
> Article.last
```

Updating a rows value in the rails Console
```shell
> article = Article.find(1)
> article.title = "Edited Title"
> article.save
```

Deleting a row in the Rails Console
```shell
> article = Article.last
> article.destroy
---- or ----
> Article.last.destroy
```


Enforcing Validation on database table using models
```rb
class Article < ApplicationRecord
  validates :title, presence: true
  validates :column_name, validation_rule: value, validation_rule2: value
  validates :column_name, length: {minimum: 3, maximum: 100}
end
```

If you try and save an Article now that does not have a title it will not save to the database
```shell
> article = Article.new
> article.save
=> false
> article.errors
=> Will return the ActiveModel::Errors Object
> article.errors.full_messages
=> Human readable error messages of why the Article didn\'t save
```

```shell
> reload!
```
Makes sure that your rails console has the latest changes in your code


## Adding New Models to routes.rb

```rb
resources :modelname, only: [:show]
```
- using the `only` keyword you can specify what routes you want to expose
  to the public
    - default options include [:show, :]
- There needs to be a corresponding controller for the Model named ModelNameController
- The new controller needs to have a corresponding action/function for each route you are exposing
- Each Controller action/function needs a view folder, template for the corresponding actions/function

To render specific records from a table you need an action that will use
the corresponding model to query the Database
```rb
def show
  @article = Article.find(params[:id])
end
```

`params` is the hash of all the parameters passed from the browser
Now within the view we can access the article with the given ID passed in through the url
@ in-front of a variable makes it an instance variable which is what makes it available inside the view

```html
<h1>Single Article View</h1>
<p><strong>Title:</strong> <%= @article.title =></p>
<p><strong>Descripotion:</strong> <%= @article.description =></p>

```

> Adding `byebug` to rails code will pause execution and open the ruby console
> typing `continue` into the console will resume execution


### Show List of Database Records using Models and routes

```rb
class ArticleController < ApplicationController
  def index
    @articles = Article.all
  end
end
```

```html
<h1>Articles Listing Page</h1>
<table>
  <thead>
    <tr>Title</tr>
    <tr>Description</tr>
  </thead>
  <tbody>
    <% @articles.each do |article| %>
    <tr>
      <td><%= article.title %></td>
      <td><%= article.description %></td>
    </tr>
    <% end %>
  </tbody>
</table>
```


### Ruby on Rails Forms

- There needs to be a new and create actions for the controller we are adding a form to
- Ruby on Rails gives us special form tools to be able to build forms that create new entries into our Database
- The `form_with` new helper is the preferred helper for rails and uses **ajax** to submit the form by default
- We will be using http POST instead, to do so we will add `local: true` in the parameters for `form_with`
- `form_for` uses HTTP POST by default
- We will need to whitelist the data coming in from the form. Rails has a security feature that prevents non-whitelisted data from being passed into the controller
  - This is called "strong params"

```rb
class ArticlesController < ApplicationController
  def new
  end

  def create
    render plain: params[:article]
    # This will render the data we input into the form after submit
    @article = Article.new(params.require(:article).permit(:title, :description))
    # Rails is smart enough to extract the values of the params article hash and save them to the Article table
    # .permit() is whitelisting the title and description from the article key in the params Hash
    @article.save
    # Save the permitted data to the database
    redirect_to articles_path(@article)
    # Pass the route path, rails can extract the ID from the Article object and use that to render the page
  end
end
```

```html
<h1>Create New Article</h1>

<%= form_with scope: :article, url: articles_path, local: true do |f| %>
  <p>
    <%= f.label :title %>
    <br/>
    <%= f.text_field :title %>
  </p>
  <p>
    <%= f.label :description %>
    <br/>
    <%= f.text_area :description %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>
```

### Adding Friendly Messages to the form views

- The validation will occur if the save fails.
- `.save` returns false if there are validation errors

```rb
class ArticlesController < ApplicationController
  def new
    @article = Article.new
    #ensures that when we load the page for the first time we have an Article variable
  end

  def create
    @article = Article.new(params.require(:article).permit(:title, :description))
    if @article.save
      flash[:notice] = "Successfully created article!"
      # Flash hashes have notice and error keys
      redirect_to articles_path(@article)
    else
      render 'new'
  end
end
```

```html
<h1>Create New Article</h1>
<% if @article.errors.any? %>
  <h2>The following errors prevented the article from being saved</h2>
  <ul>
    <% @article.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
  </ul>
<% end %>

<%= form_with scope: :article, url: articles_path, local: true do |f| %>
...
```

Flash messages need to be added to the root `application.html.erb` file so that they can be rendered

Minimal Example
```html
...
<body>
  <% flash.each do |name, msg| %>
    <%= msg =>
  <% end %>
</body>
...
```


### Updating Existing Article Records

Similar to the create action where we had a new and create method in the ArticlesController we will have an edit and update method to accomplish our goal.
First we need to expose the edit and update routes if we are using the only keyword in the routes file

```rb
resources :articles, only: [:show, :index, :new, :create, :edit, :update]
```
>Remember we can use the rails routes --expanded command to see the routes that are enabled in our app

Now we need to add the actions to the controller

```rb
class ArticlesController < ApplicationController
  ...
  def edit
  end
  def update
  end
end
```
