---
description: Manage your Feature Flags and Remote Config in your REST APIs.
sidebar_label: Overview
sidebar_position: 1
---

## Introduction

Flagsmith ships with SDKs for a bunch of different programming languages. We also have a [REST API](rest.md) that you
can use if you want to consume the API directly.

The Server Side SDKs (e.g Python, Ruby etc) can operate in 2 different modes:

1. Remote Evaluation
2. Local Evaluation

In order to make the right choice, it's worth digging into how these two approaches are different, and what the pros and
cons of each one are.

### 1 - Remote Evaluation

In this mode, every time the SDK needs to get Flag values for a particular Identity, it will make a blocking request to
the Flagsmith API to get the flags. For Remote Evaluation, use the `Environment Key`.

This is the same way that the [Client Side SDKs](#client-side-sdks) work.

### 2 - Local Evaluation

In this mode, all flag values are calculated locally, within your server. When the SDK is initialised, it will grab the
entire set of details about the Environment. This will include all the Flags, Flag values, Segment rules, Segment
overrides etc for that Environment. This full complement of data about the Environment enables the Flagsmith SDK to run
the Flag Engine _locally_ and _natively_ within your server infrastructure.

The benefits to doing this are mainly one of latency and performance. Your server side code does not need to hit the
Flagsmith API each time a user requests their flags - the flags can be calculated locally. Hence it does not need to
block and wait for the response back from the Flagsmith API.

:::tip

The SDK has to request all of the data about an Environment in order to run. Because some of this data could be
sensitive (for example, your Segment Rules), the SDK requires a specific `Server Side API Key`. This is different to the
regular `Environment Key`. This key should _not_ be shared, and should be considered sensitive data.

:::

In order to keep their Environment data up-to-date, SDKs running in Local Evaluation mode will poll the Flagmsith API
regularly and update their local Environment data with any changes from the Flagsmith API. By default the SDK will poll
the Flagsmith every `60` seconds; this rate is configurable within each SDK.

It's important to understand the [pros and cons](#pros-cons-and-caveats) for runing Local Evaluation.

## Client Side SDKs

All our Client Side SDKs run in `Remote Evaluation` mode only; they cannot run in Local Evaluation mode. The reason for
this is down to data sensitivity. Because some of this data could be sensitive (for example, your Segment Rules), we
only allow Client Side SDKs to run in `Remote Evaluation` mode.

:::info

Because Clients are almost always operating remotely from your server infrastructure, there is little benefit to them
running in `Local Evaluation` mode.

:::

## Pros, Cons and Caveats

When running _Remote Evaluation_, all aspects of the Flagsmith platform are enabled. This includes:

- Identities are presisted within the Flagsmith Datastore.
- Identity overrides specified within the Dashboard
- All Integrations work as designed.

When running in _Local Evaluation_ mode, the following caveats are important to bear in mind:

- Identities are _not_ sent to the API and so are not persisted in the datastore.
- Identity overrides do not operate at all.
- Integrations do not run.