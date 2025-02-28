---
description: IOTA Client Library user-friendly API specifications.
image: /img/logo/iota_mark_light.png
keywords:
- definition
- message
- return
- types
- parameters
- address
- account
- reference
---
# High Level API Specification

## Introduction

This document specifies a user-friendly API used in the client libraries. The main implementation is in Rust, which will
receive automatically compiled client libraries in other languages via C or WebAssembly bindings. There are many crates
to support developers who want to create foreign function interfaces with native bindings.

## Builder

The data structure to initialize the instance of the Higher level client library. It is always called first when
starting a new interaction with the library.

:::note

This is the standard approach to do initialization in Rust. Different languages might use different methods, such as
directly calling an initialization function.

:::

### Parameters

#### network

| Required | Default Value | Type | Definition                                                                                                                                                                                                         |
|----------|---------------|------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | devnet       | &str | Optional, the network type can be "devnet" or "mainnet". If you don not provide a node url is provided, default nodes will be used for the specified network. Nodes that are not in this network will be ignored. |

#### node

| Required | Default Value | Type | Definition                                                              |
|----------|---------------|------|-------------------------------------------------------------------------|
| ✘        | None          | &str | The URL of the node you want to connect to; format: `https://node:port` |

#### primary_node

| Required | Default Value | Type                                      | Definition                                                                                                                                                                                                                                         |
|----------|---------------|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | None          | &str, auth_name_pwd: Option<(&str, &str)> | The URL of a node to always you want to connect to first, if multiple nodes are available. <br/>(optional) You can use a JWT and or name and password for basic authentication; format: `https://node:port`, Some("JWT"), Some("name", "password") |

#### primary_pow_node

| Required | Default Value | Type                                      | Definition                                                                                                                                                                                                                                                                                                                                |
|----------|---------------|-------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | None          | &str, auth_name_pwd: Option<(&str, &str)> | The URL of a node to always you want to connect to first when submitting a message with remote PoW, if multiple nodes are available.  This will override primary_node in that case**. <br/>(optional) You can use a JWT or name and password for basic authentication; format: `https://node:port`, Some("JWT"), Some("name", "password") |

#### permanode

| Required | Default Value | Type                                      | Definition                                                                                                                                                                  |
|----------|---------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | None          | &str, auth_name_pwd: Option<(&str, &str)> | The URL of a permanode. <br/>(optional) You can use a JWT or name and password for basic authentication; format: `https://node:port`, Some("JWT"), Some("name", "password") |

#### node_auth

| Required | Default Value | Type                                        | Definition                                                                                                                                                                                    |
|----------|---------------|---------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | None          | &str, Option<String/>, Option<(&str, &str)> | The URL of a node to connect to with. <br/>(optional)You can use a  JWT and or name and password for basic authentication; format: `https://node:port`, Some("JWT"), Some("name", "password") |

#### nodes

| Required | Default Value | Type    | Definition                                                                                                                                                                                                                                                                                                   |
|----------|---------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | None          | &[&str] | A list of nodes to connect to. You can add nodes are using the `https://node:port` format.<br/> The amount of nodes specified in quorum_size is randomly selected from this node list to check for quorum based on the quorum threshold.<br/> If quorum_size is not given the full list of nodes is checked. |

#### node_sync_interval

| Required | Default Value           | Type                | Definition                                                      |
|----------|-------------------------|---------------------|-----------------------------------------------------------------|
| ✘        | Duration::from_secs(60) | std::time::Duration | The interval in milliseconds to check for node health and sync. |

#### node_sync_disabled

| Required | Default Value | Type | Definition                                     |
|----------|---------------|------|------------------------------------------------|
| ✘        | false         | bool | If disabled unhealthy nodes will also be used. |

#### node_pool_urls

| Required | Default Value | Type      | Definition                                                                                                                                                                                                                                                             |
|----------|---------------|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | None          | &[String] | A list of node_pool_urls from which nodes are added. <br/>The amount of nodes specified in quorum_size is randomly selected from this node list to check for quorum based on the quorum threshold. <br/>If quorum_size is not given the full list of nodes is checked. |

#### quorum

| Required | Default Value | Type | Definition                                        |
|----------|---------------|------|---------------------------------------------------|
| ✘        | false         | bool | Defines if quorum should be used for the requests |

##### quorum_size

| Required | Default Value | Type  | Definition                                       |
|----------|---------------|-------|--------------------------------------------------|
| ✘        | 3             | usize | Defines how many nodes should be used for quorum |

#### quorum_threshold

| Required | Default Value | Type  | Definition                                                                |
|----------|---------------|-------|---------------------------------------------------------------------------|
| ✘        | 66            | usize | Defines the % of nodes that need to return the same response to accept it |

#### request_timeout

| Required | Default Value           | Type                | Definition                                                                                     |
|----------|-------------------------|---------------------|------------------------------------------------------------------------------------------------|
| ✘        | Duration::from_secs(30) | std::time::Duration | The amount of seconds a request can be outstanding to a node before it is considered timed out |

#### api_timeout

| Required | Default Value                                                                                                                                                                                                                                                                                                                                                                  | Type                                      | Definition                                                                                                                      |
|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| ✘        | Api::GetInfo: Duration::from_secs(2)), <br/>Api::GetHealth: Duration::from_secs(2),<br/>Api::GetPeers: Duration::from_secs(2), <br/>Api::GetMilestone: Duration::from_secs(2),<br/>Api::GetTips: Duration::from_secs(2), <br/>Api::PostMessage: Duration::from_secs(2),<br/>Api::PostMessageWithRemotePow: Duration::from_secs(30),<br/>Api::GetOutput: Duration::from_secs(2) | HashMap<[Api],<br /> std::time::Duration> | The amount of milliseconds a request to a specific API endpoint can be outstanding to a node before it is considered timed out. |

##### local_pow

| Required | Default Value | Type | Definition                                                         |
|----------|---------------|------|--------------------------------------------------------------------|
| ✘        | True          | bool | If not defined it defaults to local PoW to offload node load times |

#### tips_interval

| Required | Default Value | Type | Definition                                            |
|----------|---------------|------|-------------------------------------------------------|
| ✘        | 15            | u64  | Time interval during PoW when new tips get requested. |

##### mqtt_broker_options

| Required | Default Value                                 | Type                            | Definition                                     |
|----------|-----------------------------------------------|---------------------------------|------------------------------------------------|
| ✘        | True,<br />Duration::from_secs(30),<br />True | [BrokerOptions](#BrokerOptions) | If not defined the default values will be used |

::: note

There must be at least one node to build the instance successfully.

:::

### Return

If you finalize the builder with `finish()` it will run the instance in the background. You do not need to worry about
handling the return object.

## On Initialization

On initialisation, calls the getNodeInfo API. This will check the health of each node in the node list, and put the
healthy nodes, which match the PoW settings and network in a synced `nodelist`.

| Node metadata | Description                                                                                                                    |
|---------------|--------------------------------------------------------------------------------------------------------------------------------|
| network       | If this parameter does not match the global builder parameter, you should not add it to the synced nodelist.                   |
| pow           | If the global local_pow parameter is set to false, then you should only use nodes with the PoW feature in the synced nodelist. |

## Sync Process

When you build `Client` instance (The instance used for calling the client APIs), the status of each listed node will be
checked. If the returned status of the node information is healthy, which means the node is synced, then this node will
be pushed into a `synced_nodes` list. The rust-like pseudocode of the `synced_nodes` construction process follows. The
node syncing process is repeated every 60 seconds by default. You can change this number by specifying the
initializer's `node_sync_interval` argument.

```rust
synced_nodes = Vec::new()
for node in node_pool_urls{
   status = Client.get_info(node).await?;
   if status == healthy{
      synced_nodes.push(node)
   }
}
```

## General High Level API

Here is the high level abstraction API collection with sensible default values for users easy to use.

### message()

A generic send function you can use to easily send a message.

#### Parameters

Depending on the provided values this function will create a message with:

* No payload.
* An indexation payload.
* A transaction payload.
* A transaction payload containing an indexation payload.

##### seed

| Required | Default | Type                     | Definition                                                                        |
|----------|---------|--------------------------|-----------------------------------------------------------------------------------|
| ✘        | None    | [Seed](#seed-2)(#seed-1) | The seed of the account you are going to spend from. Only needed for transactions |

##### account_index

| Required | Default | Type  | Definition                                                                               |
|----------|---------|-------|------------------------------------------------------------------------------------------|
| ✘        | 0       | usize | The account index responsible for the value `✘` in the Bip32Path `m/44'/4218'/✘'/0'/0'`. |

##### initial_address_index

| Required | Default | Type  | Definition                                                                                                                |
|----------|---------|-------|---------------------------------------------------------------------------------------------------------------------------|
| ✘        | 0       | usize | The index from where to start looking for balance. Responsible for the value `✘` in the Bip32Path `m/44'/4218'/0'/0'/✘'`. |

##### input

| Required | Default | Type      | Definition                                                                                   |
|----------|---------|-----------|----------------------------------------------------------------------------------------------|
| ✘        | None    | UtxoInput | Users can manually select their UtxoInputs, instead of having automatically selected inputs. |

##### input_range

| Required | Default | Type  | Definition                                                                                       |
|----------|---------|-------|--------------------------------------------------------------------------------------------------|
| ✘        | 0..100  | Range | If custom inputs are provided, this custom range will be used to search for the input addresses. |

##### output

| Required | Default | Type                                 | Definition                                                                              |
|----------|---------|--------------------------------------|-----------------------------------------------------------------------------------------|
| ✘        | None    | address: &[String],<br />amount: u64 | Address you want to send to and amount to send. The address needs to be Bech32 encoded. |

##### output_hex

| Required | Default | Type                            | Definition                                                                           |
|----------|---------|---------------------------------|--------------------------------------------------------------------------------------|
| ✘        | None    | address: &str,<br />amount: u64 | Address you want to send to and amount to send. The address needs to be hex encoded. |

##### index

| Required | Default | Type         | Definition                                                             |
|----------|---------|--------------|------------------------------------------------------------------------|
| ✘        | None    | &[u8] / &str | An optional indexation key for an indexation payload. 1-64 bytes long. |

##### data

| Required | Default | Type       | Definition                                |
|----------|---------|------------|-------------------------------------------|
| ✘        | None    | Vec&lt;u8> | Optional data for the indexation payload. |

##### parents

| Required | Default | Type                    | Definition                                              |
|----------|---------|-------------------------|---------------------------------------------------------|
| ✘        | None    | [MessageId](#messageid) | 1-8 optional parents [messageId](#messageid)to be used. |

#### Return

The [Message](#message-1) object we build.

#### Implementation Details

When implementing this function please make sure that you:

* Validate inputs, such as address and seed to check that they are correct.
* Check if the account balance is bigger or equal to the value using method similar to [`get_balance()`](#get_balance).
* Build and validate the message with signed transaction payload accordingly.
* Get tips using the [`get_tips()`](#get_tips) method.
* Perform proof-of-work locally (if not set to remote).
* Send the message using the [`post_messages()`](#post_messages) method.

### get_message()

(`GET /api/v1/messages`)

Endpoint collection of all GET messages.

#### Parameters

##### message_id

| Required | Type                    | Definition                |
|----------|-------------------------|---------------------------|
| ✔        | [messageId](#messageid) | The message's identifier. |

##### index

| Required | Type         | Definition         |
|----------|--------------|--------------------|
| ✔        | &[u8] / &str | An indexation key. |

#### Returns

Depending on the final calling method, users cam get different results according to their needs:

- `metadata(&MessageId)`: Returns [MessageMetadata](#messagemetadata) of the message.
- `data(&MessageId)`: Returns a [Message](#message-1) object.
- `raw(&MessageId)`: Returns the raw data of given message.
- `children(&MessageId)`: Returns the list of [MessageId](#messageid)s that reference a message by its identifier.
- `index(&[u8] | &str)` : Returns the list of [MessageId](#messageid)s that have this str as indexation key

### find_messages()

Find all messages using the provided message IDs.

#### Parameters

##### indexation_keys

| Required | Type           | Definition                               |
|----------|----------------|------------------------------------------|
| ✘        | [&[u8] / &str] | The index key of the indexation payload. |

##### message_ids

| Required | Type                    | Definition                |
|----------|-------------------------|---------------------------|
| ✘        | [MessageId](#messageid) | The message's identifier. |

#### Returns

A vector of [Message](#message-1) Object.

### get_unspent_address()

Return a valid unspent public Bech32 encoded address.

#### Parameters

##### seed

| Required | Default | Type            | Definition               |
|----------|---------|-----------------|--------------------------|
| ✔        | -       | [Seed](#seed-2) | The seed we want to use. |

##### account_index

| Required | Default | Type  | Definition                                                                                    |
|----------|---------|-------|-----------------------------------------------------------------------------------------------|
| ✘        | 0       | usize | The account index.<br/>Responsible for the value `✘` in the Bip32Path `m/44'/4218'/✘'/0'/0'`. |

##### initial_address_index

| Required | Default | Type  | Definition                                                                                                          |
|----------|---------|-------|---------------------------------------------------------------------------------------------------------------------|
| ✘        | 0       | usize | Start index of the addresses to search.<br/> Responsible for the value `✘` in the Bip32Path `m/44'/4218'/0'/0'/✘'`. |

#### Return

Return a tuple with type of `(String, usize)` as the address and corresponding index in the account.

#### Implementation Details

Please make sure that you follow these steps when implementing this method:

* Start by generating addresses with given account index and starting index. We have a
  default [gap limit](https://blog.blockonomics.co/bitcoin-what-is-this-gap-limit-4f098e52d7e1) of 20 at a time.
* Check for balances on the generated addresses using the [find_outputs()](#find_outputs) method and keep track of the
  positive balances.
* Repeat the above step until you find an unspent address.
* Return the address with corresponding index on the wallet chain.

### get_addresses()

Returns a list of addresses from the seed regardless of their validity.

#### Parameters

##### seed

| Required | Default | Type            | Definition                       |
|----------|---------|-----------------|----------------------------------|
| ✔        | None    | [Seed](#seed-2) | The seed you want to search for. |

##### account_index

| Required | Default | Type  | Definition                                                                                    |
|----------|---------|-------|-----------------------------------------------------------------------------------------------|
| ✘        | 0       | usize | The account index.<br/>Responsible for the value `✘` in the Bip32Path `m/44'/4218'/✘'/0'/0'`. |

##### range

| Required | Default | Type            | Definition                                                                |
|----------|---------|-----------------|---------------------------------------------------------------------------|
| ✘        | None    | std::ops::Range | Range indices of the addresses you want to search for. Default is (0..20) |

##### get_all

| Required | Default | Type | Definition                                                                                                                                                                                                           |
|----------|---------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | ✘       | ✘    | Get public and [change addresses](https://bitcoin.stackexchange.com/questions/75033/bip44-and-change-addresses). <br/>Will return Vec<([String], bool)>, where the bool is indicating whether it is a change address |

#### Return

A Vec<[String]>, with the public addresses

### get_balance()

Returns the balance for a provided seed and its wallet account index.

#### Parameters

##### seed

| Required | Default | Type            | Definition                       |
|----------|---------|-----------------|----------------------------------|
| ✔        | -       | [Seed](#seed-2) | The seed you want to search for. |

##### account_index

| Required | Default | Type  | Definition                                                                                     |
|----------|---------|-------|------------------------------------------------------------------------------------------------|
| ✘        | 0       | usize | The account index. <br/>Responsible for the value `✘` in the Bip32Path `m/44'/4218'/✘'/0'/0'`. |

##### initial_address_index

| Required | Default | Type  | Definition                                                                                                                                       |
|----------|---------|-------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | 0       | usize | Start index from which to generate addresses. The default valu is 0. <br/>Responsible for the value `✘` in the Bip32Path `m/44'/4218'/0'/0'/✘'`. |

##### gap_limit

| Required | Default | Type  | Definition                                                                                                                                                      |
|----------|---------|-------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | 20      | usize | The gap limit specifies how many addresses will be checked each round. <br/>If gap_limit amount of addresses in a row have no balance the function will return. |

#### Return

Total account balance.

#### Implementation Details

Please make sure to follow these steps when you are implementing this method:

* Start by generating addresses with given wallet account index and starting index. You will have a
  default [gap limit](https://blog.blockonomics.co/bitcoin-what-is-this-gap-limit-4f098e52d7e1) of 20 at a time.
* Check for balances on the generated addresses using the [`find_outputs()`](#find_outputs) method and keep track of the
  positive balances.
* Repeat the step above until you find an address with zero balance;
* Accumulate the positive balances and return the result.

### get_address_balances()

Return the balance in iota for the given addresses. You do not need a seed or security level to do this since you are
only checking and already know the addresses.

#### Parameters

##### addresses

| Required | Type       | Definition                        |
|----------|------------|-----------------------------------|
| ✔        | [[String]] | List of Bech32 encoded addresses. |

#### Return

A list of tuples with value of [AddressBalancePair](#addressbalancepair). The usize is the balance of the address.

#### Implementation details:

Please make sure to follow these steps when implementing this method:

* Validate the _address_ semantics.
* Get the latest balance for the provided address using the [`find_outputs()`](#find_outputs) method with the addresses
  as parameter.
* Return the list of Output which contains corresponding pairs of addresses and balances.

### generate_mnemonic()

Returns a random generated Bip39 mnemonic with words from the English word list.

#### Return

Parsed [String].

### mnemonic_to_hex_seed(mnemonic)

Returns the hex encoded seed.

#### Parameters

##### mnemonic

| Required | Type     | Definition                                              |
|----------|----------|---------------------------------------------------------|
| ✔        | [String] | A Bip39 mnemonic with words from the English word list. |

#### Return

Parsed [String].

### bech32_to_hex()

Returns a parsed hex String from bech32.

#### Parameters

#### bech32

| Required | Type     | Definition              |
|----------|----------|-------------------------|
| ✔        | [String] | Bech32 encoded address. |

#### Return

Parsed [String].

### hex_to_bech32()

Returns a parsed bech32 String from hex.

#### Parameters

##### hex

| Required | Type     | Definition           |
|----------|----------|----------------------|
| ✔        | [String] | Hex encoded address. |

##### bech32_hrp

| Required | Type              | Definition           |
|----------|-------------------|----------------------|
| ✔        | [Option<String/>] | Optional bech32 hrp. |

#### Return

A parsed [String].

### parse_bech32_address()

Returns a valid [Address](#address) parsed from a String.

#### Parameters

##### address

| Required | Type     | Definition              |
|----------|----------|-------------------------|
| ✔        | [String] | Bech32 encoded address. |

#### Return

A parsed [Address].

### is_address_valid()

#### Parameters

##### address

| Required | Type     | Definition              |
|----------|----------|-------------------------|
| ✔        | [String] | Bech32 encoded address. |

#### Return

A boolean showing if the address is valid.

### subscriber()

Subscribe to a node event [Topic](#topic)(MQTT).

Requires one of:

* `topic()`: Add a new [Topic](#topic) to the list.
* `topics()`: Add a vector of [Topic](#topic) to the list.
* `subscribe()`: Subscribe to the given topics with the callback, which will be called every time when the topic is
  detected.
* `unsubscribe()`: Unsubscribes from all subscriptions.
* `disconnect()`: Disconnects the broker. This will clear the stored topic handlers and close the MQTT connection.

#### Returns

An `Ok(())` result if the call was successful.

### retry()

Retries (promotes or reattaches) a message for the provided [messageId](#messageid) if the node suggests it. As the
confirmation throughput of the node is expected to be quite high, you should not need to use this function often.

#### Parameters

##### message_id

| Required | Type                    | Definition                 |
|----------|-------------------------|----------------------------|
| ✔        | [messageId](#messageid) | The identifier of message. |

#### Returns

A tuple with the newly promoted or reattached `(MessageId, Message)`.

#### Implementation Details

Please make sure to follow these steps when implementing this method:

* You should only allow unconfirmed messages to retry. The method will validate the confirmation state of the provided
  messages. If a message id of a confirmed message is provided, the method will return an error.
* The method will also validate if a retry is necessary. This can be done by leveraging
  the `/messages/{messageId}/metadata` endpoint (already available through [get_message](#get_message)).
  See [this](https://github.com/iotaledger/trinity-wallet/blob/develop/src/shared/libs/iota/transfers.js#L105-L131)
  implementation for reference.
* Use [reattach](#reattach) or [promote](#promote) accordingly.

### retry_until_included()

Retries (promotes or reattaches) a message for provided [messageId](#messageid) until it is included (referenced by a
milestone). The default interval is 5 seconds and the max number of attempts is 40. As the confirmation throughput of
the node is expected to be quite high, you should not need to use this function often.

#### Parameters

##### message_id

| Required | Type                       | Definition                 |
|----------|----------------------------|----------------------------|
| ✔        | [&[MessageId](#messageid)] | The identifier of message. |

##### interval

| Required | Type           | Definition                                  |
|----------|----------------|---------------------------------------------|
| ✘        | Option&lt;u64> | The interval in which we retry the message. |

##### max_attempts

| Required | Type           | Definition                                    |
|----------|----------------|-----------------------------------------------|
| ✘        | Option&lt;u64> | The maximum of attempts we retry the message. |

#### Returns

An array of tuples with the newly reattached `(MessageId, Message)`.

### consolidate_funds()

You can use this function to consolidate all funds from a range of addresses to the address with the lowest index in
that range.

#### Parameters

##### seed

| Required | Type            | Definition                       |
|----------|-----------------|----------------------------------|
| ✔        | [Seed](#seed-2) | The seed you want to search for. |

##### account_index

| Required | Type  | Definition                                                                                    |
|----------|-------|-----------------------------------------------------------------------------------------------|
| ✘        | usize | The account index.<br/>Responsible for the value `✘` in the Bip32Path `m/44'/4218'/✘'/0'/0'`. |

##### address_range

| Required | Type          | Definition                                                                                                                                                                 |
|----------|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ✘        | Range<usize/> | Range from which to generate public and internal addresses from which to consolidate the funds.<br/>Responsible for the value `✘` in the Bip32Path `m/44'/4218'/0'/0'/✘'`. |

#### Returns

The address in which the funds got consolidated.

### reattach()

Depends on [find_messages](#find_messages), [get_message](#get_message) and [post_message](#post_message).

Reattaches a message. The method will validate if a reattachment is necessary using the [get_message](#get_message)
method. If a reattachment is not necessary, the method will error out and will not allow unnecessary reattachments.

#### Parameters

##### message_id

| Required | Type                    | Definition                 |
|----------|-------------------------|----------------------------|
| ✔        | [messageId](#messageid) | The identifier of message. |

#### Returns

A tuple with the newly reattached `(MessageId, Message)`.

### promote()

Depends on [find_messages](#find_messages), [get_message](#get_message) and [post_message](#post_message).

Reattaches a message. The method will validate if a reattachment is necessary using the [get_message](#get_message)
method. If a promotion is not necessary, the method will error out and will not allow unnecessary promotions.

#### Parameters

#### message_id

| Required | Type                    | Definition                 |
|----------|-------------------------|----------------------------|
| ✔        | [messageId](#messageid) | The identifier of message. |

#### Returns

A tuple with the newly promoted `(MessageId, Message)`.

## Full Node API

The full node APIs of [Bee](https://wiki.iota.org/bee/welcome) and [Hornet](https://wiki.iota.org/hornet/welcome) will
still be public. Users who are familiar with these relative low level Restful APIs can still call them directly if they
are confident in their usage.

:::note

Given that both Bee and Hornet APIs are still in active development, the following items and signatures may change in
the future.

:::

### get_health()

(`GET /health`)

Returns the health of the node, which can be used for load-balancing or uptime monitoring.

#### Parameters

None.

#### Returns

Boolean to indicate if node is healthy.

### get_peers()

(`GET /peers`)

Get information about the peers of the node.

#### Parameters

None.

#### Returns

```Rust
pub struct PeerDto {
    pub id: String,
    #[serde(rename = "multiAddresses")]
    pub multi_addresses: Vec<String/>,
    pub alias: Option<String/>,
    pub relation: RelationDto,
    pub connected: bool,
    pub gossip: Option<GossipDto/>,
}
```

### get_info()

(`GET /api/v1/info`)

Returns information about the node.

#### Parameters

None

#### Returns

A Response Object similar to:

```rust
pub struct NodeInfoWrapper {
    pub nodeinfo: NodeInfo,
    pub url: String,
}
pub struct NodeInfo {
    pub name: String,
    pub version: String,
    pub is_healthy: bool,
    pub network_id: String,
    pub latest_milestone_index: usize,
    pub min_pow_score: f64,
    pub messages_per_second: f64,
    pub referenced_messages_per_second: f64,
    pub referenced_rate: f64,
    pub latest_milestone_timestamp: u64,
    pub confirmed_milestone_index: usize,
    pub pruning_index: usize,
    pub features: Vec<String/>,
}
```

### get_tips()

(`GET /tips`)

Returns two non-lazy tips. If the node can only provide one tip, tip1 and tip2 will be identical.

#### Parameters

None.

#### Returns

A tuple with two [[MessageId](#messageid)]s:

```rust
(MessageId, MessageId)
```

### post_message()

(`POST /message`)

Submits a message. The node will take care of any missing fields and attempt to build the message. On success, the
message will be stored in the Tangle. This endpoint will return the identifier of the message.

#### Parameters

##### message

| Required | Type                  | Definition          |
|----------|-----------------------|---------------------|
| ✔        | [Message](#message-1) | The message object. |

#### Returns

The [messageId](#messageid) of the message object.

### get_output()

(`GET /outputs`)

Get the producer of the output, the corresponding address, amount and spend status of an output. This information can
only be retrieved for outputs which are part of a confirmed transaction.

#### Parameters

##### output_id

| Required | Type      | Definition                |
|----------|-----------|---------------------------|
| ✔        | UtxoInput | Identifier of the output. |

#### Returns

An [OutputMetadata](#OutputMetadata) that contains information about the output.

### get_address()

(`GET /addresses`)

#### Parameters

##### address

| Required | Type     | Definition                 |
|----------|----------|----------------------------|
| ✔        | [String] | The address to search for. |

#### Returns

Depending on the final calling method, you can get different outputs you need:

* `balance()`: Returns the confirmed balance of the address.
* `outputs([options])`: Returns the UtxoInput array (transaction IDs with corresponding output index).

### find_outputs()

Find all outputs based on the requests criteria.

#### Parameters

##### output_id

| Required | Type        | Definition                |
|----------|-------------|---------------------------|
| ✘        | [UtxoInput] | The identifier of output. |

##### addresses

| Required | Type       | Definition                  |
|----------|------------|-----------------------------|
| ✘        | [[String]] | The Bech32 encoded address. |

#### Returns

A vector of [OutputMetadata](#OutputMetadata).

### get_milestone()

(`GET /milestones`)

Get the milestone by the given index.

#### Parameters

##### index

| Required | Type | Definition              |
|----------|------|-------------------------|
| ✔        | u32  | Index of the milestone. |

#### Returns

An [[Milestone](#milestone)] object.

### get_milestone_utxo_changes()

(`GET /milestones/{}/utxo-changes`)

Get all UTXO changes of a given milestone.

#### Parameters

##### index

| Required | Type | Definition              |
|----------|------|-------------------------|
| ✔        | u32  | Index of the milestone. |

#### Returns

```Rust
MilestoneUTXOChanges {
    index: 1,
    created_outputs: [],
    consumed_outputs: [],
}
```

### get_receipts()

(`GET /receipts`)

Get all receipts.

#### Returns

```Rust
Vec<ReceiptDto/>
```

### get_receipts_migrated_at()

(`GET /receipts/{migratedAt}`)

Get all receipts for a given milestone index.

#### Returns

```Rust
Vec<ReceiptDto/>
```

### get_treasury()

(`GET /treasury`)

Get the treasury amount.

#### Returns

```Rust
pub struct TreasuryResponse {
    #[serde(rename = "milestoneId")]
    milestone_id: String,
    amount: u64,
}
```

### get_included_message()

(`GET /transactions/{transactionId}/included-message`)

Get the included message of the transaction.

#### Parameters

##### transaction_id

| Required | Type          | Definition                 |
|----------|---------------|----------------------------|
| ✔        | TransactionId | The id of the transaction. |

#### Returns

```Rust
struct Message {
    parents: Vec<MessageId/>,
    payload: Option<Payload/>,
    nonce: u64,
}
```

## Objects

The objects used in the [API](#full-node-api) aim to provide a secure way to handle certain data structures specified in the IOTA
stack.

### Address

An Ed25519 address which can be encoded in either Bech32 or Hex. Bech32 is preferred and used in most functions.

```Rust
pub enum Address {
    Ed25519(Ed25519Address),
}
```

### AddressBalancePair

```Rust
pub struct AddressBalancePair {
    /// Address, bech32 encoded
    pub address: String,
    /// Balance in the address
    pub balance: u64,
    /// If dust is allowed on the address
    pub dust_allowed: bool,
}
```

### Api

```Rust
pub enum Api {
    /// `get_health` API
    GetHealth,
    /// `get_info`API
    GetInfo,
    /// `get_tips` API
    GetTips,
    /// `post_message` API
    PostMessage,
    /// `post_message` API with remote pow
    PostMessageWithRemotePow,
    /// `get_output` API
    GetOutput,
    /// `get_milestone` API
    GetMilestone,
}
```

### BrokerOptions

```Rust
pub struct BrokerOptions {
    #[serde(default = "default_broker_automatic_disconnect", rename = "automaticDisconnect")]
    pub(crate) automatic_disconnect: bool,
    #[serde(default = "default_broker_timeout")]
    pub(crate) timeout: std::time::Duration,
    #[serde(rename = "maxReconnectionAttempts", default)]
    pub(crate) max_reconnection_attempts: usize,
}
```



### Message

The message object returned by various functions. Based on
the [RFC](https://github.com/GalRogozinski/protocol-rfcs/blob/message/text/0017-message/0017-message.md) for the Message
object. The following is a brief overview of how each of the components in a Message type will look like:

```rust
struct Message {
    parents: Vec<MessageId/>,
    payload: Option<Payload/>,
    nonce: u64,
}

enum Payload {
    Transaction(Box<Transaction/>),
    Milestone(Box<Milestone/>),
    Indexation(Box<Indexation/>),
}

struct Transaction {
    pub essence: TransactionPayloadEssence,
    pub unlock_blocks: Vec<UnlockBlock/>,
}

struct Milestone {
    essence: MilestoneEssence,
    signatures: Vec<Box<[u8]>>,
}

struct Indexation {
    index: String,
    data: Box<[u8]>,
}

struct TransactionPayloadEssence {
    pub(crate) inputs: Box<[Input]>,
    pub(crate) outputs: Box<[Output]>,
    pub(crate) payload: Option<Payload/>,
}

enum Input {
    UTXO(UtxoInput(OutputId)),
}

struct OutputId {
    transaction_id: TransactionId,
    index: u16,
}

enum Output {
    SignatureLockedSingle(SignatureLockedSingleOutput),
}

struct SignatureLockedSingleOutput {
    address: Address,
    amount: u64,
}

enum UnlockBlock {
    Signature(SignatureUnlock),
    Reference(ReferenceUnlock),
}

enum SignatureUnlock {
    Ed25519(Ed25519Signature),
}

struct Ed25519Signature {
    public_key: [u8; 32],
    signature: Box<[u8]>,
}

struct ReferenceUnlock(u16);
```

### MessageId

A 32 bytes array which can represent as hex string.

```rust
struct MessageId([u8; MESSAGE_ID_LENGTH]);
```


### MessageMetadata

```rust
pub struct MessageMetadata {
    /// Message ID
    pub message_id: String,
    /// Message IDs of parents
    pub parents: Vec<String/>,
    /// Solid status
    pub is_solid: bool,
    /// Should promote
    pub should_promote: Option<bool/>,
    /// Should reattach
    pub should_reattach: Option<bool/>,
    /// Referenced by milestone index
    pub referenced_by_milestone_index: Option<u32>,
    /// Ledger inclusion state
    pub ledger_inclusion_state: Option<String/>,
}
```

### Milestone

A milestone metadata.

```rust
pub struct MilestoneMetadata {
    /// Milestone index
    pub milestone_index: u32,
    /// Milestone ID
    pub message_id: String,
    /// Timestamp
    pub timestamp: u64,
}
```

### OutputMetadata

The metadata of an output.

```rust
pub struct OutputMetadata {
    /// Message ID of the output
    pub message_id: Vec<u8>,
    /// Transaction ID of the output
    pub transaction_id: Vec<u8>,
    /// Output index.
    pub output_index: u16,
    /// Spend status of the output
    pub is_spent: bool,
    /// Corresponding address
    pub address: Address,
    /// Balance amount
    pub amount: u64,
}
```

### Seed

An IOTA seed with its inner structure omitted. You can create this type by passing a String. It will verify and return
an error if it is not valid.

```Rust
pub enum Seed {
    /// Ed25519 variant
    Ed25519(Ed25519Seed)
}
```


### Topic

A string with the exact MQTT topic to monitor. It can have one of the following variations:

```
milestones/latest
milestones/confirmed

messages
messages/referenced
messages/indexation/{index}
messages/{messageId}/metadata
transactions/{transactionId}/included-message

outputs/{outputId}

addresses/{address}/outputs
addresses/ed25519/{address}/outputs
```
