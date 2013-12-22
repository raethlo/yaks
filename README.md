[![Gem Version](https://badge.fury.io/rb/yaks.png)][gem]
[![Build Status](https://secure.travis-ci.org/plexus/yaks.png?branch=master)][travis]
[![Dependency Status](https://gemnasium.com/plexus/yaks.png)][gemnasium]
[![Code Climate](https://codeclimate.com/github/plexus/yaks.png)][codeclimate]
[![Coverage Status](https://coveralls.io/repos/plexus/yaks/badge.png?branch=master)][coveralls]

[gem]: https://rubygems.org/gems/yaks
[travis]: https://travis-ci.org/plexus/yaks
[gemnasium]: https://gemnasium.com/plexus/yaks
[codeclimate]: https://codeclimate.com/github/plexus/yaks
[coveralls]: https://coveralls.io/r/plexus/yaks

# Yaks

### One Stop Hypermedia Shopping ###

Yaks is a tool for turning your domain models into Hypermedia resources.

There are at the moment a number of competing media types for building Hypermedia APIs. These all add a layer of semantics on top of a low level serialization format such as JSON or XML. Even though they each have their own design goals, the core features mostly overlap. They typically provide a way to represent resources (entities), and resource collections, consisting of

* Data in key-value format, possibly with composite values
* Embedded resources
* Links to related resources
* Outbound links that have a specific relation to the resource

They might also contain extra control data to specify possible future interactions, not unlike HTML forms.

These different media types for Hypermedia clients and servers base themselves on the same set of internet standards, such as [RFC4288 Media types](http://tools.ietf.org/html/rfc4288), [RFC5988 Web Linking](http://tools.ietf.org/html/rfc5988), [RFC6906 The "profile" link relation](http://tools.ietf.org/search/rfc6906) and [RFC6570 URI Templates](http://tools.ietf.org/html/rfc6570).

## Yaks Resources

At the core of Yaks is the concept of a Resource, consisting of key-value attributes, RFC5988 style links, and embedded sub-resources.

To build an API you create 'mappers' that turn your domain models into resources. Then you pick a media type 'serializer', which can turn the resource into a hypermedia message.

The web linking standard which defines link relations like 'self', 'next' or 'alternate' is embraced as far as practically possible, for instance to find the URI that uniquely defines a resource, we look at the 'self' link. To distinguish different types of resources we use the 'profile' link.

## Mappers

To turn your domain models into resources, you define mappers, for example :

```ruby
class PostMapper < BaseMapper
  link :self, '/api/posts/{id}'

  attributes :id, :title

  has_one :author
  has_many :comments
end
```

Now you can use this to create a Resource

```ruby
resource = PostMapper.new(post).to_resource
```

## Serializers

A resource can be turned in to a specific media type representation, for example [HAL](http://stateless.co/hal_specification.html) using a Serializer

```ruby
hal = Yaks::HalSerializer.new(resource).serialize
puts JSON.dump(hal)
```

This will give you back a composite types consisting of primitives that have a mapping to JSON, so you can use your favorite JSON encoder to turn this into a character stream.

## Acknowledgment

The mapper syntax is largely borrowed from ActiveModel::Serializers, which in turn closely mimics the syntax of ActiveRecord models. It's a great concise syntax that still offers plenty of flexibility, so to not reinvent the wheel we stick to the existing syntax as far as practical, although there are several extensions and deviations.

## Non-Features

* No core extensions
* Minimal dependencies
* Only serializes what explicitly has a Serializer, will never call to_json/as_json
* Adding extra output formats does not require altering existing code
* Has no opinion on what to use for final JSON encoding (json, multi_json, yajl, oj, etc.)


## License

MIT
