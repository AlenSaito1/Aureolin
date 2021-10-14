<div align=center>

# Aureolin
**Aureolin** is a simple, fast, and powerful REST API framework for Node.js with a focus on simplicity and performance.

[![NPM](https://img.shields.io/npm/l/aureolin?style=flat-square&label=License)](https://github.com/AlenSaito1/Aureolin/blob/master/LICENSE) [![Discord](https://img.shields.io/discord/898177582829285387?label=Discord&style=flat-square)](https://discord.gg/3Pg2Nw2vjn) [![CodeFactor](https://img.shields.io/codefactor/grade/github/alensaito1/aureolin?style=flat-square&label=Code%20Quality)](https://www.codefactor.io/repository/github/alensaito1/aureolin) [![NPM](https://img.shields.io/npm/dw/aureolin?style=flat-square&label=Downloads)](https://npmjs.com/package/aureolin)


</div>

----
### [Starter Project](https://github.com/AlenSaito1/Aureolin-starter)

## Installation

```sh
npm install aureolin
```

## Example

To get started with Aureolin, you need to call the `create` function exported by Aureolin.

The `create` function takes an options object as its first and only argument.


```TS
/** @filename main.ts */

import { create } from 'aureolin'
// import create from 'aureolin'
// const { create } from require('aureolin')

const main = async () => {
    const app = await create({
        port: 3000,
        root: __dirname
    })
}
```

Option Schema is as follows

```TS
interface CreateOptions {
    port: number // port to listen on
    root: string // root directory where providers, middleware, and controllers are located
    logger?: Logger // Pino Logger
}
```
### Controllers

To create a controller you need to import the `Controller` decorator from Aureolin. 

Decorators for all Http methods are provided

The return value of the methods prefixed with an Http Decorator in a controller are sent as the response
Response can be a string, number, object, array, or a Promise.

Use the provided Parameter Decorators to let Aureolin to know what parameters pass to the to the controller methods.

```TS
/** @filename controllers/hello.ts */
import { Controller, Context, Get, Ctx, Param } from 'aureolin'

@Controller('/hello')
export class HelloController {
    @Get('/')
    async hello() {
        return 'Hello World!'
    }

    @Get('/:name')
    async helloName(@Ctx() { params: { name } }: Context) {
        return `Hello ${name}!`
    }

    @Get('/:name/:age')
    async helloNameAge(@Param('name') name: string, @Param('age') age: string) {
        return `Hello ${name}! You are Probably ${age} years old.`
    }    
}
```

## Providers

Providers are a way to inject dependencies into your controllers.
Use the `provider` decorator to decalre a class as a provider.
Use the `inject` decorator to inject provider into your controllers.

`@Provider(provide: string)`
```TS
/** @filename providers/time.ts */
import { Provider } from 'aureolin'

@Provider('time')
export default class TimeProvider {
    async getTime() {
        return new Date().toLocaleTimeString()
    }
}
```
`Inject(provide: string)`
```TS
/** @filename controllers/time.ts */
import { Controller, Context, Get, Inject } from 'aureolin'
import type TimeProvider from 'providers/time.ts'

@Controller('/time')
export class TimeController {

    constructor(@Inject('time') private timeProvider: TimeProvider) {}

    @Get('/')
    async getTime() {
        return timeProvider.getTime()
    }
}
```

## Middlewares

Middlewares are functions that are called before the controller methods are called.

The example middleware shown below will log the method and path to the console every time a request is made.
First and only param of the `use` method is the context itslef (for now)
```TS
/** @filename middlewares/logger.ts */
import { Middleware, AureolinMiddleware } from 'aureolin'

@Middleware()
export class Logger implements AureolinMiddleware {
    async use(ctx: Context) {
        console.log(`${ctx.method} ${ctx.path}`)
    }
}
```

After you have created all the necessary controllers and middlewares and placed them in the correct directories, you can create a new application.

```TS
/** @filename main.ts */

import { create } from 'aureolin'

const main = async () => {
    const app = await create({
        port: 3000,
        root: __dirname
    })
}

main()
```

After you have created the application, Visit ```http://localhost:3000/hello``` to see the output of the `hello` controller.

---
Thank you for using Aureolin!














