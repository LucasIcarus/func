## FUNC

More popular and simple way to build command-line tools.


## Feature

  - Small size package.
  
  - Elegant command support and params support.
  
  - Very few dependencies, run faster.
  
  - Excellent module design, not burden of thinking.
  
  - Full template support, developable at one command. (coming soon)

## Usage
`func` provide some annotations for create command line tools, it makes your code more semantic than ever, and easy to maintain.

### Install

  1. Install: `npm i func`.
  
  2. Note your `tsconfig.json` configuration:
    ```json
    "compilerOptions": {
      ...
      "experimentalDecorators": true,
      "emitDecoratorMetadata": true,
      ...
    }
    ```
  3. Quick start:
  
  ```ts
  import { Container, Command } from 'func'

  @Command({
    name: 'test',
  })
  export class Test {
    constructor() { console.log('ok') }
  }
  
  new Container([Init])
  ```

### Guide

#### Container
  **Entry to all modules, there must be one.**
  
  ```ts
  // app.ts
  import { Container, Command } from 'func'
  import { Create } from './cretae'
  import { Build } from './build'
  
  new Container([Create, Build])
  ```
  
#### Command (`<NAME> command`)
  **Implement a command.**  Class are triggered when a command is called.
  
  ```ts
  // ./cretae.ts
  // called by `<NAME> create` 
  import { Command } from 'func'
  
  @Command({
    name: 'create',
  })
  export class Create {
    constructor() { console.log('create trigger') }
  }
  ```
  
#### Option (`<NAME> --option`)
  **Implement a global option.**
  
  ```ts
  // ./option.ts
  // called by `<NAME> --help` 
  import { Option } from 'func'
  
  @Option({
    name: 'help',
  })
  export class Help {
  }
  ```
  
#### SubOptions (`<NAME> command --suboption`)
  **Add options based on a command.**
  
  ```ts
  // ./cretae.ts
  // called by `<NAME> create` or `<NAME> create --force` 
  import { Command, SubOptions } from 'func'
  
  @Command({
    name: 'create',
  })
  @SubOptions([{ name: 'force' }])
  export class Create {
  }
  ```
  
#### Major (`<NAME>`)
  **Trigger without Command and Option.**
  
  ```ts
  // ./main.ts
  // called by `<NAME>`
  import { CommandMajor } from 'func'
  
  @CommandMajor()
  export class Main {
  }
  ```

#### NotFound (`<NAME> <ANY>`)
  **Trigger when no commands are found**
  
  ```ts
  // ./not-found.ts
  import { CommandNotFound } from 'func'
  
  @CommandNotFound()
  export class NotFound {
    constructor() { console.log('not found any commands!') }
  }
  ```

#### Command Arguments
  **Provide arguments in commands**
  
  ```ts
  // called by `<NAME> create --force` 
  import { Command, CommandArgsProvider } from 'func'
  
  @Command({ name: 'create' })
  @SubOptions([{ name: 'force' }])
  export class Create {
    constructor(
      private arg: CommandArgsProvider,
    ) {
      console.log(arg.inputs)     // ['name', 'create']
      console.log(arg.option)     // { force: true }
      console.log(arg.native)     // original parse infos
    }
  }
  ```


#### Option Arguments
  **Provide arguments in options**
  
  ```ts
  // called by `<NAME> --project my_project` 
  import { Option, OptionArgsProvider } from 'func'
  
  @Option({ name: 'project', type: String })
  export class Project {
    constructor(
      private arg: OptionArgsProvider,
    ) {
      console.log(arg.project)      // 'my_project'
    }
  }
  ```
 
#### Register Arguments
  **Provide all registers in any class.**
  
  ```ts
  import { Option, RegisterProvider } from 'func'
  
  @Option({ name: 'help' })
  export class MyHelp {
    constructor(
      private arg: RegisterProvider,
    ) {
      console.log(arg.commands)         // all commmands
      console.log(arg.options)         // all options
    }
  }
  ```
 
## Decorators Reference
### Commands:

| Signature | Param structure | Description |
|----|----|----|
| @Command(params: CommandParams) | CommandParams = { name: string, description?: string } | create a command |
| @Option(params: OptionParams) | OptionParams = { name: string, type?: OptionType, description?: string, alias?: string } | create an option |
| @SubOptions(params: Array<OptionParams>) | ditto | create a suboption, usually used after `Command` |
| @CommandNotFound() | - | create a method to capture undeclared commands |
| @CommandMajor() | - | major command |
  
### Arguments Type

| Signature | Description |
|----|----|
| CommandArgsProvider | provide infos about the current command |
| OptionArgsProvider | provide infos about the current option |
| RegisterProvider | provide all registered metadata |
  
## Thanks
Thanks to [Shannon Moeller](https://github.com/shannonmoeller) for donating the pkgname "func" on npm!

## LICENSE
[MIT](./LICENSE)
