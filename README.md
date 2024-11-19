# DatabaseSchemaOwnership

[![](https://badge.fury.io/rb/database_schema_ownership.svg)](https://badge.fury.io/rb/database_schema_ownership)
[![](https://github.com/djezzzl/database_schema_ownership/actions/workflows/tests.yml/badge.svg?branch=master)](https://github.com/djezzzl/database_schema_ownership/actions/workflows/tests.yml?query=event%3Aschedule)
[![](https://github.com/djezzzl/database_schema_ownership/actions/workflows/rubocop.yml/badge.svg?branch=master)](https://github.com/djezzzl/database_schema_ownership/actions/workflows/rubocop.yml?query=event%3Aschedule)

The project's main goal is to help you manage the ownership of the database schema in your Ruby application.
It has a one-line configuration and it will take care of the rest.
You can use the DSL to satisfy your custom needs for more complex scenarios.

Follow me and stay tuned for the updates:
- [LinkedIn](https://www.linkedin.com/in/evgeniydemin/)
- [Medium](https://evgeniydemin.medium.com/)
- [Twitter](https://twitter.com/EvgeniyDemin/)
- [GitHub](https://github.com/djezzzl)

## How it works?

GitHub Code Ownership provides an easy way to manage the code ownership per file.
So, let's say you have a `CODEOWNERS` file under `.github` folder in your repository.

```
See https://help.github.com/articles/about-codeowners/
# for more info about CODEOWNERS file

db/schema.rb @schema-owners-team
```

Unfortunately, you can't assign a team per table. You can only assign a team per file.
If your schema would be split into multiple files, you could assign a team per file.
This is where the gem comes into play. It creates such files for you that you can easily control with Github Codeowners.

Let's look with an example. You have the following schema:
```ruby
ActiveRecord::Schema[8.0].define(version: 2024_11_12_133340) do
  create_table "locations", force: :cascade do |t|
    t.integer "user_id", null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.index ["user_id"], name: "index_locations_on_user_id"
  end

  create_table "users", force: :cascade do |t|
    t.string "name"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  add_foreign_key "locations", "users"
end
```

The gem will create the following ownership files for you on every schema change:
- `db/database_schema_ownership/locations.rb`
- `db/database_schema_ownership/users.rb`

```ruby
# db/database_schema_ownership/locations.rb
create_table "locations", force: :cascade do |t|
  t.integer "user_id", null: false
  t.datetime "created_at", null: false
  t.datetime "updated_at", null: false
  t.index ["user_id"], name: "index_locations_on_user_id"
end
add_foreign_key "locations", "users"

# db/database_schema_ownership/users.rb
create_table "users", force: :cascade do |t|
  t.string "name"
  t.datetime "created_at", null: false
  t.datetime "updated_at", null: false
end
```

Thus, now you can easily assign the ownership to the teams:
```
db/database_schema_ownership/locations.rb @geo-team
db/database_schema_ownership/users.rb @users-team
```

You can check Rails 8 examples:
- [Ruby format](https://github.com/djezzzl/database_schema_ownership/tree/master/rails8-example/db/database_schema_ownership)
- [SQL format](https://github.com/djezzzl/database_schema_ownership/tree/master/rails8-sql-example/db/database_schema_ownership)

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'database_schema_ownership', group: :development, require: "database_schema_ownership/railtie"
```

That's it! The gem will automatically adjust the rake tasks for you. 
That means, every time you dump your schema, the gem will create the ownership files for you.

## Custom configuration

*Note:* In case you don't need automatically adjusted rake tasks, you can omit the require:

```ruby
gem 'database_schema_ownership', group: :development
```

And simply run the following command to create the database ownership files:
```ruby
# schema_path - "db/schema.rb" or "db/structure.sql" or any other path
# folder_path - "db/database_schema_ownership" or any other path to store the ownership files
DatabaseSchemaOwnership::Runner.new(schema_path, folder_path).run
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake test` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and the created tag, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/djezzzl/database_schema_ownership. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [code of conduct](https://github.com/djezzzl/database_schema_ownership/blob/master/CODE_OF_CONDUCT.md).

## License

Copyright (c) Evgeniy Demin. See [LICENSE.txt](https://github.com/djezzzl/database_schema_ownership/blob/master/LICENSE.txt) for further details.

## Code of Conduct

Everyone interacting in the DatabaseSchemaOwnership project's codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/djezzzl/database_schema_ownership/blob/master/CODE_OF_CONDUCT.md).
