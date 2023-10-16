---
title: Getting Started
layout: framework_docs
object: Connect to the Fly.io Machines API and authenticate so you can start making API requests
order: 1
---

This document covers usage of the Machines REST API to start, stop, update and interact with Fly Machines. For the impatient, [flyctl also provides commands](https://fly.io/docs/flyctl/machine/) for experimenting with the API.

See all possible Machine states [in the table below](#machine-states).

## API Spec

We have a Swagger 2.0 specification available at [docs.machines.dev](https://docs.machines.dev) for the Machines API, so that you can autogenerate clients in your preferred language.

## Connecting to the API

This guide assumes that you have `flyctl` and `curl` installed, and have authenticated to Fly.io.

### Using the public `api.machines.dev` endpoint

The easiest (and recommended) way to connect to the Machines API is to use the public `api.machines.dev` endpoint, a simpler and more performant alternative to connecting over WireGuard.

Simply skip down to [setting up the environment](#setting-up-the-environment), and make sure to set `$FLY_API_HOSTNAME` to `https://api.machines.dev`.

### Using the private Machines API endpoint

You can still access your Machines directly over a Wireguard VPN, and use the private Machines API endpoint: `http://_api.internal:4280`. This method requires more setup.

Follow the [instructions](/docs/reference/private-networking/#private-network-vpn) to set up a permanent WireGuard connection to your Fly.io [IPv6 private network](/docs/reference/private-networking/). Once you're connected, Fly internal DNS should expose the Machines API endpoint at: `http://_api.internal:4280`

### Connecting via flyctl 

You can also proxy a local port to the internal API endpoint. This section is preserved mainly for the sake of interest, as the public Machines API endpoint is simpler and more performant.

```cmd
fly machines api-proxy
```

Pick any organization when asked.

With the above command running, in a separate terminal, try this to confirm you can access the API:

```cmd
curl http://127.0.0.1:4280
```

If you successfully reach the API, it should respond with a `404 page not found` error. That's because this was not a defined endpoint.

## Setting up the environment

Set these environment variables to make the following commands easier to use.

```bash
$ export FLY_API_HOSTNAME="https://api.machines.dev" # set to http://_api.internal:4280 when using Wireguard or `http://127.0.0.1:4280` when using 'flyctl proxy'
$ export FLY_API_TOKEN=$(fly auth token)
```

For local development, you can see the token used by `flyctl` with `fly auth token`. You can also create a new auth token in the [personal access token section of the fly.io dashboard](https://fly.io/user/personal_access_tokens).

In order to access this API on a fly VM, make the token available as a secret:

```cmd
fly secrets set FLY_API_TOKEN=$(fly auth token)
```

A convenient way to set the `FLY_API_HOSTNAME` is to add it to your `Dockerfile`:

```dockerfile
ENV FLY_API_HOSTNAME="https://api.machines.dev"
```

<section class="warning">The cURL examples in this document are for an app named `my-app-name`. Replace `my-app-name` with the name of your app. Also replace any example Machine IDs with your app's Machine IDs.
</section>

## Authentication

All requests must include the Fly API Token in the HTTP Headers as follows:

```
Authorization: Bearer <fly_api_token>
```