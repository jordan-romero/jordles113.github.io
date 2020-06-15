---
layout: post
title:      "Associations: Getting Polymorphic With it. "
date:       2020-06-15 21:51:23 +0000
permalink:  associations_getting_polymorphic_with_it
---

For my Sinatra project, I told myself (again) that I would push myself to do something that not only met the requirements but that was difficult. I wanted ASSOCIATIONS and I set out to make some complex ones at that. I didn't realize I really set the bar high until I got to Office Hours. My project really needed to be polymorphic for it to work. I didn't even know what that meant!? 

I began to scour the interwebs. I thought about completely reworking my project as article after article confused me. I decided to just start from the beginning. What is an Association? My new coding best friend Google has lots of answers but one really stuck and it was simple to comprehend. Associations are just relationships through macros provided by ActiveRecord. 

# One-to-One

My first two associations were easy. One-to-One. In my project, a user has a Fridge object and a List object. Just one fridge and just one list. So as the owner of said fridge or list, the macro for User was "has_one:". 
```
class User < ActiveRecord::Base 
    has_one :fridge
    has_one :list
```

****This meant that my User would have just one instance of a list or a fridge.

Likewise, the Fridge and the User model needed a macro to relate to the User, the "belongs_to:".

```
class Fridge < ActiveRecord::Base
    belongs_to :user
```
```
class List < ActiveRecord::Base
    belongs_to :user
end 
```

**The "belongs_to" macros determine where the Foreign Key resides.** 

In this case, we have a foreign key in Fridge and List. 

# Polymorphic

Now here is where things got tricky. Both a Fridge and a List have items, but like your grocery list and your fridge at home these items are not always the same. I wanted a User to be able to update both objects with different items, but items are just items and I had to associate Items to both a Fridge and a List from the same Item model. This is a Polymorphic association. 

**A polymorphic relationship is such that a single model can belong to multiple models. **

In this project, items belong to both a Fridge and a List. 

Since an Item can belong to either a fridge or a list, we want to define Items as itemizable. 
```
class Item < ActiveRecord::Base 
    belongs_to :itemizable, polymorphic: true
end
```

This belongs_to gives our Item Model the tools to interface with mutliple other models through an itemizable_id and an itemizable_type, where the itemizable_id becomes our foreign key and the itemizable_type tells the item model where it belongs (fridge or list). 

Unlike the One-to-One, "belongs_to", this sets up a way for both the Fridge and the List to associate with items as "itemizables." 
```
class Fridge < ActiveRecord::Base
    has_many :items, as: :itemizable 
    belongs_to :user
end 
```
```
class List < ActiveRecord::Base
    has_many :items, as: :itemizable
    belongs_to :user
end 
```

# Has-Many-Through
Now, I still needed a way for a user to add items to their fridge and list respectively. So that is where a "has_many_through" relationship came in.

A "has_many_through" association reflects one model having many objects THROUGH another model. This works perfectly for this case as both a fridge and a list have items. 

```
class User < ActiveRecord::Base 
    has_one :fridge
    has_one :list
    has_many :fridge_items, through: :fridge, source: :items
    has_many :list_items, through: :list, source: :items 
end 
```

I have fridge_items and list_items through a fridge or a list respectively, coming from a source, the items model.


#Running a Polymorphic Migration: 

Now that the associations are built out, lets briefly take a look at the SQLite3 migrations to further undertand how the tables are related and how to set up the migrations properly. 

**The only table that deviates from standard is the polymorphic table.** 

Remember that itemizable_id and itemizable_type. We have to create them in our Item table. 

```
class CreateItems < ActiveRecord::Migration[6.0]
  def change
    create_table :items do |t|
      t.string :name 
      t.string :expy_date
      t.integer :itemizable_id
			t.string :itemizable_type
      t.timestamps 
    end
  end 
end

```

To simplify you can use references: 
```
class CreateItems < ActiveRecord::Migration[6.0]
  def change
    create_table :items do |t|
      t.string :name 
      t.string :expy_date
      t.references :itemizable, polymorphic: true, index: true 
      t.timestamps 
    end
  end 
end
```

To understand references, I looked to the ActiveRecord source code (linked here:[https://github.com/rails/rails/blob/master/activerecord/lib/active_record/connection_adapters/abstract/schema_definitions.rb#L422](http://) specificly: Line 365). 


For more resources on Associations: 

[https://dev.to/neshaz/everything-there-is-to-know-about-associations-in-rails-52ii](http://)
[https://launchschool.com/blog/understanding-polymorphic-associations-in-rails](http://)
