---
layout: post

title: "[English] Fuel PHP - Part 1"
cover_image: cover.png

excerpt: "Improve your PHP development"

author:
  name: Rémi Delhaye
  twitter: rdlhio
  gplus: 110592770316565776182
  bio: Étudiant Ingénieur - Rails lover
  image: me.png
---

> [FuelPHP official documentation](http://fuelphp.com/docs/)

With [WordPress](https://wordpress.com) becoming the operating system of the web (their words), running 18% of all sites. It now has a focus much larger than blogging or simple journals. Although a simple application or website, creating a simple blog will demonstrate a lot of the FuelPHP features. From database migrations, pivoting ideas and code to full temporal models to store revisions of journal entries.

Before starting to code let's first thin about what to build and what the minimum viable product will be.
In this chapter we will cover the following topics:

* Creating and running database migrations
* Using Oil to create models
* Using Oil to create controllers
* Installing and using the HTML5 boilerplate
* Putting it all together to create an administration system with scaffolding
* Using the Oil command line console.

## Getting started

Given the subject material of blogging, it would be silly not to mention WordPress.

Although it became popular for it's initial focus on blogging / thought leadership, it has matured into much more. The plugin architecture and large community has morphed it into more a general purpose content management system. Like WordPress, we're going to start small and demonstrate some of the tools to make your life easier developing with FuelPHP.

Before creating your project, first create a source control repository. In this example, we'll use [GitHub](https://github.com), but alternatives like [bitbucket](https://bitbucket.org) or [Beanstalk](http://beanstalkapp.com) may be more suitable for you.

Login to your account and then create a new project / repository. In this example we'll call our project journal. Make sure to record the repository URL - you will need it shortly.

#### Now let's create the project on our development machine

```
$ cd ~/Sites
$ oil create journal

```

We'll need a data store for the journal entries, so let's configure it now. Load the db.php configuration found at `fuel/app/config/development/db.php`

As mentioned before, FuelPHP has the notion of environments, so we can add the development database configuration to source control and it won't affect the other environments.

Your `db.php` should look something like:

```php
<?php
return array(
  'default' => array(
    'connection' => array(
      'dsn' => 'mysql:host=localhost;dbname=journal_dev',
      'username' => 'journal_dev',
      'password' => 'journal_dev',
    ),
    'profiling' => true,
  ),
);

```

Although, not recommended on the live environment, enabling profiling can help whilst developing the project. The example configuration has it enabled.

We have the code and the repository, now let's link them:

```
$ cd ~/Sites/journal
$ git remote rm origin
$ git remote add origin git@github.com:your_username/journal.git
$ git pull origin
$ git add ./
$ git commit -m 'Initial commit'
$ git push origin master

```

Now is also a good time to setup a few branches for the different environments: develop, staging, and production:

```
$ git checkout -b develop
$ git push origin develop
$ git checkout -b staging
$ git push origin staging
$ git checkout -b production
$ git push origin production
$ git checkout develop

```

Continuing on the setup theme, let's set the database tables
Database table creation
Before creating the tables in the database, let's first detail what data will be stored, and the initial fields in the tables.

For the blogging, we will have blog posts or entries and these will have a published date, and be categorised to enable grouping of similar themes. Each post will be associated to an author (user) and have content and an excerpt. We can add free-form tagging later.

#### Entries

The following are the fields in this table:

* `id` - this will be the primary indentifier
* `name` (varchar)
* `slug` (varchar)
* `excerpt` (text)
* `content` (text)
* `published_at` (int)
* `created_at` - this will be a timestamp of when the record was created.
* `updated_at` - this will be a timestamp of when the record was last updated.

#### Categories

The following are the fields in this table:
* `id` - this will be the primary indentifier
* `name` (varchar)
* `slug` (varchar)
* `created_at` - this will be a timestamp of when the record was created.
* `updated_at` - this will be a timestamp of when the record was last updated.

#### Users

The following are the fields in this table:
* `id` - this will be the primary indentifier
* `name` (varchar)
* `username` (varchar)
* `password` (varchar)
* `email` (varchar)
* `last_login` (int)
* `login_hash` (text)
* `profile_fields` (text)
* `created_at` - this will be a timestamp of when the record was created.
* `updated_at` - this will be a timestamp of when the record was last updated.

We also need a link table so that multiple entries can be categorised in the same categories.

#### Categories_entries

The following are the fields in this table:

* `id` - this will be the primary indentifier
* `category_id` - this will be the primary indentifier of the category
* `entry_id` - this will be the primary indentifier of the entry
* `created_at` - this will be a timestamp of when the record was created.
* `updated_at` - this will be a timestamp of when the record was last updated.

You may have noted that each table has an `id`, `created_at`, and `updated_at` fields. These are automatically added by the FuelPHP oil tool and can prove useful when linking or forming relationships between data objects. They do add a minimal overhead, but this should not be of concern at this stage, as the extra storage required are minimal.

Let's create some migrations using the FuelPHP oil tool:

```
$ php oil generate migration create_entries name:string slug:string excerpt:text content:text published_at:int

```

This will create the migrations to assemble the posts database table. There is no need to detail the `id`, `created_at`, or `updated_at` fields as these will be automatically generated. If you don't need these extra time based fields, you can append `--no-timestamp` at the end of the generate command.

