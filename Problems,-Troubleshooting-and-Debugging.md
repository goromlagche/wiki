# Help!

Read below for tips.  If you still need help, you can:

* Ask your question in [The Karafka official Slack channel](https://slack.karafka.io)
* [Open a GitHub issue](https://github.com/karafka/karafka/issues/new).  (Don't be afraid to open an issue, even if it's not a Karafka bug.  An issue is just a conversation, not an accusation!)
* Check our [FAQ](/docs/FAQ) and the [Pro FAQ](/docs/Pro-FAQ)

You **should not** email any Karafka committer privately.

Please respect our time and efforts by sticking to one of the options above.

Please consider buying the Pro subscription for additional priority Pro support and extra features.

## OSS Support Policy

Karafka's Open Source Software (OSS) support primarily revolves around assisting users with issues related to the Karafka and librdkafka ecosystems. This involves troubleshooting and providing solutions for problems originating from Karafka or its related subcomponents.

However, it is crucial to understand that the OSS support does **not** extend to application-specific issues that do not originate from Karafka or its related parts. This includes but is not limited to:

1. Incorrect application configurations unrelated to Karafka.
2. Conflicts with other libraries or frameworks within your application.
3. Deployment issues on specific infrastructure or platforms.
4. Application-specific runtime errors.
5. Problems caused by third-party plugins or extensions.
6. Data issues within your application.
7. Issues related to application performance optimization.
8. Integration problems with other services or databases.
9. Design and architecture questions about your specific application.
10. Language-specific issues are unrelated to Karafka or librdkafka.

We acknowledge that understanding your specific applications and their configuration is essential, but due to the time and resource demands, this goes beyond the scope of our OSS support.

For users seeking assistance with application-specific issues, we offer a Pro version of Karafka. This subscription provides comprehensive support, including help with application-specific problems.

For more information about our Pro offering, please visit [this](https://karafka.io/#become-pro) page.

## Reporting problems

When you encounter issues with Karafka, there are several things you can do:

- Feel free to open a [Github issue](https://github.com/karafka/karafka/issues)
- Feel free to ask on our [Slack channel](https://slack.karafka.io)
- Use our [integration specs](https://github.com/karafka/karafka/tree/master/spec/integrations) and [example apps](https://github.com/karafka/example-apps) to create a reproduction code that you can then share with us.

## Debugging

Remember that Karafka uses the `info` log level by default. If you assign it a logger with `debug,` debug will be used.

Here are a few guidelines that you should follow when trying to create a reproduction script:

1. Ensure you are using the most recent versions of all the Karafka ecosystem gems. The issue you are facing might have already been fixed.
2. Use as few non-default gems as possible to eliminate issues emerging from other libraries.
3. Try setting the `concurrency` value to `1` - this will simplify the processing flow.

```ruby
class KarafkaApp < Karafka::App
  setup do |config|
    config.concurrency = 1
  end
end
```

4. Use a single topic with a single partition (so Karafka does not create extensive concurrent jobs).

```ruby
class KarafkaApp < Karafka::App
  setup do |config|
    # ...
  end

  routes.draw do
    # Disable other topics for debug...
    # topic :shippings do
    #   consumer ShippingsConsumer
    # end

    topic :orders do
      consumer OrdersConsumer
    end
  end
end
```

5. If the issue is related to Active Job or Ruby on Rails, try using the latest stable release.
6. Check the [Versions Lifecycle and EOL](Versions-Lifecycle-and-EOL) page to make sure that your Ruby and Ruby on Rails (if used) combination is supported.
7. Try disabling all Karafka components that may be irrelevant to the issue, like extensive listeners and other hooks.
8. You can use `TTIN` [signal](Signals-and-states#signals) to print a backtrace of all the Karafka threads if Karafka appears to be hanging or dead. For this to work, the `LoggerListener` needs to be enabled.
9. If you are interested/need extensive `librdkafka` debug info, you can set the kafka `debug` flag to `all` or one of the following values: `generic`, `broker`, `topic`, `metadata`, `feature`, `queue`, `msg`, `protocol`, `cgrp`, `security`, `fetch`, `interceptor`, `plugin`, `consumer`, `admin`, `eos`, `mock`, `assignor`, `conf`, `all`.

```ruby
class KarafkaApp < Karafka::App
  setup do |config|
    config.kafka = {
      'bootstrap.servers': '127.0.0.1:9092',
      # other settings...
      debug: 'all'
    }
  end
end
```
