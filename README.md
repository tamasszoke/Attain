# Attain

A middleware web framework for Deno's using [http](https://github.com/denoland/deno_std/tree/master/http#http) server. <br />
This middleware framework is inspired by [express](https://github.com/expressjs/express)


## Start

```ts
import { App } from "https://deno.land/x/attain/mod.ts";

const app = new App();

app.use((req, res) => {
  res.status(200).send({status: "Good"});
});

app.listen({ port: 3500 });

console.log("http://localhost:3500");
```

The middleware process the function step by step based on registered order.  

```ts
import { App } from "https://deno.land/x/attain/mod.ts";

const app = new App();

app.use((req, res) => {
  console.log("First step");
});

app.use((req, res) => {
  console.log("Second step");
});

// last step
app.use((req, res) => {
  res.status(200).send({status: "Good"});
});

app.use((req, res) => {
  console.log("Will not executed");
});

app.listen({ port: 3500 });

console.log("http://localhost:3500");
```

## Nested Routing

> **Path** - router.ts

 __warn__: <span style="color: red;">async await</span> will block your procedures.

```ts
import { Router } from "https://deno.land/x/attain/mod.ts";

const api = new Router();
// or
// const api = new App();

const sleep = (time: number) => 
  new Promise((resolve) => setTimeout(() => resolve(), time));

api.get("/hello", async (req, res) => {
  console.log("here '/hello'");
  await sleep(1000);
  res.status(200).send(`
  <!doctype html>
  <html lang="en">
    <body>
      <h1>Hello</h1>
    </body>
  </html>
  `);
});

export default api;

```

> **Path** - main.ts

```ts
import { App } from "https://deno.land/x/attain/mod.ts";
import api from "./router.ts";

const app = new App();

// nested router applied
app.use("/api", api)

app.use((req, res) => {
  res.status(404).send("page not found");
});

app.listen({ port: 3500 });

console.log("http://localhost:3500");
```

```
# start with: deno -A ./main.ts
```


---

There are several modules that are directly adapted from other modules. 
They have preserved their individual licenses and copyrights. All of the modules,
including those directly adapted are licensed under the MIT License.

All additional work is copyright 2018 - 2020 the Attain authors. All rights reserved.