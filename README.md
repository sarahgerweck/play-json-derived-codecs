(Note: this project has been renamed from [play-json-variants](https://github.com/julienrf/play-json-variants/tree/v2.0) to `play-json-derived-codecs`)

# Play JSON Derived Codecs [![](https://index.scala-lang.org/julienrf/play-json-derived-codecs/play-json-derived-codecs/latest.svg)](https://index.scala-lang.org/julienrf/play-json-derived-codecs)

`Reads`, `OWrites` and `OFormat` derivation for algebraic data types (sealed traits and case classes, possibly recursive), powered by [shapeless](http://github.com/milessabin/shapeless).

Compared to the built-in macros, this project brings support for:

- sealed traits ;
- recursive types ;
- polymorphic types.

The artifacts are built for Scala 2.11, Play 2.5.x and Shapeless 2.3.0.

For Play 2.4.x compatibility see version [`3.2`](https://github.com/julienrf/play-json-derived-codecs/tree/v3.2).

## Usage

~~~ scala
import julienrf.json.derived

case class User(name: String, age: Int)

object User {
  implicit val reads: Reads[User] = derived.reads
}
~~~

The [API](https://www.javadoc.io/doc/org.julienrf/play-json-derived-codecs_2.11/3.3) is simple: the object
`julienrf.json.derived` has just three methods.

- `reads[A]`, derives a `Reads[A]` ;
- `owrites[A]`, derives a `OWrites[A]` ;
- `oformat[A]`, derives a `OFormat[A]`.

### Representation of Sum Types

By default, sum types (types extending a sealed trait) are represented by a JSON object containing
one field whose name is the name of the concrete type and whose value is the JSON object containing
the value of the given type.

For instance, consider the following data type:

~~~ scala
sealed trait Foo
case class Bar(s: String, i: Int) extends Foo
case object Baz extends Foo
~~~

The default JSON representation of `Bar("quux", 42)` is the following JSON object:

~~~ javascript
{
  "Bar": {
    "s": "quux",
    "i": 42
  }
}
~~~

### Custom Representation of Sum Types

The default representation of sum types may not fit all use cases. For instance, it is not very
practical for enumerations. For this reason, the way sum types are represented is extensible.

For instance, you might want to represent the `Bar("quux", 42)` value as the following JSON object:

~~~ javascript
{
  "type": "Bar",
  "s": "quux",
  "i": 42
}
~~~

Here, the type information is flattened with the `Bar` members.

You can do so by using the methods in the `derived.flat` object:

~~~ scala
implicit val fooOWrites: OWrites[Foo] =
  derived.flat.owrites((__ \ "type").write[String])
~~~

In case you need even more control, you can still implement your own `TypeTagOWrites` and `TypeTagReads`.

## Contributors

See [here](https://github.com/julienrf/play-json-variants/graphs/contributors).

## Changelog

See [here](https://github.com/julienrf/play-json-derived-codecs/releases).
