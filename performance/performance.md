# Performance

Tested in Windows 10 system with 
<br />deno 1.0.3 
<br />v8 8.4.300
<br />typescript 3.9.2

Powered by Apache JMeter (v5.3)
Each test performed with 10 threads during the 10 seconds

## Contents
- [Attain](#attain)
  - [Minimal test](#minimal-test)
  - [With logger and router](#with-logger-and-router)
  - [Multi middlewares](#multi-middlewares)
  - [Send File](#send-file)
- [Express](#express)
  - [Minimal test](#minimal-test-1)
  - [With logger and router](#with-logger-and-router-1)
  - [Multi middlewares](#multi-middlewares-1)
  - [Send File](#send-file-1)
- [Oak](#oak)
  - [Minimal test](#minimal-test-2)
  - [With logger and router](#with-logger-and-router-2)
  - [Multi middlewares](#multi-middlewares-2)
  - [Send File](#send-file-2)

## Attain

### minimal test

```ts
import { App } from "https://deno.land/x/attain/mod.ts";

const app = new App();

app.use((req, res) => {
  res.status(200).send("Hello world!");
});

app.listen({ port: 3500 });
```
![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/attain.png?raw=true "Attain performance")

### with logger and router

```ts
import { App, logger } from "https://deno.land/x/attain/mod.ts";

const app = new App();

app.use(logger);

app.use("/", (req, res) => {
  res.status(200).send("Hello world!");
});

app.listen({ port: 3500 });
```

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/attain-middle.png?raw=true "Attain performance")

### multi middlewares
five simple middlewares with logger and router

```ts
import { App, Request, Response } from "https://deno.land/x/attain/mod.ts";

const app = new App();

const hello = (req: Request, res: Response) => {
  console.log("hello")
};

app.use(logger);
app.use(hello, hello, hello, hello, hello);
app.use("/", (req, res) => {
  res.status(200).send("Hello world!");
});

app.listen({ port: 3500 });
```

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/attain-middlewithfive.png?raw=true "Attain performance")

### Send file

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/attain-filesend.png?raw=true "Attain performance")


## Express

### minimal test

```ts
const express = require('express')
const app = express()
const port = 5000

app.get((req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => console.log(`Example app listening at http://localhost:${port}`))
```

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/express.png?raw=true "Express performance")

### with logger and router

```ts
const express = require('express')
const app = express()
const morgan = require('morgan')
const port = 5000

app.use(morgan('tiny'));

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => console.log(`Example app listening at http://localhost:${port}`))
```

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/express-middle.png?raw=true "Express performance")


### multi middlewares
five simple middlewares with logger and router

```ts
const express = require('express')
const app = express()
const morgan = require('morgan')
const port = 5000

const hello = (req, res, next) => {
  console.log("hello")
  next();
}
app.use(morgan('tiny'));
app.use(hello, hello, hello, hello, hello)
app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => console.log(`Example app listening at http://localhost:${port}`))
```

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/express-middlewithfive.png?raw=true "Express performance")

### Send file

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/express-sendfile.png?raw=true "Express performance")


## Oak


### minimal test

```ts
import { Application } from "https://deno.land/x/oak/mod.ts";

const app = new Application();

app.use((ctx) => {
  ctx.response.body = "Hello World!";
});

await app.listen({ port: 8000 });
```

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/oak.png?raw=true "Oak performance")

### with logger and router

```ts
import { Application, Router } from "https://deno.land/x/oak/mod.ts";

const app = new Application();
const router = new Router();
router
  .get("/", (context) => {
    context.response.body = "Hello world!";
  })

app.use(async (ctx, next) => {
  await next();
  const rt = ctx.response.headers.get("X-Response-Time");
  console.log(`${ctx.request.method} ${ctx.request.url} - ${rt}`);
});
app.use(router.routes());
app.use(router.allowedMethods());


await app.listen({ port: 8000 });
```

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/oak-middle.png?raw=true "Oak performance")

### multi middlewares
five simple middlewares with logger and router

```ts
import { Application, Router } from "https://deno.land/x/oak/mod.ts";

const app = new Application();
const router = new Router();

const hello = (ctx: any, next: any) => {
  console.log("hello1");
  next()
}

router
  .get("/", (context) => {
    context.response.body = "Hello world!";
  })

app.use(hello, hello, hello, hello, hello)
app.use(async (ctx, next) => {
  await next();
  const rt = ctx.response.headers.get("X-Response-Time");
  console.log(`${ctx.request.method} ${ctx.request.url} - ${rt}`);
});
app.use(router.routes());
app.use(router.allowedMethods());


await app.listen({ port: 8000 });
```

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/oak-middlewithfive.png?raw=true "Oak performance")

### Send file

![alt text](https://github.com/aaronwlee/Attain/blob/master/performance/oak-filesend.png?raw=true "Oak performance")
