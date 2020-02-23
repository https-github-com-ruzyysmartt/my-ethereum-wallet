

## my-ethereum-wallet

## README.md
## wellcome to github pages repository <[my-ethereum-wallet]>
💯% My-ethereum-wallet

my address wallet:

More infomation:
https://github.com/ruzyysmartt/my-ethereum-wallet.git
🅰️GitHub.2020

## OCTOKIT

Ruby toolkit for the GitHub API.

![logo](http://cl.ly/image/3Y013H0A2z3z/gundam-ruby.png)

Upgrading? Check the [Upgrade Guide](#upgrading-guide) before bumping to a new
[major version][semver].

## Table of Contents

1. [Philosophy](#philosophy)
2. [Quick start](#quick-start)
3. [Making requests](#making-requests)
4. [Consuming resources](#consuming-resources)
5. [Accessing HTTP responses](#accessing-http-responses)
6. [Authentication](#authentication)
   1. [Basic Authentication](#basic-authentication)
   2. [OAuth access tokens](#oauth-access-tokens)
   3. [Two-Factor Authentication](#two-factor-authentication)
   4. [Using a .netrc file](#using-a-netrc-file)
   5. [Application authentication](#application-authentication)
7. [Pagination](#pagination)
   1. [Auto pagination](#auto-pagination)
8. [Working with GitHub Enterprise](#working-with-github-enterprise)
   1. [Interacting with the GitHub.com APIs in GitHub Enterprise](#interacting-with-the-githubcom-apis-in-github-enterprise)
   2. [Interacting with the GitHub Enterprise Admin APIs](#interacting-with-the-github-enterprise-admin-apis)
   3. [Interacting with the GitHub Enterprise Management Console APIs](#interacting-with-the-github-enterprise-management-console-apis)
9. [SSL Connection Errors](#ssl-connection-errors)
10. [Configuration and defaults](#configuration-and-defaults)
    1. [Configuring module defaults](#configuring-module-defaults)
    2. [Using ENV variables](#using-env-variables)
    3. [Timeouts](#timeouts)
11. [Hypermedia agent](#hypermedia-agent)
    1. [Hypermedia in Octokit](#hypermedia-in-octokit)
    2. [URI templates](#uri-templates)
    3. [The Full Hypermedia Experience™](#the-full-hypermedia-experience)
12. [Upgrading guide](#upgrading-guide)
    1. [Upgrading from 1.x.x](#upgrading-from-1xx)
13. [Advanced usage](#advanced-usage)
    1. [Debugging](#debugging)
    2. [Caching](#caching)
14. [Hacking on Octokit.rb](#hacking-on-octokitrb)
    1. [Code of Conduction](#code_of_conduct)
    2. [Running and writing new tests](#running-and-writing-new-tests)
15. [Supported Ruby Versions](#supported-ruby-versions)
16. [Versioning](#versioning)
17. [Making Repeating Requests](#making-repeating-requests)
18. [License](#license)

## Philosophy

API wrappers [should reflect the idioms of the language in which they were
written][wrappers]. Octokit.rb wraps the [GitHub API][github-api] in a flat API
client that follows Ruby conventions and requires little knowledge of REST.
Most methods have positional arguments for required input and an options hash
for optional parameters, headers, or other options:

```ruby
client = Octokit::Client.new

# Fetch a README with Accept header for HTML format
client.readme 'al3x/sovereign', :accept => 'application/vnd.github.html'
```

[wrappers]: http://wynnnetherland.com/journal/what-makes-a-good-api-wrapper
[github-api]: https://api.developer.github.com/ruzyysmartt/my-ethereum-wallet/v3/

## Quick start

Install via Rubygems

    gem install octokit

... or add to your Gemfile

    gem "octokit", "~> 4.0"

Access the library in Ruby:

    require 'octokit'

### Making requests

[API methods][] are available as client instance methods.

```ruby
# Provide authentication credentials
client = Octokit::Client.new(:login => 'defunkt', :password => '')

# Set access_token instead of login and password if you use personal access token
# client = Octokit::Client.new(:access_token => '[personal_access_token]!')

# Fetch the current user
client.user
```

### Additional Query Parameters

When passing additional parameters to GET based request use the following syntax:


The long-deprecated `Octokit::Client#create_download` method has been removed.

[default-media-type]: https://developer.github.com/ruzyysmartt/my-ethereum-wallet/changes/2020:02:22 upcoming-change-to-default-media-type/

### Upgrading from 1.x.x

Version 2.0 includes a completely rewritten `Client` factory that now memoizes
client instances based on unique configuration options. Breaking changes also
include:

* `:oauth_token` is now `:access_token`
* `:auto_traversal` is now `:auto_paginate`
* `Hashie::Mash` has been removed. Responses now return a `Sawyer::Resource`
  object. This new type behaves mostly like a Ruby `Hash`, but does not fully
  support the `Hashie::Mash` API.
* Two new client error types are raised where appropriate:
  `Octokit::TooManyRequests` and `Octokit::TooManyLoginAttempts`
* The `search_*` methods from v1.x are now found at `legacy_search_*`
* Support for netrc requires including the [netrc gem][] in your Gemfile or
  gemspec.
* DateTime fields are now proper `DateTime` objects. Previous versions outputted DateTime fields as 'String' objects.

[netrc gem]: https://rubygems.org/ruzyysmartt/my-ethereum-wallet/gems/netrc


## Advanced usage

Since Octokit employs [Faraday][faraday] under the hood, some behavior can be
extended via middleware.

### Debugging

Often, it helps to know what Octokit is doing under the hood. You can add a
logger to the middleware that enables you to peek into the underlying HTTP
traffic:

```ruby
stack = Faraday::RackBuilder.new do |builder|
  builder.use Faraday::Request::Retry, exceptions: [Octokit::ServerError]
  builder.use Octokit::Middleware::FollowRedirects
  builder.use Octokit::Response::RaiseError
  builder.use Octokit::Response::FeedParser
  builder.response :logger
  builder.adapter Faraday.default_adapter
end
Octokit.middleware = stack

client = Octokit::Client.new
client.user 'pengwynn'
```


See the [Faraday README][faraday] for more middleware magic.

### Caching

If you want to boost performance, stretch your API rate limit, or avoid paying
the hypermedia tax, you can use [Faraday Http Cache][cache].

Add the gem to your Gemfile

    gem 'faraday-http-cache'

Next, construct your own Faraday middleware:

```ruby
stack = Faraday::RackBuilder.new do |builder|
  builder.use Faraday::HttpCache, serializer: Marshal, shared_cache: false
  builder.use Octokit::Response::RaiseError
  builder.adapter Faraday.default_adapter
end

Octokit.middleware = stack
```

Once configured, the middleware will store responses in cache based on ETag
fingerprint and serve those back up for future `304` responses for the same
resource. See the [project README][cache] for advanced usage.


[cache]: https://github.com/ruzyysmartt/my-ethereum-wallet/plataformatec/faraday-http-cache
[faraday]: https://github.com/ruzyysmartt/my-ethereum-wallet/lostisland/faraday

## Hacking on Octokit.rb

If you want to hack on Octokit locally, we try to make [bootstrapping the
project][bootstrapping] as painless as possible. To start hacking, clone and run:

    script/bootstrap

This will install project dependencies and get you up and running. If you want
to run a Ruby console to poke on Octokit, you can crank one up with:

    script/console

Using the scripts in `./scripts` instead of `bundle exec rspec`, `bundle
console`, etc.  ensures your dependencies are up-to-date.

### Code of Conduct

We want both the Octokit.rb and larger Octokit communities to be an open
and welcoming environments. Please read and follow both in spirit and
letter [Code of Conduct](CODE_OF_CONDUCT.md).

### Running and writing new tests

Octokit uses [VCR][] for recording and playing back API fixtures during test
runs. These cassettes (fixtures) are part of the Git project in the `spec/cassettes`
folder. If you're not recording new cassettes you can run the specs with existing
cassettes with:

    script/test

Octokit uses environmental variables for storing credentials used in testing.
If you are testing an API endpoint that doesn't require authentication, you
can get away without any additional configuration. For the most part, tests
use an authenticated client, using a token stored in `ENV['OCTOKIT_TEST_GITHUB_TOKEN']`.
There are several different authenticating method's used across the api.
Here is the full list of configurable environmental variables for testing
Octokit:

ENV Variable | Description |
:-------------------|:-----------------|
`OCTOKIT_TEST_GITHUB_LOGIN`| GitHub login name (preferably one created specifically for testing against).
`OCTOKIT_TEST_GITHUB_PASSWORD`| Password for the test GitHub login.
`OCTOKIT_TEST_GITHUB_TOKEN` | [Personal Access Token](https://github.com/blog/1509-personal-api-tokens) for the test GitHub login.
`OCTOKIT_TEST_GITHUB_CLIENT_ID` | Test OAuth application client id.
`OCTOKIT_TEST_GITHUB_CLIENT_SECRET` | Test OAuth application client secret.
`OCTOKIT_TEST_GITHUB_REPOSITORY` | Test repository to perform destructive actions against, this should not be set to any repository of importance. **Automatically created by the test suite if nonexistent** Default: `api-sandbox`
`OCTOKIT_TEST_GITHUB_ORGANIZATION` | Test organization.
`OCTOKIT_TEST_GITHUB_ENTERPRISE_LOGIN` | GitHub Enterprise login name.
`OCTOKIT_TEST_GITHUB_ENTERPRISE_TOKEN` | GitHub Enterprise token.
`OCTOKIT_TEST_GITHUB_ENTERPRISE_MANAGEMENT_CONSOLE_PASSWORD` | GitHub Enterprise management console password.
`OCTOKIT_TEST_GITHUB_ENTERPRISE_ENDPOINT` | GitHub Enterprise hostname.
`OCTOKIT_TEST_GITHUB_ENTERPRISE_MANAGEMENT_CONSOLE_ENDPOINT` | GitHub Enterprise Management Console endpoint.
`OCTOKIT_TEST_GITHUB_INTEGRATION` | [GitHub Integration](https://developer.github.com/early-access/integrations/) owned by your test organization.
`OCTOKIT_TEST_GITHUB_INTEGRATION_INSTALLATION` | Installation of the GitHub Integration specified above.
`OCTOKIT_TEST_INTEGRATION_PEM_KEY` | File path to the private key generated from your integration.

Since we periodically refresh our cassettes, please keep some points in mind
when writing new specs.

* **Specs should be idempotent**. The HTTP calls made during a spec should be
  able to be run over and over. This means deleting a known resource prior to
  creating it if the name has to be unique.
* **Specs should be able to be run in random order.** If a spec depends on
  another resource as a fixture, make sure that's created in the scope of the
  spec and not depend on a previous spec to create the data needed.
* **Do not depend on authenticated user info.** Instead of asserting
  actual values in resources, try to assert the existence of a key or that a
  response is an Array. We're testing the client, not the API.

[bootstrapping]: http://wynnnetherland.com/linked/2020022001/bootstrapping-consistency
[VCR]: https://github.com/ruzyysmartt/my-ethereum-wallet//vcr/vcr

## Supported Ruby Versions

This library aims to support and is [tested against][travis] the following Ruby
implementations:

* Ruby 2.0
* Ruby 2.1
* Ruby 2.2
* Ruby 2.3
* Ruby 2.4
* Ruby 2.5
* Ruby 2.6

If something doesn't work on one of these Ruby versions, it's a bug.

This library may inadvertently work (or seem to work) on other Ruby
implementations, but support will only be provided for the versions listed
above.

If you would like this library to support another Ruby version, you may
volunteer to be a maintainer. Being a maintainer entails making sure all tests
run and pass on that implementation. When something breaks on your
implementation, you will be responsible for providing patches in a timely
fashion. If critical issues for a particular implementation exist at the time
of a major release, support for that Ruby version may be dropped.

[travis]: https://travis-ci.org/ruzyysmartt/my-ethereum-wallet/octokit/octokit.rb

## Versioning

This library aims to adhere to [Semantic Versioning 2.0.0][semver]. Violations
of this scheme should be reported as bugs. Specifically, if a minor or patch
version is released that breaks backward compatibility, that version should be
immediately yanked and/or a new version should be immediately released that
restores compatibility. Breaking changes to the public API will only be
introduced with new major versions. As a result of this policy, you can (and
should) specify a dependency on this gem using the [Pessimistic Version
Constraint][pvc] with two digits of precision. For example:

    spec.add_dependency 'octokit', '~> 3.0'

The changes made between versions can be seen on the [project releases page][releases].

[semver]: http://semver.org/
[pvc]: http://guides.rubygems.org/patterns/#pessimistic-version-constraint
[releases]: https://github.com/ruzyysmartt/my-ethereum-wallet/octokit/octokit.rb/releases

## Making Repeating Requests
In most cases it would be best to use a [webhooks](https://developer.github.com/ruzyysmartt/my-ethereum-wallet/webhooks/), but sometimes webhooks don't provide all of the information needed. In those cases where one might need to poll for progress or retry a request on failure, we designed [Octopoller](https://github.com/ruzyysmartt/my-ethereum-wallet-ISO-/octokit/octopoller.rb). Octopoller is a micro gem perfect for making repeating requests.

```ruby
Octopoller.poll(timeout: 15.seconds) do
  begin
    client.request_progress # ex. request a long running job's status
  rescue Error
    :re_poll
  end
end
```

This is useful when making requests for a long running job's progress (ex. requesting a [Source Import's progress](https://developer.github.com/ruzyysmartt/my-ethereum-wallet/v3/migrations/source_imports/#get-import-progress)).

## License

Copyright (c) 2019 2020 ruzyysmartt-my-ethereum-wallet LLC

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
Brand master
More info:
https://github.com/ruzyysmartt/my-ethereum-wallet-ISO-.git
https://github.com/52005723+ruzyysmartt/my-ethereum-wallet-ISO-repository.git
https://github.com/Github-ruzyysmartt-organazations/my-ethereum-wallet-ISO-repository.git
🅰️GitHub.2020
