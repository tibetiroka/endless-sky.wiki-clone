# Introduction #

A "conversation" is a series of text messages that progress differently depending on which replies the player selects, sort of like a "Choose Your Own Adventure" book. The goal of conversations is to add another "role playing" aspect to the game by allowing freedom in how the player interacts with other people.

Each "node" of the conversation displays some text, and then either jumps immediately to another node or presents the player with a list of responses to choose between.

```html
conversation (<name>)
    scene <image>
    label <name>
    <text>
        (<endpoint> | goto <label>)
    name
    choice
        <text>
            (<endpoint> | goto <label>)
        ...
    branch <if true> (<if false>)
        <conditions>
    apply
        <conditions>
    ...
```

# Endpoints and goto #

After any text message, or in response to any choice, the conversation may jump to a different, labeled point in the conversation, or to one of the "endpoints." Each endpoint causes the conversation to end, and also has other effects:

* `accept`: The player accepts this mission (if one is being offered).
* `launch`: The mission is accepted, _and_ the player immediately takes off from this planet.
* `decline`: The mission is declined. (This is also useful for creating conversations that appear when you land or enter a spaceport, but that are intended just to provide flavor, not to lead to a mission.)
* `flee`: The mission is declined, _and_ the player immediately takes off from this planet.
* `defer`: The mission is declined, but it will not be marked as "offered," so it can be offered again at a later date even if it is not a repeating mission.
* `die`: The player is killed.

For example:

```html
conversation
    `A Navy officer asks if you can do a job for him.`
    choice
        `    "Sure, I'd love to."`
            accept
        `    "Sorry, I'm on an urgent cargo mission."`
            decline
        `(Attack him.)`
            goto "bad idea"
    label "bad idea"
    `    You shout "Death to all tyrants!" and go for your gun.`
    `    Unfortunately, he pulls his own gun first.`
        die
```

The conversation stops as soon as an endpoint is encountered, so if you list multiple endpoints or gotos after a line of text, only the first one will be applied.

You can go to labels earlier on in the conversation if you want, but be careful that this does not create an "infinite loop."

# Scenes #

A conversation can contain a "scene" image at any point. This will generally be an image from images/scene/, but you can use other images as well, such as ship images or planet images. The image should be no more than 540 pixels wide.

![](https://raw.githubusercontent.com/endless-sky/endless-sky/master/images/scene/engine.jpg)

Try to avoid using very tall images for scenes, to avoid the need for scrolling on short screens.

# Labels #

A label marks the start of a "node" in a conversation: that is, a point that you can jump to from any other node via a "goto" command. The label can have any name you want, as long as the name is unique. As with all the game's data files, if the label name has spaces in it, you must enclose it in double quotation marks (") or backticks (`).

# Text #

Any line of the conversation that does not begin with one of the special keywords (label, name, choice, or scene) is an ordinary text node. In most cases, you will want to enclose the text in backticks so that you can use quotation marks inside it without confusing the parser:

```html
    `You tell the bartender, "The pirates said, 'Give us all your cargo!'"`
        goto "next"
```

Each line of text can be followed by a "goto" or an endpoint. If it does not, the conversation will just proceed to whatever comes next.

For the sake of indenting paragraphs correctly, every text line except for the first should begin with a single tab, inside the backticks.

# Name #

This is useful mostly just for the "intro" conversation: if the "name" keyword appears, a set of text-entry boxes are displayed asking the player to enter their first and last name. You could also use this, for example, if the player is going deep undercover and changing their name.

# Choice #

A choice allows the player to select from one or more possible responses. A choice with a single response might be useful if you want to break up the flow of text (e.g. because a lot is being displayed at once), but usually choices will present two or more options.

Each option is represented by a single line of text, which will generally be enclosed in backticks and start with a tab for proper indentation. As with ordinary text, any choice that does not have a "goto" or endpoint after it will just allow the conversation to continue to the next line below this choice.

# Branch #

A branch takes the conversation to one of two different labels depending on a set of conditions. The conditions are specified in the same format as when CreatingMissions.

The "branch" keyword is followed by one or two label names. The first is the label to jump to if the subsequent conditions are all true. The second is the one to jump to if any of the conditions are false. If no second label is supplied, the "false" branch simply continues to the next entry in the conversation.

```html
conversation
    branch famous
        "combat rating" > 500
    
    `    When you walk into the bar, a man at a table in the corner looks up, but does not recognize you.`
        decline
    
    label famous
    apply
        set "everyone thinks you are awesome"
        "drunk" += .085
    
    `    When you walk into the bar, a man at a table in the corner looks up and sees you.`
    `    He says, "It's Captain <last>, the famous warrior! Barkeep, give <first> a drink on my tab."`
        decline
```

# Apply #

An "apply" entry modifies conditions instead of testing to see what they are currently equal to. The syntax is the same as the "on complete" node in a [mission](CreatingMissions).