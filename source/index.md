# Ruby Object Mapper

ROM is an experimental Ruby ORM that aims to bring powerful object mapping
capabilities and give you back the full power of your database. It is based on
a couple of core concepts which makes it different from a typical ORM:

  * Quering a database is considered as a private implementation detail
  * Abstract query interfaces are evil and a source of unnecessary complexity
  * Reading and mutating data are 2 distinct concerns and should be treated separately
  * It must be **simple** to use the full power of your database

With that in mind ROM ships with adapters that allow you to connect to any
database and exposes a DSL to define **relations** and **mappers** to simplify
accessing the data.

## Synopsis

``` ruby
require 'rom-sql'

setup = ROM.setup(sqlite: "sqlite::memory")

setup.sqlite.connection.create_table :users do
  primary_key :id
  String :name
  Integer :age
end

setup.relation(:users) do
  def by_name(name)
    where(name: name)
  end

  def adults
    where { age >= 18 }
  end
end

setup.mappers do
  define(:users) do
    model(name: 'User')
  end
end

rom = setup.finalize

# accessing registered relations
users = rom.relations.users

users.insert(name: "Joe", age: 17)
users.insert(name: "Jane", age: 18)

puts users.by_name("Jane").adults.to_a.inspect
# => [{:id=>2, :name=>"Jane", :age=>18}]

# reading relations using defined mappers
puts rom.read(:users).by_name("Jane").adults.to_a.inspect
# => [#<User:0x007fdba161cc48 @id=2, @name="Jane", @age=18>]
```

## ROADMAP

ROM is on its way towards 1.0.0. Please refer to [issues](https://github.com/rom-rb/rom/issues)
for details.

## Community

* [![Gitter chat](https://badges.gitter.im/rom-rb/chat.png)](https://gitter.im/rom-rb/chat)
* [Ruby Object Mapper](https://groups.google.com/forum/#!forum/rom-rb) mailing list

## Credits

This project has a long history and wouldn't exist without following people:

 * [Dan Kubb](https://github.com/dkubb)
 * [Markus Schirp](https://github.com/mbj)
 * [Martin Gamsjaeger](https://github.com/snusnu)

## License

See `LICENSE` file.
