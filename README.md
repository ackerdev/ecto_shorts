# EctoShorts

Ecto Shorts is a library focused around making Ecto easier to use in an
application and helping to write shorter code

## Installation

Documentation can be found at [https://hexdocs.pm/ecto_shorts](https://hexdocs.pm/ecto_shorts).

```elixir
def deps do
  [
    {:ecto_shorts, "~> 0.1.0"}
  ]
end
```


### Usage
There are 3 main modules to EctoShorts. SchemaHelpers, CommonFilters, CommonChanges and Actions

#### Actions
This module takes a schema and filter paramters and runs them through CommonFilters, esentially a wrapper
around Repo. For more info on filter options take a look at Common Filters

#### Common Changes
This module is responsible for determining put/cast assoc as well as creating and updating model relations

###### Extra Magic
If you pass a many to many relation only a list of id's it will count that as a `member_update` and remove or add members to the relations list

E.G. User many_to_many Fruit

This would update the user to have only fruits with id 1 and 3
```elixir
CommonChanges.put_or_cast_assoc(change(user, fruits: [%{id: 1}, %{id: 3}]), :fruits)
```

#### SchemaHelpers
This module contains helpers to check schema data

#### Common Filters
This module creates query from filter paramters like

```elixir
CommonFilters.convert_params_to_filter(User, %{id: 5})
```
is the same as
```elixir
from u in User, where: id == 5
```

This allows for filters to be constructed from data such as
```elixir
CommonFilters.convert_params_to_filter(User, %{
  favorite_food: "curry",
  age: %{gte: 18, lte: 50},
  name: %{ilike: "steven"},
  preload: [:address],
  last: 5
})
```
which the equivelent would be
```elixir
from u in User,
  preload: [:address],
  limit: 5,
  where: u.favorite_food == "curry" and
         u.age >= 18 and u.age <= 50 and
         ilike(u.name, "%steven%")
```

###### List of common filters
- `preload` - Preloads fields onto the query results
- `start_date` - Query for items inserted after this date
- `end_date` - Query for items inserted before this date
- `before` - Get items with ID's before this value
- `after` - Get items with ID's after this value
- `ids` - Get items with a list of ids
- `first` - Gets the first n items
- `last` - Gets the last n items
- `search` - ***Warning:*** This requires schemas using this to have a `&by_search(query, val)` function
