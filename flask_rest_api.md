# Implementing a RESTful API in Flask
Flask can easily be used as a backend for your REST API, which allows other programmers (and users) to access data on your server in ways that you've defined.

Here is a simple explanation of how to setup a REST API with Flask. This tutorial assumes a basic knowledge of Python and Flask.

## The Basic Setup
1. Create and open a file named something like `app.py` in your project folder. Add the following lines.
  ```
  from flask import Flask, request
  from flask_restful import Resource, Api

  app = Flask(__name__)
  api = Api(app)
  
  if __name__ == '__main__':
    app.run(debug=True)
  ```
  This imports both `Flask` and the `flask_restful` modules you'll need and instantiates objects (`app` and `api`) for these that you will use to setup your API. The `if __name__ == '__main__':` portion runs when you execute `app.py` in Python.
2. Run `python app.py` in your terminal. You should see something like this:

  ```
  $ python api.py
   * Running on http://127.0.0.1:5000/
   * Restarting with reloader
  ```
3. Your server is now running. You can press `Ctrl-C` to stop the server (you'll need to do this when you make edits to `app.py`)

## Implimentation: `Get`
A `GET` request is essentially when a programmer 'asks' for information from a server. You query the API, and the API sends a response. This response can have useful information. Let's see how this looks in our `app.py`:

1. Add the following lines after we define `app` and `api`, but before the `if` statement:
  ```
  courses = {
    "CS125": "Intoduction to CS",
    "CS225": "Data Structures"
  }
  class CourseInfo(Resourse):
    def get(self, course_id):
      return {course_id: courses[course_id]}
      
  api.add_resource(CourseInfo, '/<string:course_id>')
  ```
2. To test this, simply go to `http://127.0.0.1:5000/CS125` in your browser (the port may be something other than `5000`, check your terminal). You should see the corresponding course name. Now try `/CS225`. 
3. Essentially what this does is create a dictionary that maps a courses 'ID' to it's name. Then, we create a `Resource` class called `CourseInfo` that will handle our `get` requests. In the `get` method, we have a `course_id` parameter, which the user will provide when they call the API. This `get` method simply returns the name of the course associated with the provided id. NOTE: Bad things will happen if/when a user tries to access a key we have not defined.
4. The `api.add_resource` call simply maps our `CourseInfo` object to the URL `/` with the arguments `<string:course_id>`: a string named `course_id`. You could just as easly make this map to `/courses/<string:course_id>`, to have a more descriptive endpoint.


## Implementation: `Put`
A `PUT` request is useful when we want to have the ability for our users to send us data that we will somehow keep. The implementation of our `put` method will not look radically different than `get`, but just know that in this case we are generally accepting data instead of sending it.

1. Add the following lines after our `get` method within the `CourseInfo` class:

  ```
  def put(self, course_id):
    courses[course_id] = request.form['data']
    return {course_id: courses[course_id]}
  ```
2. In a separate terminal window, run the following command:

  ```
  curl http://localhost:5000/CS173 -d "data=Discrete Structures" -X PUT
  ```
  You should get a confirmation response letting you know that your data has been accepted. Now try going to `http://localhost:5000/CS173` in your browser. You should see that your data has been saved in the server. Cool!
3. This `put` method again takes the `course_id` parameter. It uses that as a key to store the data that we recieve in the `request` object (which was something that we previously imported from `Flask`) in our courses dictionary. Then, we `return` the same response as before to acknowledge the data we've received.
4. Notice that you don't have to change anything with the `api` object because we've routed all requests to our `CourseInfo` object. That one call routes both `get` and `put` requests to that same object.
5. To reiterate, this type of request can be useful when we want to have the user *send* us data, which we may do some work with to store on the server.


## Full Code
I've created a gist with the [full code](https://gist.github.com/benjamincongdon/5044c344ab23dc6d956d5f7831579baa) of this tutorial.

## Next Steps
Where do we go from here? Well, this is just the first steps in writing a functional, useful API. A lot about writing a good API is not even the request handling itself, but rather the tools that you've built up around storing and managing user data that allows you to have more interesting functionality. You may have to query databases, or handle several different arguments when you receive a `get` request. Think about what types of tasks you're users will need to accomplish with your API and design the types of interactions your backend accepts accordingly.

### Further Reading
The [flask_restful documentation](https://flask-restful-cn.readthedocs.org/en/0.3.4/quickstart.html#endpoints) is a great resource for learning more about writing APIs with Flask.

## Attributions
This tutorial was modified form resources found on the [flask_restful](https://flask-restful-cn.readthedocs.org/en/0.3.4/quickstart.html#endpoints) documentation.
