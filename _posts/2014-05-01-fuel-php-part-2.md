---
layout: post

title: "[English] Fuel PHP - Part 2"
cover_image: cover.png

excerpt: "Improve your PHP development"

author:
  name: Rémi Delhaye
  twitter: rdlhio
  gplus: 110592770316565776182
  bio: Étudiant Ingénieur - Rails lover
  image: me.png
---

## Migrations and oil

When quickly creating projects and getting the bare bones together, the FuelPHP oil tool can handle some of the more repetitive tasks for you - allowing you to concentrate on other aspects of the project. This ranges from setting up the database tables (like already demonstrated),  to creating models and controllers. It can even be used to scaffold an administration system ready to be used to administer content with a full user authentication system. It'll provide the starting point although the results won't always be pretty, it will be functional.
This allows you to quickly test idea and iterate towards to the perfect code. We'll run through the individual features of oil to create the models, controllers and the database migrations. This will give more time to tweak the outputs to create a more polished project / journal.

One of the most useful features of oil is the ability to migrate the database structure. These can be used to ensure consistency between environments. When deploying code, it's possible to also run migrations on the database.
Migrations can be used to rename a table, add fields as well as removing field or even tables. Oil has the notion of magic migrations. These start with a keywords prefix, making it easy to see what they will be doing.

```
$ php oil generate migration create_users name:text
$ php oil g migration rename_table_users_to_people
$ php oil g migration add_profile_to_people profile:text
$ php oil g migration delete_profile_from_people profile:text
$ php oil g migration drop_people

```

Note: you need to be aware of the keywords when creating a migration. Keywords include `create_`, `rename_`, `add_`, `delete_`, `rename_`, and `drop_`.

Migations are stored in `fuel/app/migrations` and a list of the performed migrations are stored in `fuel/app/config/<environment>/migrations.php`.

Additionally, the migrations database table records the current state of the project.

Running migrations is as a simple as typing:

```
$ oil refine migrate


```

If you want to migrate up or down, run the following:

```
$ oil r migrate:up
$ oil r migrate:down

```

These commands can be worked into a deployment script, so migrations can automatically be run when new code is added to the different environments, particularly useful for avoiding errors on the production environment.
Before progressing to generating models and their database tables, let's drop the previously created posts table:

```
$ oil g migration drop_posts
$ oil r migrate

```

Note: you can simply type r and g to run refine and generate oil actions

## Models

Now that we have a brief understanding of migrations and oil, let's put oil to use. We'll create model along with their corresponding database table details.

```
$ php oil generate model entry name:string slug:string excerpt:text content:text published_at:int

```

This will create both the model and migration. Let's open up the newly created model `fuel/app/classes/model/entry.php`

```
<?php
class Model_Entry extends \Orm\Model
{
  protected static $_properties = array(
    'id',
    'name',
    'slug',
    'excerpt',
    'content',
    'published_at',
    'created_at',
    'updated_at',
  );

  protected static $_observers = array(
    'Orm\Observer_CreatedAt' => array(
      'events' => array('before_insert'),
      'mysql_timestamp' => false,
    ),
    'Orm\Observer_UpdatedAt' => array(
      'events' => array('before_save'),
      'mysql_timestamp' => false,
    ),
  );

  protected static $_table_name = 'entries'
}

```

You'll notice in the newly generated model that is is extending the ORM Model. For this to work we'll need to include the ORM in the autoloader. This is a good chance to introduce the FuelPHP autoloader configuration. Load the configuration file: `fuel/app/config/config.php`

Look for the 'Always load' section and uncomment the `always_load` array like so:

```
'always_load' => array(
  'packages' => array(
    'orm',
  )
),

```

When you install new packages, simply add them in the `always_load` array for them to be automatically loaded.

Now back to the entry model, you'll notice the $_properties array, this will make is easier to call variables from the model later. If you add a new database field, it'll need to be added to the properties array. Other items of note are the observers. These are actions that execute methods or functions at different times. The ones used in the model are before_insert and before_update. These are currently being used to create the timestamps when adding or update entries.

The other observers are listed in the documentation, one that will be useful for use is the `Observer_Slug`. This will handle turning the entry title to URL safe and avoids the need to rewrite pre-existing functionality. Let's add this observer to our entry model. Add the following to the observers array:

```
'Orm\Observer_Slug' => array(
  'events' => array( 'before_insert', 'before_update'),
  'source' => 'name',
  'property' => 'slug'm
),

```

Now when we save an entry, the slug will automatically be updated.

There are some other database tables to create, let's do this now and then we can create relations between their models to make our lives easier.

```
$ oil generate model category name:varchar slug:varchar
$ oil generate model categories_entries category_id:int entry_id:int

```

Like for the entries model, add the `Observer_Slug` to the new category model.
For the user table, we are going to use the auth package as it includes user authentication functionality. To do this we need to copy the default auth package configuration to our application config.

```
$ cp ~/Sites/journal/fuel/packages/auth/config/auth.php ~/Sites/journal/fuel/app/config/auth.php


```

At this stage we should change the salt value in the auth.php config file. Once this is done run the following to create the neccessary auth tables:

```
$ oil refine migrate --packages=auth

```

Let's run the migration tool to make sure the database is updated:

```
$ oil r migrate

```

Now that we have the database structure established, we can relate the models with each other. This is made possible by the ORM model, which natively supports the following relationships:

* `belongs_to` - This model has the primary key for the relation and it belongs to a single related object.
* `has_one` - It's primary key is stored in an other table, which belongs to this model. It only has a single relation.
* `has_many` - It's primary key is save in multiple results of another model. Each other model will need to belong to this one. It can have many relations.
* `many_to_many` - Has it's primary keys saved in a table in between that keeps pairs of primary keys from both tables. It can have and belong to many models.

For the journal, we'll use the `belongs_to`, `has_one`, and `many_to_many` relations. Making full use of the relationships only needs a quick addition to the already created models. Load up the entry model in your favourite text editor. The model can be found at: `fuel/app/classes/model/entry.php`

```
<?php

class Model_Entry extends \Orm\Model
{
  protected static $_properties = array(
    'id',
    'name',
    'slug',
    'excerpt',
    'content',
    'published_at',
    'created_at',
    'updated_at',
  );

  protected static $_observers = array(
    'Orm\Observer_CreatedAt' => array(
      'events' => array('before_insert'),
      'mysql_timestamp' => false,
    ),
    'Orm\Observer_UpdatedAt' => array(
      'events' => array('before_update'),
      'mysql_timestamp' => false,
    ),
        'Orm\Observer_Slug' => array(
            'events' => array('before_insert'),
            'source' => 'name',
            'property' => 'slug',
        ),
  );
  protected static $_table_name = 'entries';
}

```

Add the following to the model just before the closing curly brace:

```
 protected static $_belongs_to = array(
            'category' => array(
                'key_from' => 'id',
                'model_to' => 'Model_Category_Entry',
                'key_to' => 'entry_id',
                'cascade_save' => false,
                'cascade_delete' => false,
            ),
        );

    protected static $_has_many = array(
        'categories' => array(
            'key_from' => 'id',
            'model_to' => 'Model_Category_Entry',
            'key_to' => 'entry_id',
            'cascade_save' => false,
            'cascade_delete' => false,
        )
    );

```

With these additions, we are configuring the ORM to treat the relationship between entries and categories as a many to many one. Instead of directly linking categories to entries, we are using the category_entry link table. This will allow us to assign multiple categories to entries and for the categories to be reused.

For example is we retrieve an entry, the list of categories would be obtained like:

```
$entry = Model_Entry::find(1)
$categories =$entry->categories;

```

In addition to the many to many relationship let's link the users to the entries that they authored.

Firstly, we need to add the `user_id` to the entries table:

```
$ oil g migration add_user_id_to_entry user_id:int
$ oil r migrate

```

We need to add the user_id to the entry model `$_properties`:

```
protected static $_properties = array(
    'id',
    'name',
    'slug',
    'excerpt',
    'content',
    'published_at',
    'created_at',
    'updated_at',
  );

```

Since the relationship maps from the user_id to the primary id key in the user table, we can just define the user as part of the `$_belongs_to` array. Everything else will fall into place once the reciprocal relation is added to the `Model_User`:

```
$_belongs_to = array(
  'user',
  'category' => ...
);

```

Talking of the `Model_User`, it needs creating:

```
$ oil g model user username:string password:string group:string email:string last_login:int profile_fields:text --no-migration

```

Although most of the user functionality is done from within the auth package, adding the user model is necessary for the `Model_Entry` relation with the user data.

Load the user model: `fuel/app/classes/model/user.php` and add the following array:

```
protected static $_has_many = array( 'entry' );

```

The category and category_entry models also need the relations creating:

`fuel/app/class/mode/category.php`

```
<?php

class Model_Category extends \Orm\Model
{
  protected static $_properties = array(
    'id',
    'name',
    'slug',
    'created_at',
    'updated_at',
  );

  protected static $_observers = array(
    'Orm\Observer_CreatedAt' => array(
      'events' => array('before_insert'),
      'mysql_timestamp' => false,
    ),
    'Orm\Observer_UpdatedAt' => array(
      'events' => array('before_update'),
      'mysql_timestamp' => false,
    ),
  );
  protected static $_table_name = 'categories';

    protected static $_belongs_to = array(
            'entry' => array(
                'key_from' => 'id',
                'model_to' => 'Model_Category_Entry',
                'key_to' => 'category_id',
                'cascade_save' => false,
                'cascade_delete' => false,
            ),
        );

    protected static $_has_many = array(
        'entries' => array(
            'key_from' => 'id',
            'model_to' => 'Model_Category_Entry',
            'key_to' => 'category_id',
            'cascade_save' => false,
            'cascade_delete' => false,
        )
    );
}

```

`fuel/app/classes/model/category/entry.php`

```
<?php

class Model_Category_Entry extends \Orm\Model
{
  protected static $_properties = array(
    'id',
    'category_id',
    'entry_id',
    'created_at',
    'updated_at',
  );

  protected static $_observers = array(
    'Orm\Observer_CreatedAt' => array(
      'events' => array('before_insert'),
      'mysql_timestamp' => false,
    ),
    'Orm\Observer_UpdatedAt' => array(
      'events' => array('before_update'),
      'mysql_timestamp' => false,
    ),
  );
  protected static $_table_name = 'category_entries';

    protected static $_belongs_to = array(
        'category',
        'entry',
    );
}

```

Now that we have the models and the migrations, it's a good idea to add them into source control. Once that's done, let's start creating the controller to make use of the models and then the views - including forms.
