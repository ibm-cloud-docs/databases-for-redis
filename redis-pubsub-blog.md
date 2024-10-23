---
copyright:
  years: 2024
lastupdated: "2024-10-17"

subcollection: databases-for-redis

---

# Redis Pub/Sub
{: #redis-pub-sub}

Pub/Sub is a means of collecting data from any number of data _publishers_ and allowing 1 or more _subscribers_ to consume the feed in real time, without having to connect to the publishers directly. This pattern allows systems to scale more easily and allows publishers and subscribers to be added or removed at any time.

Redis allows the creation of Pub/Sub _channels_ to which publishers and subscribers interface. Messages on a channel are sent _at most once_ to each subscriber making them ideal for trigger actions which must not be duplicated, but beware because once delivered, a channel message is not persisted.

Use-cases:

- Real-time message.
- Ephemeral multi-user chat.
- Log streaming.

## Subscribing to a channel
{: #redis-subscribing}

A _subscriber_ simply issues a "SUBSCRIBE <channel>" command to begin subscribing to a channel:

```redis
> SUBSCRIBE mychannel
```

If a subscriber needs to subscribe to more than one channel, then this is possible too:

```redis
> SUBSCRIBE mychannel1 mychannel2 mychannel3
```

Subscribing to a channel means that the subscriber will receieve any messages that are published to that channel _after_ they have subscribed. Older messages are not persisted, so the subscriber must be connected and subscribed to get messages as they happen.

If the channel does not exist then the subscriber will see no errors, nor will they see any messages.

## Publishing to a channel
{: #redis-publishing}

A publisher can publish to a channel with the `PUBLISH <channel> <message>` command:

```redis
> PUBLISH mychannel "my first message"
```

and any subscribers will receive the message as follows:

```redis
1) "message"
2) "mychannel"
3) "my first message"
```

The subscription message includes the channel name and the message itself.

A common pattern is to send JSON objects as the message text:

```redis
> PUBLISH mychannel '{"id":"1234","name":"db14","msg":"Out of Memory"}'
```

The return value from `PUBLISH` is an integer that tells the caller how many subscribers received the message.

## Selective subscription
{: #redis-selective-subscription}

Instead of subscribing to whole channels, it is possible to subscribe to wildcard channels. By prudent naming of channels, this can be helpful to see, for example, all of an application's logs, just its production logs, or just its production database's logs.

```redis
> PSUBSCRIBE *
> PSUBSCRIBE prod_*
> PSUBSCRIBE prod_database_*
> PSUBSCRIBE prod_database_db57_activity_*
```

## Unsubscribing
{: #redis-unsubscribing}

A subscriber can unsubscribe to all of its subscribed channels with:

```redis
> UNSUBSCRIBE
```

## Further reading
{: #redis-further-reading}

- [Redis Pub/Sub](https://redis.io/docs/latest/develop/interact/pubsub/){: .external}
  
