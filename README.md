# Fisherman Command Loader

>Loads commands from files in a directory

## Set up

```bash
npm install --save command-loader-fisherman
```

Include the middleware to the bot:

```javascript
[...]
const CommandLoader = require('command-loader-fisherman')
var commandLoader = new CommandLoader(path.resolve(__dirname, 'commands')) //the  command dir
bot.use(commandLoader)
[...]
```

## Adding commands

As you can see, you initialize the CommandLoader with a directory, **use path.resolve() to get the full path directory** location (since `fs.stat()` is used).

Commands are loaded once you initialize middlewares with fisherman.

Each commands should be in a subfolder, named like the register name.
A `_register.json` as to be present in each register directory

Tree example (C = commands folder):

```bash
C:
├───core
│       help.js
│       _register.json
│
└───giveaways
        giveaway.js
        _register.json
```

Each javascript file inside will be loaded, which should contains a exported command object

### Javascript command file

You just have to export a command object, like this:

Example: help command (named `help.js`, in core dir)

```javascript
var helpMessageBuilt = null
module.exports = {
  name: 'help',
  execute: function (req, res) {
    if (helpMessageBuilt) {
      res.send('', { embed: helpMessageBuilt }).catch(console.log)
      return
    }
    var embed = { title: 'Bot help' }
    var description = ''
    var commands
    req.client.registers.forEach(function (register) {
      commands = ''
      register.forEach(function (value) {
        commands = commands + ' `' + value.name + '` '
      })
      description = description + '__' + register.name + ':__\n' + commands + '\n\n'
    })

    embed.description = description
    helpMessageBuilt = embed
    res.send('', { embed: helpMessageBuilt }).catch(console.log)
  },
  channelType: ['dm', 'text']

}
```

### _register.json file

This file contain register properties:

There are only 3 properties:

| Name        | Description         |
| ------------- |:-------------:|
| key     | Register key name (used in register map of fisherman) |
| name      | Register name     |
| description | Register description     |

*All those property have default value = `null`*