---
layout: post

title: "[English] Fuel PHP - Part 3"
cover_image: cover.png

excerpt: "Improve your PHP development"

author:
  name: Rémi Delhaye
  twitter: rdlhio
  gplus: 110592770316565776182
  bio: Étudiant Ingénieur - Rails lover
  image: me.png
---

We have run through the basics of creating a project in FuelPHP. We touched upon how useful packages can be, but they can't be matched directly to a URL. In this chapter we will introduce modules, which unlike packages can be accessed directly from a URL. We will also run through some of the more advanced topics relating to FuelPHP.

Topics covered:
* Modules - what they are and how to use them
* Tasks
* Routing
* Unit testing
* Profiling

## What are modules and how to use them

We touched upon in the previous chapter that packages don't relate directly to a URL, they need a controllers and views in the application to do this. Modules on the other hand are a group of MVC elements that can act independently from the project application. They allow for encapsulation and reusability of your code, so you can share a module between projects without needing to write application code to fully utilize the functionality.

Modules are expected to reside within the modules directly within the application. If you would prefer to store them elsewhere, this can be changed in the application configuration. It's recommended to use modules on larger projects that consist of a large code base, as they can help keep the code in order.

Modules can be used independently and don't need access to the global application code. A route can be created to allow for a module to be directly accessed via a URL. Since they can include views and controllers, they can be thought of as mini applications in their own rights.

By default, modules are expected to reside in the modules folder within the application, for example `~/Sites/journal/fuel/app/modules`. This can be changed in your application config.php file - it appears in the section named module_paths. If you need to change the path to the modules, you will need to ensure that the end of the path includes the DS global variable. For example:

```
'module_paths' => array(
    APPPATH.'journal_modules'.DS, // path to application modules
    APPPATH.'..'.DS.'globalmods'.DS, // path to our global modules
),
```

## Namespaces

Historically, naming of classes and functions is a difficult one. We often prefer class names to have meaning and indicate what the classes does, this can lead to conflicts between our and third-party code. Since PHP5.3 we have been able to optionally use namespaces to avoid these conflicts. In FuelPHP all modules are required to have their own namespaces to avoid these conflicts. The namespaces must be named identically to the directory name for the module. For example a module in the Examplemodule directory will have a controller like:

```
<?php
/**
 * Module controller in the Examplemodule module
 */
 namespace Examplemodule;

  class Controller_Example
  {

   }
```

In addition to the namespace, controller names, the module folder name also influences the name in the URL if you want to route to the controllers in the module. So choosing the right name is an important one, but even without the perfect name, we can still change the URL routes from within the main application.
Module directory structure

Speaking of the directory name of the module, we should treat the module as a self contained application, complete will an expected directory structure. The following is the expected directory structure for any module:

```
/classes
  /controller
  /model
  /view
/config
/lang
/tasks
/views
```

As you will notice, the directory structure is very similar to the full application directory structure.
Using the module from the main application

Sometimes we may want to use some of the functions in the modules from the main application. In these cases we need to auto load the class before we can reference the module in the code. This can be done in a couple of ways. The first is to auto load the module in the application config.php, like we did with the example packages.

In the `always_load` array in the `config.php` file, you will notice a section for modules:

```
'always_load' => array(
    'modules' => array('examplemodule'),
```

An alternative way of loading the module is to do it only when we need to in the classes that require the module functionality.

This can be done like:

```
Module::load('examplemodule');
```

Once we have loaded the module, you can all the functions from the module in the following way:

```
\Examplemodule\Exampleclass::examplemethod( 'params' );
```

As modules tend to be self-contained applications, each has a specific use and they are not as widely open-sourced as packages are.

## Tasks

Sometimes we will want background processes, periodic tasks, or maintenance tasks to occur. This is where FuelPHP tasks come in handy. They can be run via the command line or set up as a cron job and can call upon modules and other classes just like controllers can.

Tasks should be placed in the `fuel/app/tasks directory` and by default only a `run()` method needs to be defined within the class. If you need other methods, these can be added in the usual way for PHP classes.

The tasks are called using the oil refine command. FuelPHP comes with an example task called robots, and will be included in the fuel/app/tasks folder.

To call the main method of the robot task you can run the following:

`$ php oil refine robot`

The run method in the robot task has a variable defined, allowing you to change pass a string via the oil refine command. Typing the following will change the message from the task:

`$ php oil refine robots "Kill all mice"`

If you look at the robots.php file, you will notice that a second method called `*protect()*` - this will be called using the following code:

`$ php oil r robots:protect`

Tasks in FuelPHP come in very useful for periodic actions and are very similar to controllers – making them straightfoward to write. They also have the advantage of having the same access to the core FuelPHP functionality.

## Routing

As with other frameworks, FuelPHP has fairly extensive routing capabilities. In this section we will run through the basics.

Firstly, there are a couple of reserved routes which are `_root_` and `_404_`. The root is used when there is no URL specified, for example the homepage or root page. The second is for when the requested content controller or view can't be found.

The routes exist in the config directory of the application in a file called routes.php. Let's load the `routes.php` file from `~/Sites/journal/fuel/app/config/routes.php`

```
<?php
return array(
  '_root_'  => 'welcome/index',  // The default route
  '_404_'   => 'welcome/404',    // The main 404 route
);
```

As you can see from the routes configuration file, the routes are stored as an array. The key on the left is matched to the URL and then the items on the right are then executed by FuelPHP. This is fairly straight forward, but can lend itself to complex URL matching with keyword matching.

The simplest routes are ones that match a URL string directly to a controller and action. These can look like:

```
return array(
  'about'  => 'welcome/about',  // The action method in the welcome controller
  'contact'   => 'about/contact',    // The contact method in the about controller
);
```

Our applications would tend to be fairly dynamic in nature, so specifying all the possible routes in the application would be a tedious job.

This is where more advanced routing comes in handy. For this we can use keywords and basic regular expressions to match strings in the URL and translate them to the controller method. These expressions make use of keywords preceded by a colon:

* `:any` - this matches anything from that point on.
* `:segment` - this matches a single segment in the URL. The segment can be anything. This can be useful for language strings in the URL.
* `:num` - matches numeric values in the URL
* `:alpha` - matches any alpha characters
* `:alnum` - matches any alpha numeric characters

`'journal/(:any)' => 'journal/entry/$1'`,

This will match any journal entry and send the entry name to the entry method in the journal controller.

`'(:segment)/contact' => 'site/contact/$1'`,

This will allow any preceding segment, so can be used for URLs like `/en/contact` and will send the language flag as a variable to the contact method in the site controller. The final URL would be something like `/site/contact/en`.

As developers we often try to make our code more readable by choosing clear and descriptive variable names. The same can be done with more advanced routing in FuelPHP, as it allows for you to use named parameters in the routes. These named segments can then be accessed from within your methods or actions. For example:

```
return array(
  'journal/:year/:month/:day/:id' => 'journal/entry',
);
```

In this route the `/journal/2013/08/10/name` would be routed to the entry method within the journal controller. In the entry method, we can get the named segments like this:

```
$this->param('year');
$this->param('month');
$this->param('day');
$this->param('id');
```

FuelPHP uses regex for the named segments to work within the route. Each segment counts as a back reference, for example the $1 and $2 we often use when making use of regular expressions in PHP. Back references are a regex term and more information can be seen at: http://www.regular-expressions.info/brackets.html#usebackrefinregex
In a route of `:name/(\d{2}`, the digit would be found using the variable $2 and the $1 would return the value of the :name segment .

We mentioned the RESTful controller template in the previous chapters. These can be used in conjunction with verb based routing to direct requests to the correct methods in the RESTful controller. This allows a route to a certain URL to be routed through to different methods and controllers to fit with the functionality.

#### For example:
* A POST request to /journal could be routed to the create method in the journal controller.
* A GET request to  /journal could be routed to the index method in the journal controller.
These requests all follow the recommended use of the HTTP verbs to perform actions in the application and the route in the routes.php would look something like the following:

```
return array(
'journal' => array(
    array('GET', new Route('journal/index')),
    array('POST', new Route('journal/create')),
)
);
```

We could do something similar for the PUT and DELETE verbs and we can use regex and named parameters to make it easier to get the relevant information from the URL.
If you are working with user profile information and data we would look to use https or secure connections. Again routing in FuelPHP supports this. The following example would only load the route if the request is via https rather than just http:

```
return array(
  'user/(:any)' => array( array( 'GET', new Route( 'user/view/$1' ), true ) ),
);
```

The third parameter ensures that the named route is only used when https is used.
During development, we often rearrange the structure of the application to reflect the changing functionality. One feature of routes in FuelPHP, called named routes and reverse routing, makes this process easier. Instead needing to edit all of our views, we can simply change the named route in the main routes.php file. For this to work, we need to use the name of the route in our views. In the following example we change the `'admin/app/dashboard'` to `'admin/dashboard'`:

```
return array(
    'admin/app/dashboard' => array('admin/dashboard', 'name' => 'admin_dashboard'),
);
```

In our views that need to link to the dashboard we would use the following anchor code:

`echo Html::anchor(Router::get('admin_dashboard'), 'Dashboard');`

Note: this only works for application code and will not work for module routes.

## Unit testing

No modern framework would be complete without the ability to test the application code and functionality. FuelPHP is built this in mind and includes tests and test cases based on the PHPUnit testing framework.
So what is unit testing?

Unit testing are automated tests written to check that units of functionality (methods and functions) are working as expected. The tests typically test that for a given input that the output is correct, treating the functions as a black box to ensure that the internal logic works.

Since unit testing automated, it's easy to check that recent code changes don't break other functionality. It also allows for the use of a continuous integration server like Jenkins (http://jenkins-ci.org). A continuous integration server will automatically deploy your code for you once it passes the unit testing - allowing you to concentrate on the actual code.

## PHPUnit

There are several unit-testing tools available in PHP, but the de-facto standard is PHPUnit by Sebastian Bergmann. This is the testing framework supported by FuelPHP and is run using oil command line tool, but before using it you will need to ensure that PHPUnit is installed. For the most up-to-date installation instructions, I'd recommend browsing the official documentation at: http://www.phpunit.de/manual/current/en/installation.html

## Running unit tests

Unit tests are run using the FuelPHP oil command line tool and is a case of running something like the following:

```
$ php oil test
$ Tests Running...This may take a few moments.
$ PHPUnit 3.7.21 by Sebastian Bergmann.
$
$ Configuration read from /home/user/sites/journal/fuel/core/phpunit.xml

$ ..................................................  63 / 251 ( 25%)
$ .................................................. 126 / 251 ( 50%)
$ .................................................. 189 / 251 ( 75%)
$ ..................................................
$ Time: 6 seconds, Memory: 22.25Mb
$
$ OK (251 tests, 206 assertions)
```

## Creating Unit Tests

The tests are located in the application in the `fuel/app/tests` directory and will read any tests in subdirectories. The tests files should follow a similar structure to the classes they are testing. So if you were testing the category model ( `/fuel/app/classes/model/category.php`) it would have a test file located at `fuel/app/tests/model/category.php`. The test cases should extend the TestCase class, which is an extension of the PHPUnit_Framework_TestCase class. This means that you will be able to the usual PHPUnit assertions and methods in your tests.

The name of the class should be prefixed with Test_, so the category test should be named Test_Model_Category. The class should look something like:

```
<?php
class Test_Model_Category extends TestCase
{
  public function test_category()
  {
  }
}
```

The official documentation lists the many assertions and recommended ways of writing tests. The documentation can be found at http://www.phpunit.de/manual/current/en/writing-tests-for-phpunit.html


## Grouping unit tests

As your application grows, sometimes running all the unit tests can be time consuming; as such we can group tests together and then only test certain groups. This is done with the --group= command at the end of the usual test command. So running the User group of tests would be done with the following code:

`$ php oil test --group=User`

The grouping is done in a docbloc comment in each testing class, and each test case can be assigned to multiple groups. The following would assign the category model test to Blog and App groups:

```
<?php
/**
 * @group Blog
 * @group App
 */
 class Test_Model_Category extends TestCase
{
  public function test_category()
  {
  }
}
```

## Configuration and module testing

FuelPHP uses configuration in the phpunit.xml file that is included in the `fuel/core/` directory. To customize the configuration we need to copy this file to our application and then make any changes there. FuelPHP will load the application phpunit.xml in favor of the core version.

As mentioned earlier, modules are applications in their own right - including unit testing. The unit tests should be included in a test directory within the top level of each module. For FuelPHP to run the tests in modules, it needs to know that they exist. This is done by including the modules directory in the phpunit.xml. Once you have made a copy of the core FuelPHP phpunit.xml file, you can add the following:

```
<testsuite name="modules">
    <directory suffix=".php">../app/modules/*/tests</directory>
</testsuite>
```

Now that you know a little about unit testing in FuelPHP, let's cover another aspect of development - namely the ability to profile your application.

## Profiling

FuelPHP includes a profiler that is based on PHP Quick Profiler. This allows you to profile and debug your code all without needing to write extra functions within the application. The profiler can be switched on and off via the application’s config.php file. To enable the profiler simply change the 'profiling' variable to true, and set it to false to disable the profiler.

The profiler also includes a database-profiling tool, but due to the resources required it is disabled by default. The database profiler will need to be enabled on an environment basis, so the development environment can have it enabled without affecting the other environments. It can be enabled in the environment's db.php file using the value `'profiling' => true`,

The profiler has a tabbed interface and consists of the following tabs:

* `Console` - the default tab giving information about errors, log entries, memory usage along with execution timings.
* `Load` time - the request load time.
* `Database` - The execution time and number of database queries executed.
* `Memory` - The peak memory used by the page load.
* `File` - a list of the files loaded with their file names and their sizes.
* `Config` - the final configuration variables at the end of the page load.
* `Session` - the contents of the session at the end of the page load.
* `GET` - the $_GET array contents.
* `POST` - the $_POST array contents

The profiler provides a lot of information that can help when optimizing your application.
