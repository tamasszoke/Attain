# Attain - [Deno](https://deno.land/)

A middleware web framework for Deno's using [http](https://github.com/denoland/deno_std/tree/master/http#http) server. <br />
This middleware framework is inspired by [express](https://github.com/expressjs/express)

Download and use
```js
import { App, Router, Request, Response } from "https://raw.githubusercontent.com/aaronwlee/Attain/master/mod.ts";
// or
import { App, Router, Request, Response } from "https://deno.land/x/attain/mod.ts";
```


## Start

```ts
import { App, Request, Response } from "https://deno.land/x/attain/mod.ts";

const app = new App();

const sampleMiddleware = (req: Request, res: Response) => {
  console.log("before send")
};

app.use("/:id", (req, res) => {
  console.log(req.params);
  res.status(200).send(`id: ${req.params.id}`);
})

app.use(sampleMiddleware, (req, res) => {
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
}, (req, res) => {
  console.log("Second step");
});

app.use((req, res) => {
  console.log("Third step");
});

// last step
app.use((req, res) => {
  console.log("Last step");
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

const sleep = (time: number) => {
  new Promise((resolve) => setTimeout(() => resolve(), time));
};

// It will stop here for 1 second.
api.get("/block", async (req, res) => {
  console.log("here '/block'");
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

// It will not stop here
api.get("/nonblock", (req, res) => {
  console.log("here '/nonblock'");
  sleep(1000).then(_ => {
      res.status(200).send(`
      <!doctype html>
      <html lang="en">
        <body>
          <h1>Hello</h1>
        </body>
      </html>
      `);
  });
})

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
# start with: deno run -A ./main.ts
```

## Extra plugins
 - __logger__ : `logging "response - method - status - path - time"`
 - __parser__ : `parsing the request body and save it to request.params`
```ts
import { App } from "https://deno.land/x/attain/mod.ts";
import logger from "https://deno.land/x/attain/plugins/logger.ts";
import parser from "https://deno.land/x/attain/plugins/json-parser.ts";

const app = new App();

// logging response method status path time
app.use(logger);

// parsing the request body and save it to request.params
app.use(parser);

app.use("/", (req, res) => {
  res.status(200).send("hello");
});

app.use("/:id", (req, res) => {
  console.log(req.params);
  res.status(200).send(`id: ${req.params.id}`);
})

app.post("/submit", (req, res) => {
  console.log(req.params);
  res.status(200).send({ data: "has received" });
});

app.use((req, res) => {
  res.status(404).send("page not found");
});

app.listen({ port: 4000 });
console.log("Start listening on http://localhost:4000");

```

---

There are several modules that are directly adapted from other modules. 
They have preserved their individual licenses and copyrights. All of the modules,
including those directly adapted are licensed under the MIT License.

All additional work is copyright 2018 - 2020 the Attain authors. All rights reserved.