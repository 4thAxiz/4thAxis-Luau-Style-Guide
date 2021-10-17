# 4thAxis-Luau-Style-Guide
For anyone who is trying to improve their readability and consistency or just want enlightenment.

---
title: '4thAxis Style Guide'
disqus: hackmd
---

4thAxis' Luau Style Guide
===



Introduction and Overview
---

First and foremost before we get started please remember-- **this is not the official  style guide of Lua or Luau**.

The purpose of following a style guide is to keep consistent and clean code. You may choose to follow or take inspiration or if you're not here to read about style practices, you may simply read this for enlightenment. This guide assumes you have a general understanding of Luau. This guide also goes into depth into why you should follow such practices. The goal of this style guide is to promote styles that respect the modified LSP of Lua 5.1 external Luau-like analysis that are also in regards to developer ergonomics. This guide values styles that promote readability and in a language that is being constantly optimized-- the era of focusing on performance over readability is slowly coming to an end. 

*This guide at the moment does not support static type checking.*
# Table of Contents
[TOC]
- [4thAxis-Luau-Style-Guide](#4thaxis-luau-style-guide)
- [4thAxis' Luau Style Guide](#4thaxis--luau-style-guide)
  * [Introduction and Overview](#introduction-and-overview)
- [Table of Contents](#table-of-contents)
- [Comments](#comments)
  * [Writing comments](#writing-comments)
  * [When and where](#when-and-where)
- [Variables](#variables)
  * [Scope Bounding](#scope-bounding)
    + [Localization](#localization)
    + [Under The Hood, Special Instructions](#under-the-hood--special-instructions)
  * [Naming Convention](#naming-convention)
    + [Diversity](#diversity)
    + [The Mono-Belief](#the-mono-belief)
  * [Arrangement](#arrangement)
    + [An Ergonomically-driven Style](#an-ergonomically-driven-style)
    + [Modules](#modules)
    + [Services](#services)
    + [Assets](#assets)
    + [Modifiers](#modifiers)
    + [Run-time Variables](#run-time-variables)
    + [Tables](#tables)
    + [Functions](#functions)
    + [Format](#format)
  * [Variable Alignment](#variable-alignment)
  * [Assessment](#assessment)
    + [Idiomatic Evaluation](#idiomatic-evaluation)
    + [Internal semantics, who wins?](#internal-semantics--who-wins-)
  * [Dummy Variables convention](#dummy-variables-convention)
- [Syntax](#syntax)
  * [Literals](#literals)
  * [Function call literals](#function-call-literals)
    + [Reasonability](#reasonability)
    + [Only-exception](#only-exception)
    + [Abstract Syntax Tree Accounts](#abstract-syntax-tree-accounts)
  * [Whitespacing](#whitespacing)
  * [Punctuation](#punctuation)
- [Expressions](#expressions)
  * [Conditional Expressions](#conditional-expressions)
    + [The Anti-pattern](#the-anti-pattern)
  * [Conditional Branching](#conditional-branching)
  * [Boolean Semantics](#boolean-semantics)
    + [Null-Coalescing and Ternary Optimization](#null-coalescing-and-ternary-optimization)
    + [Ternary Semantics Syntax](#ternary-semantics-syntax)
  * [Parentheses Rule](#parentheses-rule)
  * [Precedence (TODO)](#precedence--todo-)
- [Tables](#tables-1)
  * [Structuring](#structuring)
    + [Unoccupied Tables](#unoccupied-tables)
  * [Indexing](#indexing)
    + [Indexing Optimization](#indexing-optimization)
  * [Abstraction](#abstraction)
- [Functions](#functions-1)
  * [Structure](#structure)
  * [Monadic, Dyadic, Triad forms (TODO)](#monadic--dyadic--triad-forms--todo-)
  * [Parameters](#parameters)
- [Error Handling](#error-handling)
  * [Dynamic checks](#dynamic-checks)
  * [Abstract erroring](#abstract-erroring)
  * [Native vs Luau Consistency](#native-vs-luau-consistency)
- [Metadata](#metadata)
  * [Format and Structure](#format-and-structure)
- [Object-oriented](#object-oriented)
  * [Meta-Mechanism](#meta-mechanism)
  * [Classes](#classes)
  * [Constructor](#constructor)
  * [Private Fields](#private-fields)
  * [Format and Structure](#format-and-structure-1)
- [References](#references)
          + [Tags: `Luau`, `Lua`, `StyleGuide`](#tags---luau----lua----styleguide-)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>


# Comments

## Writing comments

Single line comments are to have spaces after the double hyphen like so:

```lua
-- Line Comment here
```

Block/body comments are supposed to be formatted like so:

```lua
--[[
    Two dashes with two square brackets and enter.
]]
```

## When and where

Comments should be kept minimum, provide comments only when needed. For example describing a series of semantics used to achieve something and when your code doesn’t express those semantics clearly is a good example when to provide comments. Otherwise you shouldn’t. Providing more comments makes code much more harder to read. Though this seems like a very contradicting statement-- it’s true. What takes more longer to read, a 5 paragraph essay or a 1 paragraph essay? Many famous authors like Robert C. Martin have mentioned this. In Short, if you find yourself practicing amplification on comments, you’re doing something wrong. [3]

Code should be self-documenting, however, open-source APIs follow different rules. You should be documenting your APIs well with comments to further abstract your code. The user shouldn’t have to read your API code to figure out how to use it.

Comments should always be placed inside scopes. Most code editors and IDEs will close out scopes and you can use this to your advantage to hide comments.

# Variables

## Scope Bounding

### Localization
Always scope your variables locally. Not only are the usage of global variables in Luau very slow but much more confusing. There are times where you can get reassignment confused with the declaration of a global. You’ll also end up using more resources and because of this, you’ll have to come up with silly variable names like: Jump2 or NewJump because the previous variables couldn’t be garbage collected. In short, there is never a real reason to have a global-- avoid this at all times.

### Under The Hood, Special Instructions
Unlike globals, local variables don't require any special instructions. From an internal-view-- Luau (at the time this guide was written) generates the same instructions as native Lua 5.1, *SETGLOBAL* for defining a global and *GETGLOBAL* for fetching a global. Globals are stored on the environment and fetching these takes much longer. Globals are fetched with a dictionary look-up on the environment while locals only require to be indexed from an array stored on the stack in some register. Globals are also never garbage collected. This also at the time of this writing conflict with parallel Luau as the environment is only isolated to the VM it's running in, therefore this will break dependencies unless Luau adds some secure shared storage between multiple VMs that still follow the principles of VM isolation for Luau sandboxing. 

## Naming Convention

### Diversity
This style guide unlike most guidelines doesn't follow multiple casing. You should use only **one** casing style-- preferably *PascalCase*-- and for everything. The idea of having specific casings for specific variables is to denote a certain variable type (like an argument), however, this has problems. 

The problem with different casing patterns:

1. More diversity in casing will make code look inconsistent.
2. The programmer has to continuously reference back to the casing guide for certain variables.
3. The reader would also find this confusing if the casing guide wasn't linked and if it was, the reader would also have to continuously reference the guide for context.

### The Mono-Belief
A simple solution is naming your variables correctly in the first place-- give proper meaning to your variables instead of relying on specific casings for variables. Using *camelCase* in Luau is useless and I find this especially more inconsistent (no disrespect to those who use this casing). The casing is understandable for object-oriented programming in other languages but Luau doesn't implement built-in classes or an inheritance system (at the time this guide was written).

## Arrangement

### An Ergonomically-driven Style
The arrangement not only follows a near universal standard of variable arrangement but goes into depth promoting developer ergonomic benefits to help you write better and more readable code.
### Modules

Ideally, you should always require modules first since standardly in most programming languages you always import/load/require modules first.

### Services
After modules in a new section, you fetch services. Sometimes services may have to be declared before modules. **Always use GetService()**.

### Assets
After services in a new section, you fetch and store assets using *WaitForChild*.

### Modifiers
After assets in a new section, you store constants and setting variables that are meant to modify your script

### Run-time Variables

After settings in a new section you store variables that your code will use and modify at run-time.

### Tables
Tables are meant to go last, after regular variables-- all in the same section or if needed, a new section

### Functions
After your variables you have your functions; arranged from auxiliary functions that are least commonly called to most commonly. Right after your auxiliary should come your main functions.  

### Format
Here is an example of how variables should be formatted (Commenting out sections are optional, I did this for a clearer visual representation):


```lua 

-- Modules
local Module = require(:WaitForChild("Module"))
local Module2 = require(:WaitForChild("Module2"))
local Module3 = require(:WaitForChild("Module3"))
 
-- Service
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local ServerScriptService = game:GetService("ServerScriptService")
local RunService = game:GetService("RunService")

-- Assets 
local Effect = :WaitForChild("Effect")
local Shockwave = :WaitForChild("Shockwave")
local Particle = :WaitForChild("Particle")

-- Settings
local PartSpawningSpeed = 100
local PartSize = 1
local WindSpeed = 20
 
-- Variables
local Name = "Steve"
local CurrentItem = "Sword"
local Table = { "Harris, Thomas, Kevin" }
local TemporaryTable = {} -- { "Billy, Newton" }

-- Auxiliary
local function ResetPlayer()
    ...
end


local function ConvertTo...()
    return ...
end

-- Main functions
local function FireballHandler()

end

...

```

## Variable Alignment 

Vertical alignment and both regular alignments are completely fine. Personally I find vertical alignment more pleasing to read, especially when you have a lot of defined variables. Most IDEs have shortcuts to automatically vertically align your code and this style is totally acceptable by Luau’s flexible lexer and linter. Although you will have to stick to one alignment style for consistency. 

## Assessment

### Idiomatic Evaluation
When assessing variables you should do this in an idiomatic matter. Use the not operator instead of *~= nil/ ~= false*. Only use *~= nil* when you strictly have to check if the variable is *nil*. Do i*f Exist then* instead of *if Exist == true then*.

Good:
```lua
if Running then

end

if not Running then

end
```
Bad:

```lua 
if Running == true then

end

if Running == false then

end
```
When necessary:
```lua
if Existance == nil then

end
```
### Internal semantics, who wins?
Internally, the idiomatic convention generates a different bytecode compared to the strict imperative evaluation: Luau (at the time of this writing) still uses native Lua 5.1’s instruction for handling these types of semantics. The instruction: *TEST* for the mentioned assessment which proves to be faster than the instruction for the non-idiomatic evaluation: *EQ*. Lua’s VM will execute the *TEST* instruction in conjunction with the *JMP* instruction to implement short-circuit LISP-style logical operators that retain and propagate operand values instead of booleans. Though the performance differences between the latter is quite miniscule, this settles arguments for those who argue over what’s the faster method.

>Source: https://www.lua.org/source/5.1/lopcodes.c.html
>Source: https://www.lua.org/source/5.1/lvm.c.html
## Dummy Variables convention
This style guide follows the standard convention for dummy variables like so:

```lua
local _ = ...
```

# Syntax
Stay **consistent** with syntax and follow the rules!

## Literals
When declaring a string literal, use double quotes: *" "*

## Function call literals

### Reasonability
For consistency reasons, you should always use the syntax: *Call(...)* to call a function no matter the micro-performance boost. In some cases for readability purposes it is reasonable to use the syntax: *Call{...}*. Here's a good example of implementing a *table call expression*:

```lua
Dialogues.Bob = {}; Dialogues.Bob.Dialogues = {

	DogDialogue = Dialogue.new("Find my dog."): Choices {
		
		["Yes"] = Dialogue.new("Thanks for accepting!").DialogueEvents:AssignQuestTo("4thAxis", "Lost Dog"),
		["No"] = Dialogue.new("Aww...").DialogueEvents:RemoveQuestFrom("4thAxis", "Lost Dog"),

		["Here you are!"] = Dialogue.new("Thanks so much!").DialogueEvents:GiveRewards()
	}
}

```

### Only-exception
Unfortunately, the only exception besides the default call-statement was the table-call-- this means that the *string-call expression* is not favored by the style guide. For one, the syntax is not appealing cognitively and two, not many people use the syntax which brings inconsistencies.  

### Abstract Syntax Tree Accounts
The aforementioned micro-performance benefits are only the result of a more compact AST production. The bytecode generated will be the same however the tokens are being assembled more differently into an abstract syntax tree. The difference is that the argument node doesn’t build sub-nodes for more arguments-- instead it re-uses the same argument node to place the literal argument set. Remember native Lua does not have a AST generation phase as it uses a one-pass compiler in contrast to Luau, however, there are AST inspection tools that you can use. It seems this optimization is applied by those ASTs inspectors commonly so it could be something that Luau would apply which would explain for the almost unnoticeable speed differences. Here is the AST format below:

<!-- "Remember, native Lua does not have an AST generation phase as it uses a one-pass compiler in contrast to Luau, however, there are AST inspection tools that you can use. It seems this optimization is commonly applied by those ASTs tools, commonly so it could be something that Luau would apply which would explain for the almost unnoticeable speed differences." -->

```lua 
local function Hello(TableArgument) end

Hello({})

Hello{}
```
Generates the following AST (https://astexplorer.net/):
```
- Chunk {
    type: "Chunk"
    - body: [
        + FunctionDeclaration {} -- Hello function
        
        - CallStatement {
            type: "CallStatement"
            
            - expression: CallExpression {
                type: "CallExpression"
                + Base: Identifier {}
                - arguments: [ -- Argument node 
                    - TableConstructorExpression { -- sub-nodes for arguments passed, +1 for each argument.
                        type: TableConstructorExpression
                        fields: [ ]
                        + range
                    }
                ]
            }
        }
        
         - CallStatement {
            type: "CallStatement"
            
            - expression: TableCallExpression {
                type: "TableCallExpression"
                + Base: Identifier {}
                - arguments: TableConstructorExpression [ -- re-use argument node here and not build any sub-nodes.
                
                    type: TableConstructorExpression
                    fields: [ ]
                    + range
                    
                ]
            }
        }
    ]
}

```

The benefit is that during the sementical phase of compilation, we only traverse less nodes. 

## Whitespacing

You should be indenting code standardly or stylistically (which will be discussed later on) for each scope. Leave whitespaces to section off code. Here’s a good example of separating stuff with whitespacing:

```lua

local function Dialogue(Player, Npc)
	local LastDialogue = Player.LastCompletedDialogues.Of(Npc)
	
	if LastDialogue then
		return ( next(Npc.Dialogues, LastDialogue) or "Thanks for all your help!" ) 
	else
		return Npc.Dialogue1
	end
end


Bob.Interact.PromptTriggered:Connect(function(_, Player)
	Bob.Prompt( Dialoge(Player, Bob) )
	
	local Connection; Connection = Player.QuestAccepted:Connect(function(Quest)
		Player.CurrentQuest = Quest; Connection = nil
	end)
end)

-- Example 2

function OrbitSystem:_Orbit()
    local Orbiters = self.Orbiters
    
    local OrbiterName, CurrentOrbiter, OrbiterAngle, OrbiterRadius, OrbitSpeed, OrbitClockwise;
    local PreviousOrbiter
    
    RunService.Stepped:Connect(function()       
        if self.Pause then return end
        
        OrbiterName = next(Orbiters, PreviousOrbiter) or next(Orbiters); CurrentOrbiter = Orbiters[OrbiterName] do 
 
            OrbiterRadius    = CurrentOrbiter.Radius
            OrbitSpeed       = CurrentOrbiter.OrbitSpeed
            OrbitClockwise   = CurrentOrbiter.Clockwise
            OrbiterAngle     = CurrentOrbiter.Angle 
        end
        
 
        CurrentOrbiter.Object.Position = Vector3.new (
            
            self.Center.Position.X + math.cos(OrbiterAngle) * CurrentOrbiter.Radius, 
            0, 
            self.Center.Position.Z + math.sin(OrbiterAngle) * CurrentOrbiter.Radius
        )
        
        CurrentOrbiter.Angle += ( OrbitClockwise and OrbitSpeed ) or -(OrbitSpeed)
        PreviousOrbiter = OrbiterName
        
    end)
end
```

## Punctuation 
Don’t use semicolons for every statement to show a line has ended-- the lexer is flexible enough to generate tokens and lexemes correctly. Only use semicolons when you have multiple statements in a single line. Here’s an example:
```lua 
local Object = instance.new("Part"); Object.Parent = ...

...
```

The style guideline does promote the use of multiple statements in a single line but only to a certain extent. It's a good way to group code into sections and visually reduce your LOC.

When you have more complex expressions or statements-- the ones that especially span over lines are the ones where you should optionally use semicolons like so:

```lua 
local Vector = Vector3.new (
    10,
    10,
    10
); -- Optional

local _ = ( ... and ... ) or ( ... and ... ); -- Optional



if ... then 
    CallFunction();   -- Bad
    Value += 1;       -- Bad
end
```

# Expressions

## Conditional Expressions
Expressions must be constructed in single statements like so:

```lua 
if Running and Sitting then
    
end

```
Paranthesis shouldn't be used in if-statements unless you have ternary operators which will be discussed later on.

### The Anti-pattern


```lua 
if not Running and Sitting
   or Sitting
   or ...
   or ... then
   
end
```
The shown pattern above is interpreted by many as a code smell. The modified Lua 5.1 language server for external Luau-like analysis in IDEs won’t function properly with this. This code smell also exists in other languages and IDEs like JetBrains will flag this. Remember this style guide respects Luau’s linter analysis’s rules and all mentioned styles are in regard to proper linter functionality.

## Conditional Branching

Conditional branching is the biggest way to reduce readability besides bad variable naming-- this means that you should  keep branching to a minimum when possible to lower the cognitive complexity of your code [2]. If needed you may take the inverse of your condition to optimize the cognitive complexity like so:

```lua 
local Profile = ...

    if Profile then

        profile:ListenToRelease(function()
            CachedProfiles[Player] = nil
            PlayerData[Player] = nil
            player:Kick(...)
        end)

        if Player:IsDescendantOf(Players) then
            CachedProfiles[Player] = Profile

            local Data = CachedProfiles[Player]

            PlayerData[Player] = {...}
        else
            Profile:Release()
        end
    else
        Player:Kick(...)
    end
```
This can be optimized by preserving the semantics and only taking the inverse of a few conditions to generate less branching and LOC. In many times, we can use this to optimize the control-flow graph and produce less edge cases and inherently reduce quantitative metrics measuring the complexity of our code like so:

```lua 
if not Profile then Player:Kick() end

Profile:ListenToRelease(function()
    CachedProfiles[Player] = nil
    PlayerData[Player] = nil
    Player:Kick(...)
end)

if not Player:IsDescendantOf(Players) then profile:Release() end
CachedProfiles[Player] = Profile

local Data = CachedProfiles[player]
PlayerData[player] = {...}
```

In-line conditionals are completely allowed and encouraged as again our flexible lexer will generate tokens just fine along with regards to static analysis. A general rule is that in-line if-statements should be avoided if they become too long. This is determined intuitively by the programmer.

## Boolean Semantics

### Null-Coalescing and Ternary Optimization
Short-circuit evaluation also known as “minimal evaluation”, is another practice used to create more readable code-- this will not raise the cognitive complexity of your code:

> Cognitive Complexity also ignores the null-coalescing operators found in many languages,
again because they allow short-handing multiple lines of code into one. For example, both of
the following code samples do the same thing:
```
MyObj myObj = null;        MyObj myObj = a?.myObj;
if (a != null) {
 myObj = a.myObj;
}                    
```
>The meaning of the version on the left takes a moment to process, while the version on the
right is immediately clear once you understand the null-coalescing syntax. For that reason,
Cognitive Complexity ignores null-coalescing operators.

*G. Ann Campbell. 2018. Cognitive Complexity - A new way of measuring understandability. Technical Report. SonarSource SA, Switzerland. https://www.sonarsource.com/docs/CognitiveComplexity.pdf*

### Ternary Semantics Syntax
Ternary operators can become harder to read which is why it is important to provide parenthesis like so:

```lua 
local WalkingSpeed = ( Healthy and Fast ) or Slow

local ... = ( Healthy and Running ) or ( BadHealth and Sitting )
```
Bad:
```lua 
local WalkingSpeed = Healthy and Fast or Slow

local ... = Healthy and Running or BadHealth and Sitting -- Way to confusing to read
```

## Parentheses Rule
Parentheses should only be spaced in between if your parentheses are nested like so:

```lua 
local ... = Call( Vector3.new() )
```

## Precedence (TODO)


# Tables

## Structuring
Table elements must be separated only by commas. Semicolons are discouraged and bad practice as they are used instead to show a line has ended. This may also cause confusion in table arrangement so it’s best to steer away and stick to consistent code.

Linear Arrays that hold only indices must be structured like so:
```lua 
local Array = {1, 2, 3, 4, 5, 6, 7, 9, 10}
local FriendList = {"James", "Hairy", "Bob", "Newton"}
```
If your Array holds too many indices to be grouped into a single line-- you should make them multi-lined like so:
```lua
local MyManyFriends = {
    "Sven",
    "Armstrong",
    "Roberto",
    "John",
    "Gavin",
    "Lucy",
    "Kevin",
    "Bob"
}
```
Dictionary-tables are structured like so and will always be multi-lined: 

```lua 
local Physics = {
    Inertia = 5,
    RotationalInertia = Vector3.new(),
    Gyro = Vector3.new(4000, 0, 4000)
    
    MobilityForce = {
        Forward = 3000,
        Left = 1500,
        Right = 1500,
        Backwards = 3000
    }
}
```

If you find yourself implementing data structures in tables you must have names referring to their abstract data types somewhere in the name of your table variable, like so:
```lua 
local WeaponTree = {}

local DialogueGraph = {}

local EventQueue = {}
```

### Unoccupied Tables
When you construct a table and later populate you should comment future elements like so:
```lua 
local HitList = {} -- { Players }
local GetFriends = {} -- { Bob, Sam, Jimmy }

-- So we know what elements will be appended to our table for further context.
```

## Indexing
When possible you should always be indexing your tables with the following notation:

```lua 
Table.Element
```
Over
```lua 
Table["Element"]
```

### Indexing Optimization
As of the writing of this style guide, Luau only does inline caching for the first shown example. Luau implements this using a mechanism that is used by many VMs like JVM and LuaJIT. The compiler can predict the hash slot used by field lookup, and the VM can correct this prediction dynamically [1].


## Abstraction

If your Tables take up too much space-- it is best to abstract them into modules which should preferably be placed underneath your script or module. Less code is always better code.

# Functions

## Structure

Your functions should be structured consistently like so:

1. Comments first!
2. Guard Statements, returns that have to be done on certain conditions to stop your function from running.
3. Argument Checks.
4. Variables.
6. Main logic.
7. Only return if needed. specifying no returns by returning nil is more to read.

Example:

```lua 
local function Run(Player, Speed)
--[[
    Comments about the function here if necessary. Putting comments inside the function
    is better because most IDEs allow you to close out scopes so we can close out long comments too!
]]

-- Guard Statement first, try to keep this an in-line statement if possible:
if not Healthy return end

-- Argument checks next:
Player = Player or error(...)
Speed = Speed or 10

-- Variables:
local Humanoid = ...
local ... = ...

-- Main Logic:

...

if ... then
    ...
end

...

end
```

## Monadic, Dyadic, Triad forms (TODO)

## Parameters

When you reach over 5 arguments to your functions you should use tables instead. As your parameters grow it increases the chances of parameter positional mismatch which is why you should take the aforementioned developer ergonomic approach. You’ll also actually save VM registers.

Function parameters should be commented out with their types like so (if you don't use Luau's static type checking system):

```lua 
local function Run(Speed, Duration) -- Run ( Speed: number, Duration: number )

end

-- Varags should be styled like so:

-- Function ( Argument: string, (single name here that regards to the varags): ... ) 

```
Exactly as formatted above.


# Error Handling
You should always be doing proper error handling to keep efficient maintainability of your code.

## Dynamic checks
This guide is against the assert function. Simple short circuit evaluations can be used which look much more readable like so:
```lua 
Player = Player or error("The player does not exist :(")
-- vs
assert(Player, "The player does not exist")
```
Internally, *assert* handles errors much efficiently but we care about readability more and worrying about how efficiently code is going to error is completely extraneous as our code is going to stop operating anyways.

## Abstract erroring
Sometimes we want a more efficient and readable way to handle erroring and safety checks. We can apply the concept of unit-testing and create readable tests like below:

```lua
local function ...(Argument)
Argument = Check(Argument).Type.ShouldBe("string") and Argument or error("") -- We could also error internally and abstract our error message
end
```
Although preferably when we are doing tight performance operations-- unit-test patterns can become a problem however Luau is constantly optimizing.
## Native vs Luau Consistency

*type()* should only be used to check native types for consistency and context reasons while *typeof()* should only be preserved for Luau types.

# Metadata
Metadata should always be abstracted into modules.

## Format and Structure
Your Metadata module should return an anonymous table unless needed. Here's an example below:

```lua 
return { -- Metadata modules must only return an anonymous table

    TropicalFish = { 
        LevelToEarn = 1,        -- What level this is earned at
        MaxHunger = 18,         -- Max hunger
        SpeedBoast = 5,         -- How fast the fish can travel when boosting
        SpeedBoostDuration = 2, -- Speed boost duration in seconds
        BiteDamage = 1,         -- The damage of the bite
        Size = 1,               -- Size of fish, serves to who can eat who
        Speed = 1,              -- Speed at which the fish swims at
        Model = nil,            -- Template Model 
        Species = "SmallFish",  -- Class
 
        Animations = {          -- Animation set
            Swim = "",
            MouthOpen = ""
        }
    }
 
}
```

# Object-oriented 

## Meta-Mechanism
Metatables should only be used for object-oriented patterns. Internally, Lua’s meta-mechanisms are very inefficient as numerous register accesses have to be made and elements have to be copied and moved continuously. Do not worry, however, as Luau plans on implementing a built-in inheritance syntax in the near future at the time of this writing.

## Classes
Your index metamethod should point directly back at the table for Luau optimizations to be made and for fast method calls to be properly made [1]. Classes should be formatted just like below:

```lua 
local FishClass = {}
FishClass.__index = FishClass
```

## Constructor
You’re constructor must be constructed and accessed using the following notation below:

```lua 
function Class.new()

Object.new()
```
Your constructor may be *PascalCase* or *camelCase*, this one is up to you as people commonly use *camelCase* for their constructors. Methods must use the notation: *Object:Hello()*

## Private Fields
You should denote private fields with the convention: *_* before the variable or function like so:
```lua 
function Class:_InternalFunction()

end
```

## Format and Structure

Here is an example of how everything should go together:

```lua 
local HumanoidClass = {}                -- Must include "Class" here
HumanoidClass.__index = HumanoidClass   -- Declare the metamethod here and not in the table unless needed
 
 
function HumanoidClass.new(PlayerName, ...) -- .new( PlayersName: string, ExtraAttributes: ... )
    PlayerName = PlayerName or error("Function takes the players name as its 1st argument");
    
    return setmetatable({
        _WalkSpeed = 0,
        _Sitting = false,
        _PlayersName = PlayerName,     -- Put _ at the beginning  of the variable to show it's a private variable.
        _ExtraAttributes = ...
    }, HumanoidClass)
end 
 
 
function HumanoidClass:Walk(WalkSpeed) -- :Walk( Speed: number )
    WalkSpeed = WalkSpeed or 16;
    
    self._WalkSpeed = WalkSpeed
    
    -- Walk
end
 
 
function HumanoidClass:ToggleSitting(ToggleSittingDuration) -- :Sit( ToggleSittingDuration: number )
    local HumanoidSitting = self._Sitting
    HumanoidSitting = not HumanoidSitting
    
    if ToggleSittingDuration then
        wait(SittingDuration)
        HumanoidSitting = not HumanoidSitting
    end
    
end
 
return HumanoidClass
```


# References 


:::info
[1] https://roblox.github.io/luau/performance

[2] G. Ann Campbell. 2018. Cognitive Complexity - A new way of measuring understandability. Technical Report. SonarSource SA, Switzerland. https://www.sonarsource.com/docs/CognitiveComplexity.pdf

[3] Martin, Robert C. Clean Code: A Handbook of Agile Software Craftsmanship. Upper Saddle River, NJ: Prentice Hall, 2009. Print.

:::


###### Tags: `Luau`, `Lua`, `StyleGuide`
 
