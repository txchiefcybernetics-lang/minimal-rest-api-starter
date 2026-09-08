## Prerequisites

**Install Encore:**
- **macOS:** `brew install encoredev/tap/encore`
- **Linux:** `curl -L https://encore.dev/install.sh | bash`
- **Windows:** `iwr https://encore.dev/install.ps1 | iex`

## Create app

Create a local app for tradexpress.co from this template:

```bash
encore app create tradexpress-api --example=ts/hello-world
encore app create tradexpress-api --example=ts/hello-world

### Local Development Dashboard

While `encore run` is running, open [http://localhost:9400/](http://localhost:9400/) to access Encore's [local developer dashboard](https://encore.dev/docs/observability/dev-dash).

Here you can see traces for all requests that you made, see your architecture diagram (just a single service for this simple example), and view API documentation in the Service Catalog.

## Development

### Add a new service

To create a new microservice, add a file named encore.service.ts in a new directory.
The file should export a service definition by calling `new Service`, imported from `encore.dev/service`.

```ts
import { Service } from "encore.dev/service";

export default new Service("my-service");
```

Encore will now consider this directory and all its subdirectories as part of the service.

Learn more in the docs: https://encore.dev/docs/ts/primitives/services

### Add a new endpoint

Create a new `.ts` file in your new service directory and write a regular async function within it. Then to turn it into an API endpoint, use the `api` function from the `encore.dev/api` module. This function designates it as an API endpoint.

Learn more in the docs: https://encore.dev/docs/ts/primitives/defining-apis

### Service-to-service API calls

Calling API endpoints between services looks like regular function calls with Encore.ts.
The only thing you need to do is import the service you want to call from `~encore/clients` and then call its API endpoints like functions.

In the example below, we import the service `hello` and call the `ping` endpoint using a function call to `hello.ping`:

```ts
import { hello } from "~encore/clients"; // import 'hello' service

export const myOtherAPI = api({}, async (): Promise<void> => {
  const resp = await hello.ping({ name: "World" });
  console.log(resp.message); // "Hello World!"
});
```

Learn more in the docs: https://encore.dev/docs/ts/primitives/api-calls

### Add a database

To create a database, import `encore.dev/storage/sqldb` and call `new SQLDatabase`, assigning the result to a top-level variable. For example:

```ts
import { SQLDatabase } from "encore.dev/storage/sqldb";

// Create the todo database and assign it to the "db" variable
const db = new SQLDatabase("todo", {
  migrations: "./migrations",
});
```

Then create a directory `migrations` inside the service directory and add a migration file `0001_create_table.up.sql` to define the database schema. For example:

```sql
CREATE TABLE todo_item (
  id BIGSERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  done BOOLEAN NOT NULL DEFAULT false
  -- etc...
);
```

Once you've added a migration, restart your app with `encore run` to start up the database and apply the migration. Keep in mind that you need to have [Docker](https://docker.com) installed and running to start the database.

Learn more in the docs: https://encore.dev/docs/ts/primitives/databases

# TradeXpress (tradexpress.co) — Production API & Backend

Welcome to the official backend repository and API documentation for **tradexpress.co**. This application is built using TypeScript and powered by Encore.ts to handle fast, scalable, and type-safe microservices for customs brokerage, logistics estimations, and AI-driven trade consultations.

[![Deploy to Encore](https://github.com/encoredev/examples/raw/main/assets/deploytoenc.svg)](https://app.encore.cloud/create-app/clone/ts-hello-world)

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Project Setup & Configuration](#project-setup--configuration)
3. [Running Locally](#running-locally)
4. [Using the API](#using-the-api)
5. [Local Development Dashboard](#local-development-dashboard)
6. [Backend Architecture & Development](#backend-architecture--development)
   - [Adding a New Service](#add-a-new-service)
   - [Adding a New Endpoint](#add-a-new-endpoint)
   - [Service-to-Service API Calls](#service-to-service-api-calls)
   - [Adding a Database](#add-a-database)
7. [Advanced Encore.ts Features](#advanced-encorets-features)
8. [Deployment & Exposing tradexpress.co](#deployment--exposing-tradexpressco)
   - [Self-Hosting via Docker](#self-hosting)
   - [Encore Cloud Platform](#encore-cloud-platform)
9. [GitHub Integration](#link-to-github)
10. [Testing](#testing)

---

## Prerequisites

Before setting up the project for `tradexpress.co`, make sure you have the Encore CLI installed based on your operating system:

* **macOS:** `brew install encoredev/tap/encore`
* **Linux:** `curl -L https://encore.dev/install.sh | bash`
* **Windows:** `iwr https://encore.dev/install.ps1 | iex`

You will also need **Docker** installed and running locally if you plan on spinning up local databases and persistence layers.

---

## Project Setup & Configuration

Create a local app from this template specifically structured for `tradexpress.co`:

```bash
encore app create tradexpress-api --example=ts/hello-world
