# Kafka Rust Client

[![Build Status](https://travis-ci.org/spicavigo/kafka-rust.svg?branch=master)](https://travis-ci.org/spicavigo/kafka-rust) [![](http://meritbadge.herokuapp.com/kafka)](https://crates.io/crates/kafka)

### Documentation

[Kafka Rust Client Documentation](http://fauzism.co/rustdoc/kafka/index.html)

The documentation includes some examples too.


### Installation

This crate works with Cargo and is on [crates.io](https://crates.io/crates/kafka). I will be updating the package frequently till we move out of pre-release. So add this to your `Cargo.toml` (instead of a specific version):

```toml
[dependencies]
kafka = "*"
```

#### Usage:

##### Load topic metadata:
[Load Metadata] (http://fauzism.co/rustdoc/kafka/client/struct.KafkaClient.html#method.load_metadata_all)
```rust
extern crate kafka;
use kafka::client::KafkaClient;
fn main() {
    let mut client = KafkaClient::new(&vec!("localhost:9092".to_string()));
    client.load_metadata_all();
    // OR 
    // client.load_metadata(&vec!("my-topic".to_string())); // Loads metadata for vector of topics
 }
```
##### Fetch Offsets:

[For one topic] (http://fauzism.co/rustdoc/kafka/client/struct.KafkaClient.html#method.fetch_topic_offset)

```rust
extern crate kafka;
use kafka::client::KafkaClient;
fn main() {
    let mut client = KafkaClient::new(&vec!("localhost:9092".to_string()));
    client.load_metadata_all();
    let offsets = client.fetch_topic_offset(&"my-topic".to_string());
}
```

[For multiple topics] (http://fauzism.co/rustdoc/kafka/client/struct.KafkaClient.html#method.fetch_offsets)

```rust
extern crate kafka;
use kafka::client::KafkaClient;
fn main() {
    let mut client = KafkaClient::new(&vec!("localhost:9092".to_string()));
    client.load_metadata_all();
    let topics = client.topic_partitions.keys().cloned().collect();
    let offsets = client.fetch_offsets(topics);
}
```
##### Produce:

[Single Message] (http://fauzism.co/rustdoc/kafka/client/struct.KafkaClient.html#method.send_message)

```rust
extern crate kafka;
use kafka::client::KafkaClient;
fn main() {
    let mut client = KafkaClient::new(&vec!("localhost:9092".to_string()));
    client.load_metadata_all();
    client.send_message(
        1,                              // Required Acks
        0,                              // Timeout
        &"my-topic".to_string(),        // Topic
        0,                              // Partition
        &"b".to_string().into_bytes()   // Message
    )
}
```

[Multiple Messages] (http://fauzism.co/rustdoc/kafka/client/struct.KafkaClient.html#method.send_messages)

```rust
extern crate kafka;
use kafka::client::KafkaClient;
use kafka::utils;
fn main() {
    let mut client = KafkaClient::new(&vec!("localhost:9092".to_string()));
    client.load_metadata_all();
    let m1 = "a".to_string().into_bytes();
    let m2 = "b".to_string().into_bytes();
    let req = vec!(utils::ProduceMessage{topic: "my-topic".to_string(), message: m1},
                    utils::ProduceMessage{topic: "my-topic-2".to_string(), message: m2});
    client.send_messages(1, 100, req);  // required acks, timeout, messages
}
```

##### Fetch Messages:

[Single (topic, partition, offset)] (http://fauzism.co/rustdoc/kafka/client/struct.KafkaClient.html#method.fetch_messages)

```rust
extern crate kafka;
use kafka::client::KafkaClient;
fn main() {
    let mut client = KafkaClient::new(&vec!("localhost:9092".to_string()));
    client.load_metadata_all();
    // Topic, partition, offset
    let msgs = client.fetch_messages(&"my-topic".to_string(), 0, 0);
}
```

[Multiple (topic, partition, offset)] (http://fauzism.co/rustdoc/kafka/client/struct.KafkaClient.html#method.fetch_messages_multi)

```rust
extern crate kafka;
use kafka::client::KafkaClient;
use kafka::utils;
fn main() {
    let mut client = KafkaClient::new(&vec!("localhost:9092".to_string()));
    client.load_metadata_all();
    let msgs = client.fetch_messages_multi(vec!(utils::TopicPartitionOffset{
                                                    topic: "my-topic".to_string(),
                                                    partition: 0,
                                                    offset: 0
                                                    },
                                                utils::TopicPartitionOffset{
                                                    topic: "my-topic-2".to_string(),
                                                    partition: 0,
                                                    offset: 0
                                                })));
}
```
#### TODO:

* Tests - (Added tests for gzip.rs, snappy.rs, and codecs.rs)
* Offset Management functions (Not thoroughly tested)
