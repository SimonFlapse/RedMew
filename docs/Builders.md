# [Work in progress] Guide on using the map_gen/shared/builders
Currently our builders module contains 83 functions assisting our map creation. This wiki entry strives to document the usage of these.
## Basic map generation
Basic map generation using our map_gen/shared/generate module consists of the usage of functions returning true or false for a given coordinate. Notice that factorios map generation works before the scenarios', the true or false only determines whether to keep the current map or remove it entirely, leaving void in its place.

***
**Example 1.1 (20 x 20 square)**

This example generates a 20 x 20 square and leaves everything else as void. Notice how the usage of the boolean true and false affects the map generation.
```lua 
local function createVoid(x, y)
    if (math.abs(x) <= 10 and math.abs(y) <= 10) then
        return true
    end
    return false
end

return createVoid
```
**Result:**

Since the function returns false (translated into void) for all coordinates except `-10 <= x <= 10 | -10 <= y <= 10` the result is a 20 x 20 square shape

![image](https://user-images.githubusercontent.com/44922798/52696220-c6385780-2f6e-11e9-8b80-2935d6319c0e.png)

***

**Example 1.2 (20 x 20 square using builder)**
```lua
local b = require 'map_gen.shared.builders'

local square = b.rectangle(20,20)

return square
```

# Functions

## Builders.Rectangle
Create a rectangular shape

`@param width int` <br> `@param height int` <br>

_Example_
```lua
local square = b.rectangle(16, 8)
```

![image](https://user-images.githubusercontent.com/44922798/52699811-e9670500-2f76-11e9-8916-f307a0df28c4.png)
