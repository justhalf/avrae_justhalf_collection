### Homebrewing a Potion

You can make your own potion which the alias `!potion` can run for you (or to make it public so others can subscribe to
it!). To do that, you can list down your homebrewed potions in the uvar `potionHomebrew`.

The content of the uvar should be a JSON list, each containing either a gvar address or a potion object. A potion
object is a dictionary (JSON object) with the following keys:
* name -> string
* aliases -> list of string
* description -> string
* help -> string
* showHelp -> boolean
* cc -> list of dictionary containing argument to create_cc
* automation -> gvar address or automation object
* source -> string

An example of `potionHomebrew` content would be:
```
[
    "0990d563-9217-4faf-ac46-4f72dc9159b7",
    {
        "name": "Potion of Long-lasting Effect",
        "aliases": ["longlast", "long effect"],
        "description": "This potion effect last permanently, and increases every time you drink it, up to 10 times.",
        "help": "This alias creates a cc automatically to keep track the number of times you have drunk this.",
        "showHelp": false,
        "cc": [
            {
                "name": "Potion of Long-lasting Effect Uses",
                "minVal": 0,
                "maxVal": 10,
                "initial_value": 0,
                "reset": "none",
                "desc": "Keeps track of the number of Potion of Long-lasting Effect drunk."
            }
        ],
        "automation": "gvar-to-automation-object",
        "source": "HB"
    }
]
```

The first item is a gvar address, which points to a collection of potion objects like the one shown in the second item.
"showHelp" parameter controls whether the "help" is shown when the potion is drunk.

### Automation Object

The automation object used in the potion automation is something you can export from the [Avrae automation
dashboard](https://avrae.io/dashboard/characters), but with some restrictions:
* The root-level nodes can only be either of these three cases:
  * List of Branch nodes
    * Each __must__ have the condition as `choice in "a string"` or `choice == "a string"`
    * These Branch nodes are used to select which one to run when the potion is used with `-choice`
    * The special Branch node with the condition `choice == ""` is used when no `-choice` is provided
  * One iEffect node
  * One Target node
* Within the Target node or Branch node, only the following nodes are supported:
  * iEffect node
  * Damage node
  * Temp HP node
  * Saving Throw node
  * Text node
* Within iEffect node, the extra Action or extra Buttons granted have no restrictions
  * This is because the alias does not have to process these nodes
* No variables are allowed in the above nodes
  * One exception is the variable `{SB(N)}` where the value of `N` is a number, which will be processed as a Strength
    score change if it is the full text of a Passive Effect under iEffect
  * For example, in Potion of Hill Giant Strength, the to_hit_bonus and damage_bonus fields are filled as `{SB(21)}`

For a more practical help, take a look at the default implementations of the various potions in Basic Rules (2014) in
[this gvar](https://avrae.io/dashboard/gvars?lookup=0990d563-9217-4faf-ac46-4f72dc9159b7).
