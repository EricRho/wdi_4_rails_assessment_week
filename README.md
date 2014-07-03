<!-- # Week 4 Comprehensive Assessment

Fork this repository, update this file to include your answers, and submit a pull request. You may refer to any notes, past projects, or external resources you want. The questions do not have to be completed in order.

1. My app has Students who are organized into Houses. What lines should I add to `config/routes.rb` to create a complete set of nested RESTful routes for these resources? -->

resources :houses do
  resources :students
end

root 'houses#index'

<!-- 2. Write a migration to create a `students` table with columns for name, age, and something to link the student to the `House` they belong to. -->

rails g migration CreateStudents name:string age:integer house:references


<!-- 3. Assuming a Student can only be in a single House, what kind of associations should I define on these models? Do I need to introduce any new models, and if so, what associations should I define on those?
 -->

in student model:

class Student < ActiveRecord::Base
  belongs_to :house
  validates! :house_id, uniqueness { scope: [:student_id]}
end

house model:

class House < ActiveRecord::Base
  has_many :students
end


<!-- 4. Now I want Students to be able to enroll in Courses. What kind of associations should I define on these models? Do I need to introduce any new models, and if so, what associations should I define on those?
 -->

student model:

class Student < ActiveRecord::Base
  belongs_to :house
  validates! :house_id, uniqueness {scope: [:student_id]}
  has_many :courses
end

create new course model:

class Course < ActiveRecord::Base
  belongs_to :student
  has_many :students
end


<!-- 5. I want Students to be able to leave Comments on both Courses and Professors. What kind of associations should I define on these models? Do I need to introduce any new models, and if so, what associations should I define on those? -->


student model:

class Students <ActiveRecord::Base
  belongs_to :house
  validates! :house_id, uniqueness {scope: [:student_id]}
  has_many :courses
  has_many :comments
end

create new comment model:

class Comment < ActiveRecord::Base
  belongs_to :student
  belongs_to :commentable, polymorphic: true

end

create new professor model:

class Professor < ActiveRecord::Base
  belongs_to :commentable, polymorphic: true
  has_many :comments
end

course model:

class Course < ActiveRecord::Base
  belongs_to :student
  has_many :students
  belongs_to :commentable, polymorphic: true
  has_many :comments
end


<!-- 6. When creating a new Student in my students controller, assuming I have a `student_params` method and the house the student should belong to is stored in `@house`, what code should I write to link the student to the house? -->

def new
  @student = Student.new
end

def create
  @house = House.find(params[:id])
  @student = @house.student.create(student_params)
  redirect_to house_student_path(house, student)
end


<!-- 7. If I'm using Devise with a User model, what code should I write in a controller or view to find out whether a user is signed in? And how can I get a reference to the currently-signed-in user? -->

class CommentController < ApplicationController
  before_action :authenticate_user!

  ....
end



<!-- 8. If I'm using Devise with a User model, what code should I write in my students controller so that only signed-in users can access the `new` and `create` actions? -->

class StudentController < ApplicationController
  before_action :authenticate_user!, only [:new, :create]

  ....
end



<!-- 9. When I deploy a new version of my app to Heroku that contains a new migration, how can I run that migration on my production database? -->

heroku run rake db:schema:load


<!-- 10. If accessing my app on Heroku gives me an uninformative "something went wrong" error message, how can I find out what the real error is? -->


heroku logs
