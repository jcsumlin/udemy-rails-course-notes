### Routes
Routes are configured in the ***config/routes.rb*** file

Rails.application.routes.draw do
  root 'pages#home' # Rails auto adds _controller to the end of this string
  # adding a #hello means that rails is expecting a 'hello' method inside the application_controller
  get 'test', to: 'application#hello'
  get 'about', to: 'pages#about'
  resources :entries
  # For details on the DSL available within this file, see https://guides.rubyonrails.org/routing.html
end

Define a route that points to a controller#action

Have an appropriatelty named controller and action that describe the page being rendered
  - controller for static pages 'pages_controller.rb'
  - action for homepage: 'def home'
> If done this way Rails will expect a new folder in the 'views' folder named after
> the controller and an 'html.erb' file named after the action that existes within
> the controller.


We can generate controllers using the `rails` command
 - rails generate controller name_of_controller
    - generates the controller.rb file and views folder for that controller


app/assets >> images and stylesheets go here that we want to use in our pages
  - Also where configs live
application.html.erb >> root html page for rendering. (where header tags live)
app/controllers >> where all the controllers will live
  - ApplicationController: all other controllers we create will inherite this
  controller so it will house the default functionality

Inheritance: class ClassName < ParentClassName

app/helpers >> where helper functions for view templates live
app/javascript/packs/application.js >> the main JS manifest file that makes JS avaliable throughout the app
app/models >> database models, all the models we will create will extend the application_record.rb class
app/views/layouts >> where all the view templates live
bin/ >> mostly ignore
config/ >> where the app config files live, live env configs

Gemfile >> dependencies for the rails application
 - `bundle install` will install the dependencies and create the Gemfile.lock
 which describes the version numbers and dependencies of all your dependencies


### Deploying Rails Applications to Production

git push heroku <- pushes code to heroku and builds the website
heroku login <- login to heroku in cli


#### Generate all the necessary files for a new database table
rails generate scaffold NameOfResource column:string column:text
 - This command is not really used in the fife project since its a mess

resources :entries <- gives us actions related to database models


> rails routes --expanded <- will print all the routes for your application

> rails generate migration name_of_migration
This will generate a migration and time stamp it
> rails db:migrate
runs the rails migration files that are outstanding
The details of what the migrations made are contained in the /db/schema.sql file
> rails db:rollback
reverts the last migration file that was run


def change
  add_column :name_of_tables, :created_at, :datetime
  add_column :name_of_tables, :updated_at, :datetime
end

The create_at and updated_at columns are magic columns that are managed by rails


Model name: article

Class name: Article -> Capitalized A and singular, CamelCase

File name: article.rb -> singular and all lowercase, snake_case

Table name: articles -> plural of model name and all lowercase

## Model to communicate with the database
## Automatically inherits getters and setters to make and edit records
class ModelName < ApplicationRecord

end


> rails console (or rails c) <- starts the rails console
You can test the connetion between the a Model and the database by typing the
name of the Model and .all
 - ModelName.all

CRUD Operations in the Rails Console:

> ModelName.create(column1: "value")
    - as long as you see the "commit transaction" message, the record was created
Easily create new records by assigning the model to a new variable
      > model = ModelName.new
      >  model.column1 = "this is a value"
          - To see the values stored in the model you just type the variable
            name in the console.
      > model.save
        - this will save model to a new record using the data we set and set
          the variable to the returned row value (this will set created_at/updated_at values)
>> exit
will pull you out of the rails console


More Examples of Rails console operations with models
  > Article.create(title: "first article", description: "Description of first article") # make sure Article is capitalized if using this method
  > article = Article.new
  > article.title = "second article"
  > article.description = "description of second article"
  > article.save
  > article = Article.new(title: "third article", description: "description of third article")
  > article.save


RUD Operations using Rails Console

To get a specific row by id
> Article.find(1)
Read the first Article row
> Article.first
Read the last Article row
> Article.last

Updating a rows value in the rails Console
> article = Article.find(1)
> article.title = "Edited Title"
> article.save

Deleting a row in the Rails Console
> article = Article.last
> article.destroy
---- or ----
> Article.last.destroy


Enforcing Validation on database table using models
class Article < ApplicationRecord
  validates :title, presence: true
  validates :column_name, validation_rule: value, validation_rule2: value
  validates :column_name, length: {minimum: 3, maximum: 100}
end

If you try and save an Article now that does not have a title it will not save to the database
> article = Article.new
> article.save
=> false
< article.errors
=> Will return the ActiveModel::Errors Object
> article.errors.full_messages
=> Human readable error messages of why the Article didn\'t save


> reload!
Makes sure that your rails console has the latest changes in your code
