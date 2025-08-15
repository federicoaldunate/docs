# **BlackLab — Rack Middleware WAF (Web Application Firewall)**

BlackLab is a lightweight, extensible web application firewall designed as Rack middleware — perfect for protecting Rails and other Rack-based web applications. It blocks common attack vectors like SQL injection, XSS, path traversal, and more using modular plugins.

## **Table of Contents**

- [<u>Motivation</u>](https://github.com/windmotion-io/black-lab#motivation)
- [<u>Installation</u>](https://github.com/windmotion-io/black-lab#installation)
- [<u>Usage</u>](https://github.com/windmotion-io/black-lab#usage)
- [<u>Examples</u>](https://github.com/windmotion-io/black-lab#examples)
- [<u>Contributions</u>](https://github.com/windmotion-io/black-lab#contributions)
- [<u>License</u>](https://github.com/windmotion-io/black-lab#license)
- [<u>Credits</u>](https://github.com/windmotion-io/black-lab#credits)

---

## **Motivation**

Imagine gaining partial unauthorized access to a web application, creating users without permission, probing with attack vectors, and being partially blocked by an existing WAF. But the existing WAF wasn’t tightly integrated with the Rails app, so my user might still be there, in their system. Why not just block the user, trigger an alarm, a Slack message or something?

I built **BlackLab** to bridge that gap: a Rack-compatible WAF that integrates seamlessly with Rails and any Rack app, blocking attacks early with configurable plugins. It’s lightweight, extensible, and easy to configure.

For the full story behind BlackLab’s creation, check out my [<u>blog post here</u>](https://github.com/windmotion-io/black-lab/blob/master/YOUR_BLOG_POST_LINK_HERE).

---

## **Installation**

Add this line to your application's Gemfile:

```ruby
gem "black_lab"
```

And then execute:

```
bundle install
```

## **Usage**

In your [config.ru](http://config.ru) or Rails middleware stack, simply add:

```ruby
require "black_lab"

use BlackLab::Middleware
```

Configure **BlackLab** via an initializer (e.g., config/initializers/black_lab.rb):

```ruby
BlackLab.configure do |config|
  config.block_message = "Blocked by BlackLab WAF"
  config.block_duration = 3600 # seconds to block an IP/user in cache
  config.block_callback = ->(request) { puts "Blocked #{request.ip}" } # { User.find_by(ip: request.ip).block_forever!)}
  config.callback_threshold = 15 # points threshold to trigger callback
  config.cache_store = Rails.cache # or any object responding to #write/#read
  config.plugins = [
    BlackLab::Plugins::SqliPlugin.new(weight: 3),
    BlackLab::Plugins::XssPlugin.new(weight: 3),
    BlackLab::Plugins::PathTraversalPlugin.new(weight: 3),
    #BlackLab::Plugins::LfiPlugin.new(weight: 1),
    #BlackLab::Plugins::RfiPlugin.new(weight: 2)
  ]
end
```

## **Common Patterns we are blocking now**

- SQL Injection Detection
- Cross-Site Scripting (XSS) Detection
- Path Traversal Detection
- Remote File Inclusion (RFI) Detection
- Local File Inclusion (LFI) Detection

## **Benchmarks**

BlackLab provides benchmarking scripts to measure the performance of individual plugins. You can run these benchmarks to ensure your WAF operates efficiently under load.

To benchmark a plugin, navigate to the bench directory and run the appropriate Ruby script. For example, to benchmark the SQL Injection plugin:

```
ruby benchmarks/sqli_plugin_benchmark.rb
```

## **Contributing**

Bug reports and pull requests are welcome on GitHub at [<u>https://github.com/windmotion-io/black_lab</u>](https://github.com/windmotion-io/black_lab). This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [<u>code of conduct</u>](https://github.com/windmotion-io/black_lab/blob/master/CODE_OF_CONDUCT.md).

## **License**

The gem is available as open source under the terms of the [<u>MIT License</u>](https://opensource.org/licenses/MIT).

## **Code of Conduct**

Everyone interacting in the BlackLab project's codebases, issue trackers, chat rooms and mailing lists is expected to follow the [<u>code of conduct</u>](https://github.com/windmotion-io/black_lab/blob/master/CODE_OF_CONDUCT.md).