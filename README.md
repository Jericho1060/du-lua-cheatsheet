# du-lua-cheatsheet
 a simple cheatsheet for lua in Dual Universe

## Menu

* [Comments](#comments)
* [invoking functions](#invoking-functions)
* [Tables / arrays](#tables--arrays)
* [Loops](#loops)
* [Conditions](#conditions)
* [variables](#variables)
* [Functions](#functions)
* [Lookups](#lookups)
* [Metatables](#metatables)
* [classes](#classes)
* [constants](#constants)
* [operators and thei metatable names](#operators-and-thei-metatable-names)
* [API Global Functions](#api-global-functions)
* [API Strings](#api-strings)
* [Coroutine](#coroutine)

# Lua Cheatsheet

## Comments

```lua
-- comment

--[[
    multiline
    comment
]]
```

## invoking functions

```lua
system.print()
system.print("Hi")

-- You can omit parentheses if the argument is one string or table literal
system.print "Hello World"     <->     system.print("Hello World")
f{x=10, y=20}                  <->     f({x=10, y=20})
type{}                         <->     type({})
```

## Tables / arrays

```lua
t = {}
t = { a = 1, b = 2 }
t.a = function() ... end

t = { ["hello"] = 200 }
t.hello

-- Remember, arrays are also tables
array = { "a", "b", "c", "d" }
system.print(array[2])       -- "b" (one-indexed)
system.print(#array)         -- 4 (length)


-- API functions
table.foreach(t, function(row) ... end)       -- Iterates over the table
table.setn(t, 10)                             -- Sets the length of the table
table.insert(t, 21)                           -- append (--> t[#t+1] = 21)
table.insert(t, 4, 99)                        -- insert (--> t[4] = 99)
table.getn(t)                                 -- length (--> #t)
table.concat(t, ", ")                         -- join (--> "a, b, c, d")
table.sort(t)                                 -- sort (--> { "a", "b", "c", "d" }
table.sort(t, function(a,b) return a > b end) -- sort (--> { "d", "c", "b", "a" }
table.remove(t, 4)                            -- remove (--> "d")
```

## Loops

```lua
while condition do
end

for i = 1,5 do
end

for i = start,finish,delta do
end

for k,v in pairs(tab) do
end

repeat
until condition

-- Breaking out:
while x do
  if condition then break end
end
```

## Conditions

```lua
if condition then
  system.print("yes")
elseif condition then
  system.print("maybe")
else
  system.print("no")
end
```

# variables

```lua
local x = 2
two = 2
two, four = 2, 4
```

## Functions

```lua
function myFunction()
  return 1
end

function myFunctionWithArgs(a, b)
  -- ...
end

myFunction()

anonymousFunctions(function()
  -- ...
end)

-- Not exported in the module
local function myPrivateFunction()
end

-- Splats
function doAction(action, ...)
  system.print("Doing '"..action.."' to", ...) -- "Doing 'write' to", "Shirley", "Abed"
end

doAction('write', "Shirley", "Abed")
```

## Lookups

```lua
mytable = { x = 2, y = function() .. end }

-- The same:
mytable.x
mytable['x']

-- Syntactic sugar, these are equivalent:
mytable.y(mytable)
mytable:y()

mytable.y(mytable, a, b)
mytable:y(a, b)

function X:y(z) .. end
function X.y(self, z) .. end
```

-- Metatables

```lua
mt = {}

-- A metatable is simply a table with functions in it.
mt.__tostring = function() return "lol" end
mt.__add      = function(b) ... end       -- a + b
mt.__mul      = function(b) ... end       -- a * b
mt.__index    = function(k) ... end       -- Lookups (a[k] or a.k)
mt.__newindex = function(k, v) ... end    -- Setters (a[k] = v)

-- Metatables allow you to override behavior of another table.
mytable = {}
setmetatable(mytable, mt)

system.print(myobject)
```

## classes

```lua
-- A class is simply a table with a metatable.
-- The metatable is used to create new instances of the class.
-- The metatable's __index is used to lookup methods on the class.

-- Create a new class
Account = {}

function Account:new(balance)
  local t = setmetatable({}, { __index = Account })

  -- Your constructor stuff
  t.balance = (balance or 0)
  return t
end

function Account:withdraw(amount)
  print("Withdrawing "..amount.."...")
  self.balance = self.balance - amount
  self:report()
end

function Account:report()
  system.print("Your current balance is: "..self.balance)
end

a = Account:new(9000)
a:withdraw(200)    -- method call

```

## constants

```lua
nil
false
true
```

## operators and thei metatable names

```lua
-- Relational (binary)
-- __eq  __lt  __gt  __le  __ge
   ==    <     >     <=    >=
~=   -- Not equal, just like !=

-- Arithmetic (binary)
-- __add  __sub  __muv  __div  __mod  __pow
   +      -      *      /      %      ^

-- Arithmetic (unary)
-- __unm (unary minus)
   -

-- Logic (and/or)
nil and false  --> nil
false and nil  --> false
0 and 20       --> 20
10 and 20      --> 20


-- Length
-- __len(array)
#array


-- Indexing
-- __index(table, key)
t[key]
t.key

-- __newindex(table, key, value)
t[key]=value

-- String concat
-- __concat(left, right)
"hello, "..name

-- Call
-- __call(func, ...)
```

## API Global Functions

```lua
assert(x) -- Throws an error if x is false
assert(x, "failed") -- Throws an error if x is false, with the message "failed"

type(x) -- Returns the type of x ("nil" | "number" | "string" | "boolean" | "table" | "function" | "thread")

-- Does /not/ invoke meta methods (__index and __newindex)
rawset(t, index, value)    -- Like t[index] = value
rawget(t, index)           -- Like t[index]

_G  -- Global context
setfenv(1, {})  -- 1: current function, 2: caller, and so on -- {}: the new _G

pairs(t)     -- iterable list of {key, value}
ipairs(t)    -- iterable list of {index, value}

tonumber("34") -- 34
tonumber("8f", 16) -- 143
```

## API: Strings

```lua
'string'..'concatenation' -- "stringconcatenation"

s = "Hello"
s:upper()            -- Just like string.upper(s): "HELLO"
s:lower()            -- Just like string.lower(s): "hello"
s:len()              -- Just like #s or string.len(s): 5

s:find("e")          -- Just like string.find(s, "e"): 2 index (in lua index start ar 1, not 0) or nil if not found
s:gfind("l")         -- Just like string.gfind(s, "l"): iterator of all "l" in s or nil if not found

s:match("e")         -- Just like string.match(s, "e"): "e" or nil if not found
s:gmatch("l")        -- Just like string.gmatch(s, "l"): iterator of all "l" in s or nil if not found

s:sub(2, 4)          -- Just like string.sub(s, 2, 4): "ell"
s:gsub("l", "i", 10) -- Just like string.gsub(s, "l", "i", 10): "Heiio" (10 is the number of replacement)

s:rep(10)            -- Just like string.rep(s, 10): "HelloHelloHelloHelloHelloHelloHelloHelloHelloHello"
s:char(65)           -- Just like string.char(65): "A"
s:reverse()          -- Just like string.reverse(s): "olleH"
s:byte()             -- Just like string.byte(s): 72 (the first byte of the string)
s:format("%s world") -- Just like string.format("%s world", s): "Hello world"
```

## Coroutine

```lua
-- A coroutine is a function that can be paused and resumed.
-- It is useful for implementing cooperative multitasking.

-- Create a new coroutine
co = coroutine.create(function() --[[do something]] end)

-- Resume a coroutine
coroutine.resume(co) -- true | false, error

-- Yield a coroutine
coroutine.yield()

-- Get the status of a coroutine
coroutine.status(co) -- "running" | "suspended" | "dead"

-- example
co = coroutine.create(function()
  for i = 1, 10 do
    system.print(i) -- print 1 to 10
    coroutine.yield() -- pause
  end
end)

coroutine.resume(co) -- 1
coroutine.resume(co) -- 2
coroutine.resume(co) -- 3
coroutine.resume(co) -- 4
coroutine.resume(co) -- 5
-- ...

```