# Jovo Language Model

In this section, you will learn more about the Jovo Language Model, found in the `/models` folder of your project. It can be used to created platform specific language models with the [Jovo CLI](../../02_cli './cli').

* [Introduction](#introduction)
* [Language Model Elements](#language-model-elements)
    * [Invocation](#invocation)
    * [Intents](#intents)
        * [Intent Name](#intent-name)
        * [Phrases](#phrases)
        * [Inputs](#inputs)
    * [Input Types](#input-types)
        * [Input Type Name](#input-type-name)
        * [Values](#values)
        * [Synonyms](#synonyms)
* [Platform Specific Elements](#platform-specific-elements)
  * [Alexa](#alexa)
  * [Dialogflow](#dialogflow)

## Introduction
The Jovo Language Model allows you to maintain only a single file, which can be used to create the platform language models with the help of the [`Jovo CLI`](../../02_cli './cli'). 

You can find the language model files in the `/models` folder of your Jovo project:

![Models Folder in a Jovo Project](../../img/folder-structure-models.png "Models Folder in a Jovo Project" )

For the platform specific `nlu` (natural language understanding), we currently support built-in `alexa` for Alexa Skills, and `dialogflow` for Google Actions. These are referenced in the `app.json` file after the platforms are initialized with [`jovo init <platform>`](../../02_cli#jovo-init './cli#jovo-init'). To learn more about how the resulting platform models look like, please read [App Configuration > Models > Platforms](./platforms './model/platforms').



Every language you choose to support will have its very own language model (`en-US`, `de-DE`, etc.). Overall the Jovo Language Model is similar to the Alexa interaction model with some small changes here and there.

For example, the `en-US.json` in the [Jovo Sample Voice App](https://github.com/jovotech/jovo-sample-voice-app-nodejs) looks like this:

```javascript
{  
    "invocation": "my test app",
    "intents":[  
        {  
            "name": "HelloWorldIntent",
            "phrases": [  
                "hello",
                "say hello",
                "say hello world"
            ]
        },
        {  
            "name": "MyNameIsIntent",
            "phrases": [  
                "{name}",
                "my name is {name}",
                "i am {name}",
                "you can call me {name}"
            ],
            "inputs": [  
                {  
                    "name": "name",
                    "type": {  
                        "alexa": "AMAZON.US_FIRST_NAME",
                        "dialogflow": "@sys.given-name"
                    }
                }
            ]
        }
    ],
    "alexa": {  
        "interactionModel": {  
            "languageModel": {  
                "intents": [  
                    {  
                        "name": "AMAZON.CancelIntent",
                        "samples": [  

                        ]
                    },
                    {  
                        "name": "AMAZON.HelpIntent",
                        "samples": [  

                        ]
                    },
                    {  
                        "name": "AMAZON.StopIntent",
                        "samples": [  

                        ]
                    }
                ]
            }
        }
    }
}
```
Let's go through the specific elements in detail.

## Language Model Elements

The Jovo Language Model consists of several elements, which we will go through step by step in this section:

* [Invocation](#invocation)
* [Intents](#intents)
    * [Intent Name](#intent-name)
    * [Phrases](#phrases)
    * [Inputs](#inputs)
* [Input Types](#input-types)
    * [Input Type Name](#input-type-name)
    * [Values](#values)
    * [Synonyms](#synonyms)

For platform specific language model elements, take a look at the sections below:

* [Platform Specific Elements](#platform-specific-elements)
    * [Alexa](#alexa)
    * [Dialogflow](#dialogflow)

### Invocation

The `invocation` is the first element of the Jovo Language Model. It sets the invocation name of your voice application (the one people are using to talk to your voice app, see [Getting Started > Voice App Basics](../../01_getting-started/voice-app-basics.md './voice-app-basics') for more information).

```javascript
"invocation": "my test app",
```

Please note: The `invocation` element is currently only exported to Alexa Skills. Invocation names for Google Actions have to be set in the Actions on Google developer console.


### Intents

Intents can be added to the JSON as objects that include a `name`, sample `phrases`, and `inputs` (optional):

```javascript
{  
    "name": "MyNameIsIntent",
    "phrases": [  
        "{name}",
        "my name is {name}",
        "i am {name}",
        "you can call me {name}"
    ],
    "inputs": [  
        {  
            "name": "name",
            "type": {  
                "alexa": "AMAZON.US_FIRST_NAME",
                "dialogflow": "@sys.given-name"
            }
        }
    ]
}
```

#### Intent Name

The `name` specifies how the intent is called on the platforms. We recommend using a consistent standard. In our examples, we `Intent` to each name, like `MyNameIsIntent`.

#### Phrases

This is an array of example sentences, or, `phrases`, which will be used to train the language model on Alexa and Dialogflow. This is the equivalent to utterances or "user says" on the respective developer platforms.

#### Inputs
While defining your `inputs` (slots on Alexa and entities in Dialogflow) you can choose to either provide seperate input types for each platform, or define your own input type:

```javascript
"inputs": [
    {
        "name": "name",
        "type": {
            // Use built in slots/entities from each platform
            "alexa": "AMAZON.US_FIRST_NAME", 
            "dialogflow": "@sys.given-name"
        }
    },
    {
        "name": "city",
        // Use your own input type
        "type": "myCityInputType"
    }
]
```
In the upper part of the example above, for the `name` input, we distinguish between input types for `alexa` and `dialogflow`. Learn more about their built-in input types here:
* Amazon Alexa: [Slot Type Reference](https://developer.amazon.com/docs/custom-skills/slot-type-reference.html)
* Dialogflow: [System Entities](https://dialogflow.com/docs/reference/system-entities)

In the lower part, we reference a new input type called `myCityInputType`, which we need to define outside the `intents` array of the overall model.

### Input Types

The `inputTypes` array is the place where you can define your own input types and provide a `name`, `values`, and `synonyms` (optional).

```javascript
"inputTypes": [
    {
        "name": "myCityInputType",
        "values": [
            {
                "value": "Berlin"
            },
            {
                "value": "New York",
                "synonyms": [
                    "New York City"
                ]
            }
        ]
    }
],
```

#### Input Type Name

The `name` specifies how the input type is referenced. Again, we recommend to use a consistent style throughout all input types to keep it organized.

#### Values

This is an array of elements that each contain a `value` and optionally `synonyms`. With the values, you can define which inputs you're expecting from the user.

#### Synonyms

Sometimes different words have the same meaning. In the example above, we have a main value `New York` and a synonym `New York City`. 

To learn more about how these input values and synonyms can be accessed, take a look at [App Logic > Data](../../04_app-logic/02_data './data').


## Platform Specific Elements

If you only want to use certain features for one of the platforms, you can also add objects for their natural language understanding tools (`nlu`) to the model.

For Alexa Skills, Jovo currently supports the built-in NLU (natural language understanding) [`alexa`](#alexa), while for Google Assistant, [`dialogflow`](#dialogflow) is supported.

### Alexa

Some of the features Alexa provides have to be implemented separately in the `alexa` nlu section. 

Here are some examples:
* Built-in intents and slots (the ones with `AMAZON.` prepended to their names)
* Other Alexa-specific features like the Dialog Interface

This is how it looks like:

```javascript
"alexa": {
    "interactionModel": {
        "languageModel": {
            "intents": [
                {
                    "name": "AMAZON.CancelIntent",
                    "samples": []
                },
                {
                    "name": "AMAZON.HelpIntent",
                    "samples": []
                },
                {
                    "name": "AMAZON.StopIntent",
                    "samples": []
                }
            ]
        }
    }
}
```

If you don't have this object in your language model, the [`jovo init`](../../02_cli/#jovo-init './cli#jovo-init') command will automatically append it with all the built-in intents required by Amazon.

The `alexa` object contains the `interactionModel` in its original syntax. For example, you can go to the Code Editor in the Skill Builder (beta) and copy-paste the stuff that you need into this part of the Jovo Language Model file.

### Dialogflow

There are two ways you can add `dialogflow` specific elements:
* Add options to Jovo Language Model intents
* Add intents and entities to `dialogflow` element

You can add options to Jovo intents like this:
```javascript
{  
    "name":"HelloWorldIntent",
    "phrases":[  
        "hello",
        "say hello",
        "say hello world"
    ],
    "dialogflow": {
        "priority": 1,
        "webhookForSlotFilling": true
    }
},
```
In the above example, you can see that you can add specific elements like a `priority` to an intent.

Similar to the [`alexa`](#alexa) element, you can also add `dialogflow` specific intents and entities to the language model. 

```javascript
 "dialogflow": {
    "intents": [
        {
            "name": "Default Fallback Intent",
            "auto": true,
            "webhookUsed": true,
            "fallbackIntent": true
        },
        {
            "name": "Default Welcome Intent",
            "auto": true,
            "webhookUsed": true,
            "events": [
                {
                    "name": "WELCOME"
                }
            ]
        }
    ],
    "entities": [
        {
            "name": "hobby",
            "isOverridable": true,
            "isEnum": false,
            "automatedExpansion": false
        }
    ]
}
```

If you don't have this object in your language model, the [`jovo init`](../../02_cli/#jovo-init './cli#jovo-init') command will automatically append it with all the intents required by Dialogflow.

The `dialogflow` object contains the agent data in its original syntax. For example, you export your Dialoglow Agent, look at the filex, and copy-paste the stuff that you need into this part of the Jovo Language Model file.


<!--[metadata]: {"title": "Jovo Language Model", 
                "description": " Learn more about the Jovo Language Model, a consolidated model for your Alexa Skill and Google Action.",
                "activeSections": ["configuration", "model", "model_index"],
                "expandedSections": "configuration",
                "inSections": "configuration",
                "breadCrumbs": {"Docs": "docs/",
				"App Configuration": "docs/configuration",
                                "Model": ""
                                },
		"commentsID": "framework/docs/model",
		"route": "docs/model"
                }-->
