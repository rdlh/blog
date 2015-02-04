---
layout: post

title: "[English] How to: Metaprogramming with Ruby"
cover_image: cover.png

excerpt: "Learn how to create classes dynamically"

author:
  name: Rémi Delhaye
  twitter: rdlhio
  gplus: 110592770316565776182
  bio: Étudiant Ingénieur - Rails lover
  image: me.png
---

> Metaprogramming is the act of writing code that operates on code rather than on data. This involves inspecting and modifying a program as it runs using constructs exposed by the language.

Almost every major language construct in Ruby, most notably *classes* and *methods*, can be **changed at runtime**. You can add *methods* to *classes*, remove them or redefine them. This is fairly unusual for a mainstream object oriented language.

In the example, we'll see how to create dynamically 3 classes: `Activity`, `Category` and `Location` with their own attributes (written in the `MODEL_ATTRIBUTES` as a Hash)

```ruby
module GeneratedModels
  MODEL_NAMES = %w[Activity Category Location]
  MODEL_ATTRIBUTES = {
    'Activity' => [:provider_id, :title, :category, :location, :rating],
    'Category' => [:provider_id, :name],
    'Location' => [:provider_id, :name, :country]
  }

  def self.generate_models
    # We each on the 3 model names
    GeneratedModels::MODEL_NAMES.each do |klass_name|

      # Get the good attributes for the current creating model
      klass_vars  = GeneratedModels::MODEL_ATTRIBUTES[klass_name.to_s]
      klass       = generate_class klass_vars

      # Define and set the class in the current Module (here, GeneratedModels)
      const_set klass_name, klass
    end
  end

  def self.generate_class(klass_vars)
    Class.new do
      klass_vars.each do |field|

        # Define attributes setters
        define_method field.intern do
          instance_variable_get("@#{field}")
        end

        # Define attributes getters
        define_method "#{field}=".intern do |arg|
          instance_variable_set("@#{field}", arg)
        end
      end

      # Define the initialize method with arguments
      define_method :initialize do |args|
        klass_vars.each do |field|
          instance_variable_set("@#{field}", args[field])
        end
      end
    end
  end
end

```

Let's try this! We'll call the static function `GeneratedModels.generate_models`

```ruby
GeneratedModels.generate_models

```

And now, let the magic happen!

```ruby
# Create a category..
category = GeneratedModels::Category.new({
  provider_id: 1,
  name: 'Museum'
})

# a location..
location = GeneratedModels::Location.new({
  provider_id: 1,
  name: 'Paris',
  country: 'France'
})

# and an activity!
acivity = GeneratedModels::Activity.new({
  provider_id: 1,
  name: 'Louvre',
  category: category,
  location: location,
  rating: 5
})

```
