This wiki entry is under construction.

# [Work in progress] Guide on using the map_gen/shared/builders
Currently our builders module contains 83 functions assisting our map creation. This wiki entry strives to document the usage of these.

This guide might not be enough information, but it sure beats no information at all. If you need any assistance please visit www.redmew.com/discord we'll be happy to assist in #devtalk or #mapgen


**TABLE OF CONTENT**
* [Basic map generation](#basic-map-generation)
* [Getting Started](#getting-started)
* [Functions](#functions)
  * [Notes](#notes)  
  * [Shape creation](#shape-creation)
  * [Shape manipulation](#shape-manipulation) 
  * [Entity creation](#entity-creation) 
  * [Patterns](#patterns) 
  * **Helper functions**  
    * [Manhattan value](#buildersmanhattan_value)
    * [Euclidean value](#builderseuclidean_value)
    * [Exponential value](#buildersexponential_value)

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

local shape = b.rectangle(20,20)

return shape
```

Useful for testing:
```lua
/c local x = 256 game.forces.player.chart(game.player.surface, {lefttop = {x = -x, y = -x}, rightbottom = {x = x, y = x}})
--and
/c global.task_queue_speed = 10 --Threaded work
--or
/c game.speed = 3 --Non-threaded work
```
The generate module works after the charting has been executed! Wait a while for it to apply

# Getting started
Go take a look at https://github.com/Refactorio/RedMew/wiki/Creating-a-new-map

Getting started with the builder you need to understand how to create a new map. Take this example:
```lua
local b = require 'map_gen.shared.builders'

local map = b.rectangle(200, 200)

return map
```
It creates a 200 x 200 square of land and the rest of the map is void.

The important part of this is that a new map should always return a function that takes the following parameters: <br>
`@param x number` x-coordinate <br> `@param y number` y-coordinate <br> `@param world table` containing a `x` and `y` coordinate for the world (Not affected by any manipulation) <br>
Using the builders functions will always return a function that satisfies this.

In the function section you'll notice the use of the variables shape and map. They are interchangeable and affects nothing. Best pratice is to combine shapes and then add any entites to a function called `map` before returning it.

# Functions

## Notes
Every shape is a function with the format function(x, y, world), during map generation the shape function is called with the current x and y coordinate. These coordinates are from the center of a tile resulting in always ending with .5 (eg. 10.5 instead of 10) to get the correct coordinate you can use `world.x` or `world.y` (eg. `x` = 10.5 `world.x` = 10)

Another usage of `world` is when manipulating a shape (eg. by translation) this changes the x and y coordinates while `world.x` and `world.y` will return the true coordinates

## Shape creation
| Function  | Description |
| ------------- | ------------- |
| [Rectangle](#buildersrectangle)  | Creates a rectangular shape  |
| [Line x](#buildersline_x)  | Creates a infinite vertical line  |
| [Line y](#buildersline_y)  | Creates a infinite horizontal line |
| [Path](#builderspath) | Creates an infinite cross |
| [Square Diamond](#builderssquare_diamond) | Creates a square diamond |
| [Rectangle Diamond](#buildersrectangle_diamond) | Creates a rectangular diamond | 
| [Circle](#builderscircle) | Creates a circle |
| [Oval](#buildersoval) | Creates an oval |
| [Sine wave fill](#builderssine_fill) | Creates a sine wave and fills the gaps |
| [Sine wave](#builderssine_wave) | Creates a sine wave with a thickness |
| [Rectangular spiral](#buildersrectangular_spiral) | Creates an infinite rectangular spiral |
| [Circular spiral](#builderscircular_spiral) | Creates an infinite circular spiral |
| [Circular growing spiral](#builderscircular_spiral_grow) | Creates an infinite growing circular spiral |
| [Circular spiral with n threads](#builderscircular_spiral_n_threads) | Creates a number of threads of infinite circular spirals |
| [Circular growing spiral with n threads](#builderscircular_spiral_grow_n_threads) | Creates a number of infinite growing circular spirals |

## Builders.rectangle
Creates a rectangular shape

`@param width int` <br> `@param height int` <br>

_Example_
```lua
local shape = b.rectangle(16, 8)
```

![image](https://user-images.githubusercontent.com/44922798/52699811-e9670500-2f76-11e9-8916-f307a0df28c4.png)


## Builders.line_x
Creates a infinite vertical line

`@param tickness int` <br>

_Example_
```lua
local shape = b.line_x(10)
```


![image](https://user-images.githubusercontent.com/44922798/52700800-19170c80-2f79-11e9-8030-2bb0e3f01d9b.png)

## Builders.line_y
Creates a infinite horizontal line

`@param tickness int` <br>

_Example_
```lua
local shape = b.line_y(10)
```


![image](https://user-images.githubusercontent.com/44922798/52701076-a9555180-2f79-11e9-9abf-f4a65d9cb7bd.png)

## Builders.path
Creates a infinite cross
Equivalent to combining `line_x` and `line_y`

`@param tickness int` width of the vertical line <br> `@param optional_thickness_height int --optional` width of the horizontal line <br>

_Example_
```lua
local shape = b.path(10, 5)
```


![image](https://user-images.githubusercontent.com/44922798/53253636-02cc2780-36c2-11e9-92fa-af8035bdc194.png)


## Builders.square_diamond
Creates a diamond where width is equal to height <br>
Equivalent to creating a square and rotating it 45 degrees

`@param size int` <br>

_Example_
```lua
local shape = b.square_diamond(50)
```


![image](https://user-images.githubusercontent.com/44922798/52701183-ecafc000-2f79-11e9-8824-3528d2e53e6b.png)

## Builders.rectangle_diamond
Like square_diamond but with configurable width and height <br>
Equivalent to creating a rectangle and rotating it 45 degrees

`@param width int` <br> `@param height int`

_Example_
```lua
local shape = b.rectangle_diamond(32, 16)
```

![image](https://user-images.githubusercontent.com/44922798/52701529-b6267500-2f7a-11e9-82d3-e93622c472fa.png)

## Builders.circle
Creates a circle

`@param radius int` <br>

_Example_
```lua
local shape = b.circle(10)
```


![image](https://user-images.githubusercontent.com/44922798/52701755-3e0c7f00-2f7b-11e9-99f4-f9e1c59a8ee3.png)

## Builders.oval
Like circle but with configurable width and height <br>

`@param radius_x int` <br> `@param radius_y int`

_Example_
```lua
local shape = b.oval(10, 20)
```

![image](https://user-images.githubusercontent.com/44922798/52702079-d73b9580-2f7b-11e9-9a8e-e8f71178b610.png)

## Builders.sine_fill
Creates a sine wave and fills it out <br>

`@param width int` <br> `@param height int` the amplitude <br> `@see sine_wave for comparison`

_Example_
```lua
local shape = b.sine_fill(20, 10)
```

![image](https://user-images.githubusercontent.com/44922798/52702389-529d4700-2f7c-11e9-827d-32652745fb97.png)

## Builders.sine_wave
Creates a sine wave with a thickness <br>

`@param width int --the wave lenght` <br> `@param height int --the amplitude` <br> `@param thickness int` <br> `@see sine_fill for comparison`

_Example_
```lua
local shape = b.sine_wave(20, 10, 2)
```

![image](https://user-images.githubusercontent.com/44922798/52702890-6e551d00-2f7d-11e9-9aef-25296dda84a5.png)

## Builders.rectangular_spiral
Creates an infinite rectangular spiral <br>

`@param x_size int` <br> `@param optional_y_size int` **optional** otherwise equal to x_size

_Example_
```lua
local shape = b.rectangular_spiral(10, 10) 
--equals b.rectangular_spiral(10)
```

![image](https://user-images.githubusercontent.com/44922798/52703155-005d2580-2f7e-11e9-9970-c6ef1fb979c5.png)

## Builders.circular_spiral
Creates an infinite circular spiral <br>

`@param in_thickness int` defines the number of land tiles the spiral contains <br> `@param total_thickness int` defines the number of total tiles the spiral spans over (The gap is `total_thickness - in_thickness`) <br> `@see circular_spiral_grow for comparison`

_Example_
```lua
local shape = b.circular_spiral(5, 10) 
```

![image](https://user-images.githubusercontent.com/44922798/52704684-6e571c00-2f81-11e9-8038-b5e77ded4751.png)

## Builders.circular_spiral_grow
Creates an infinite growing circular spiral <br>

`@param in_thickness int` defines the number of land tiles the spiral contains <br> `@param total_thickness int` defines the number of total tiles the spiral spans over (The gap is `total_thickness - in_thickness`) <br> `@param grow_factor int` defines how quickly the spiral grow. (Lower numbers result in faster growth, larger number in slower.) <br> `@see circular_spiral for comparison`

_Example_
```lua
local shape = b.circular_spiral(5, 10, 50) 
```

![image](https://user-images.githubusercontent.com/44922798/52706325-6b5e2a80-2f85-11e9-9cae-1502691a9750.png)

## Builders.circular_spiral_n_threads
Creates a number of threads of infinite circular spirals <br>

`@param in_thickness int` defines the number of land tiles the spiral contains <br> `@param total_thickness int` defines the number of total tiles the spiral spans over (The gap is `total_thickness - in_thickness`) <br> `@param n_threads int` defines the number of threads/spirals <br> `@see circular_spiral for comparison` <br> `@see circular_spiral_grow_n_threads for comparison`

_Example_
```lua
local shape = b.circular_spiral_n_threads(5, 10, 2) 
```

![image](https://user-images.githubusercontent.com/44922798/52716633-7aea6d00-2f9f-11e9-9a9c-62b6a868cfee.png)

## Builders.circular_spiral_grow_n_threads
Creates a number of infinite growing circular spirals <br>

`@param in_thickness int` defines the number of land tiles the spiral contains <br> `@param total_thickness int` defines the number of total tiles the spiral spans over (The gap is `total_thickness - in_thickness`) <br> `@param grow_factor int` defines how quickly the spiral grow. (Lower numbers result in faster growth, larger number in slower.) <br> `@param n_threads int` defines the number of threads/spirals <br> `@see circular_spiral_grow for comparison` <br> `@see circular_spiral_n_threads for comparison`

_Example_
```lua
local shape = b.circular_spiral_grow_n_threads(5, 10, 50, 2) 
```

![image](https://user-images.githubusercontent.com/44922798/52716887-111e9300-2fa0-11e9-9ec6-8370ddb26ade.png)


## Builders.decompress
**TBC**

## Builders.picture
**TBC**

## Shape manipulation

| Function  | Description |
| ------------- | ------------- |
| [Translate](#builderstranslate) | Translates a shapes position |
| [Scale](#buildersscale) | Scales a shapes |
| [Rotate](#buildersrotate) | Rotates a shape counter clockwise |
| [Flip along x](#buildersflip_x) | Flips a shape along the x-axis |
| [Flip along y](#buildersflip_y) | Flips a shape along the y-axis |
| [Flip both x and y](#buildersflip_xy) | Flips a shape along the xy-axis |
| [Combine Any](#buildersany) | **OR** combine |
| [Combine All](#buildersall) | **AND** combine |
| [~~Combine~~](#builderscombine) | _Unused_ : _No Docs_ |
| [Add](#buildersadd) | **OR** combine. _Only two shapes_ |
| [Subtract](#builderssubtract) | Subtracts a shape from the other. |
| [Invert](#buildersinvert) | Inverts a shape |
| [Throttle along x](#buildersthrottle_x) | Cuts horizontal lines in a shape |
| [Throttle along y](#buildersthrottle_y) | Cuts vertical lines in a shape |
| [Throttle along x and y](#buildersthrottle_xy) | Applies `Builders.throttle_x` and `Builders.throttle_y` to a shape |
| [Throttle along world.x and world.y](#buildersthrottle_world_xy) | Preferred over `Builders.throttle_xy` |
| [Choose](#builderschoose) | Applying one of two shapes based on output of another shape |
| [If else](#buildersif_else)   | Applying one shape based on the output of another shape |
| [Linear grow](#builderslinear_grow)   | _No Docs_ |
| [Grow](#buildersgrow)   |  _No Docs_ |
| [Project](#buildersproject)   |  _No Docs_ |
| [Project pattern](#buildersproject_pattern)   |  _No Docs_ |
| [Project overlap](#buildersproject_overlap)   |  _No Docs_ |

## Builders.translate
Translates a shapes position

`@param shape function` the function of a shape to be translated (Must have format function(x, y, world) where world is optional) <br> `@param x_offset int` <br> `@param y_offset int` <br>

_Example_
<br>
Using a rectangle shape
```lua
local shape = b.translate(b.rectangle(16, 8), 8, 4)
```

Player is at position (0, 0) <br>
Without translation (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52717279-116b5e00-2fa1-11e9-9c22-badbd9df6e3c.png) <br>
With translation (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52717248-fb5d9d80-2fa0-11e9-946a-ad10703fc2b3.png)

## Builders.scale
Scales a shapes

`@param shape function` the function of a shape to be scaled (Must have format function(x, y, world) where world is optional) <br> `@param x_scale int` <br> `@param y_scale int` <br>

_Example_
<br>
Using a rectangle shape
```lua
local shape = b.scale(b.rectangle(16, 8), 1, 2)
```

Player is at position (0, 0) <br>
Without scaling (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52717279-116b5e00-2fa1-11e9-9c22-badbd9df6e3c.png) <br>
With scaling (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52718083-e7b33680-2fa2-11e9-8c54-628666bd05f9.png)

## Builders.rotate
Rotates a shape counter clockwise

`@param shape function` the function of a shape to be rotated (Must have format function(x, y, world) where world is optional) <br> `@param angle int` specified in radians (NOT degrees) <br>

_Example_
<br>
Using a rectangle shape
```lua
local shape = b.rotate(b.rectangle(16, 8), math.pi/2)
```

Player is at position (0, 0) <br>
Without rotation (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52717279-116b5e00-2fa1-11e9-9c22-badbd9df6e3c.png) <br>
With rotation (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52717882-6eb3df00-2fa2-11e9-8a2b-84937d43dda9.png)

## Builders.flip_x
Flips a shape along the x-axis
Equivalent to rotate by pi

`@param shape function` the function of a shape to be flipped (Must have format function(x, y, world) where world is optional) <br>

_Example_
<br>
Using a L shape
```lua
-- creating the L shape
local pre_shape = b.translate(b.rectangle(16, 8), 4, 0)
pre_shape = b.add(pre_shape, b.rotate(pre_shape), math.pi/2)

--applying flip_x
local shape = b.flip_x(pre_shape)
```

Player is at position (0, 0) <br>
Without flipping along x (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52718941-e1be5500-2fa4-11e9-8827-34ff98f0cde8.png) <br>
With flipping along x (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52718975-f1d63480-2fa4-11e9-9018-3134b1954ade.png)

## Builders.flip_y
Flips a shape along the y-axis
Equivalent to rotate by -pi

`@param shape function` the function of a shape to be flipped (Must have format function(x, y, world) where world is optional) <br>

_Example_
<br>
Using a rectangle rotated shape
```lua
-- creating the L shape
local pre_shape = b.translate(b.rectangle(16, 8), 4, 0)
pre_shape = b.add(pre_shape, b.rotate(pre_shape), math.pi/2)

--applying flip_y
local shape = b.flip_y(pre_shape)
```


Player is at position (0, 0) <br>
Without flipping along y (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52718941-e1be5500-2fa4-11e9-8827-34ff98f0cde8.png) <br>
With flipping along y (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52719040-1500e400-2fa5-11e9-9188-a77766a3161c.png)

## Builders.flip_xy
Flips a shape along the xy-axis
Equivalent to rotate by 2pi

`@param shape function` the function of a shape to be flipped (Must have format function(x, y, world) where world is optional) <br>

_Example_
<br>
Using a rectangle rotated shape
```lua
-- creating the L shape
local pre_shape = b.translate(b.rectangle(16, 8), 4, 0)
pre_shape = b.add(pre_shape, b.rotate(pre_shape), math.pi/2)

--applying flip_y
local shape = b.flip_xy(pre_shape)
```


Player is at position (0, 0) <br>
Without flipping along xy (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52718941-e1be5500-2fa4-11e9-8827-34ff98f0cde8.png) <br>
With flipping along xy (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52719333-bbe58000-2fa5-11e9-92be-4eac6d76937f.png)

## Builders.any
Combines all shapes in supplied array as if it where evaluated as an _OR_ operation.
If any shape returns true for a coordinate, the resulting shape returns true

`@param shapes table of functions` table/array of all shapes to be combined (Must have format function(x, y, world) where world is optional) <br> `@see Builders.all for comparison`

_Example_
<br>
Using 4 rectangles which have been rotated
```lua
-- creating the 4 shapes
local shape1 = b.translate(b.rectangle(16, 8), 4, 0)
local shape2 = b.rotate(shape1), math.pi/2)
local shape3 = b.rotate(shape2), math.pi/2)
local shape4 = b.rotate(shape3), math.pi/2)

--Combining using any
local shape = b.any({shape1, shape2, shape3, shape4})
```


Player is at position (0, 0) <br>
Base shape (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52720893-c9e8d000-2fa8-11e9-97bb-77bcedd97ae9.png) <br>
Resulting shape (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52720814-a6be2080-2fa8-11e9-97ed-f406eb7e57e3.png)


## Builders.all
Combines all shapes in supplied array as if it where evaluated as an _AND_ operation
If, and only if, all shapes returns true for a coordinate, the resulting shape returns true.

`@param shapes table of functions` table/array of all shapes to be combined (Must have format function(x, y, world) where world is optional) <br> `@see Builders.any for comparison`

_Example_
<br>
Using 4 rectangles which have been rotated
```lua
-- creating the 4 shapes
local shape1 = b.translate(b.rectangle(16, 8), 4, 0)
local shape2 = b.rotate(shape1), math.pi/2)
local shape3 = b.rotate(shape2), math.pi/2)
local shape4 = b.rotate(shape3), math.pi/2)

--Combining using all
local shape = b.all({shape1, shape2, shape3, shape4})
```


Player is at position (0, 0) <br>
Base shape (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52720893-c9e8d000-2fa8-11e9-97bb-77bcedd97ae9.png) <br>
(Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52720992-01577c80-2fa9-11e9-9fea-9b5ff92f609d.png)


## Builders.combine
**TBC** <br>
No map currently uses Builders.combine

Expected behavior: <br>
Works like Builders.any but keeps any entities that have been added to a shape. <br>
Builders.any is using a lazy evaluation and thus terminates at first `true`. <br>

Alternative usage:
Use Builders.any and apply entities after using Builders.apply_entity or Builders.apply_entities

## Builders.add
Combines two shapes as if it where evaluated as an _OR_ operation.
Equivalent to `Builders.any({shape1, shape2})`

`@param shape1 function` the function of the first shape to be combined (Must have format function(x, y, world) where world is optional) <br> `@param shape2 function` the function of the second shape to be combined (Must have format function(x, y, world) where world is optional) <br> `@see Builders.any for comparison`

_Example_
<br>
Using 2 rectangles to form an L shape
```lua
-- creating the L shape
local shape1 = b.translate(b.rectangle(16, 8), 4, 0)
local shape2 = b.rotate(shape1, math.pi/2)

--applying flip_x
local shape = b.add(shape1, shape2)
```

Result: (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52718975-f1d63480-2fa4-11e9-9018-3134b1954ade.png)

## Builders.subtract
Subtracts a shape from the other.

`@param shape function` the function of the shape to be subtracted from (Must have format function(x, y, world) where world is optional) <br> `@param minus_shape function` the function of the subtracting shape (Must have format function(x, y, world) where world is optional)

_Example_
<br>
Using 2 rectangles
```lua
-- creating the 2 rectangles
local shape1 = b.rectangle(10, 10)
local shape2 = b.rectangle(5, 5)

--applying subtract
local shape = b.subtract(shape1, shape2)
```

Result: (Water added for illustrational purposes) <br>
![image](https://user-images.githubusercontent.com/44922798/52724542-d4f32e80-2faf-11e9-8b99-2b8bc37b18f9.png)

## Builders.invert
Inverts a shape (true becomes false and vice versa)

`@param shape function` the function of the shape to be inverted (Must have format function(x, y, world) where world is optional) <br>

_Example_
<br>
Using 2 rectangles subtracted (see Builders.subtract example)
```lua
-- creating the 2 rectangles
local shape1 = b.rectangle(10, 10)
local shape2 = b.rectangle(5, 5)

--applying subtract
local pre_shape = b.subtract(shape1, shape2)

--applying invert
local shape = b.invert(pre_shape)
```

Before inversion (Water added for illustrational purposes, acts as false) <br>
![image](https://user-images.githubusercontent.com/44922798/52724542-d4f32e80-2faf-11e9-8b99-2b8bc37b18f9.png) <br>
After inversion (Water added for illustrational purposes, acts as false) <br>
![image](https://user-images.githubusercontent.com/44922798/52724967-9a3dc600-2fb0-11e9-8a47-d1c8912a72ca.png)


## Builders.throttle_x
Cuts horizontal lines in a shape

`@param shape function` the function of the shape to be throttled (Must have format function(x, y, world) where world is optional)  <br> `@param x_in int` width of tiles unaffected <br> `@param x_size int` total width of a single part of the throttled shape (affected tiles width equals `(x_size - x_in)/2`)

_Example_
<br>
Using a rectangle
```lua
-- creating the rectangle
local rectangle = b.rectangle(20, 20)

--applying throttle_x
local shape = b.throttle_x(rectangle, 2, 4)
```

Before throttle_x (Water added for illustrational purposes, acts as false) <br>
![image](https://user-images.githubusercontent.com/44922798/52725661-ee957580-2fb1-11e9-9339-4bcb1ea16bee.png) <br>
After throttle_x (Water added for illustrational purposes, acts as false) <br>
![image](https://user-images.githubusercontent.com/44922798/52725579-ce65b680-2fb1-11e9-9742-150f2ecb2224.png)

_Elaboration_
<br>
Calling `builders.throttle_x` with `x_in` as `2` and `x_size` as `4` results in the rectangle being cut into `20 / x_size <=> 5` pieces each with width `x_size <=> 4`. Since `x_in` is `2`, two tiles in the width are kept as land (true), while `(x_size - x_in) / 2 <=> 1` tile on either side is discarded as water/void (false).

## Builders.throttle_y
Cuts vertical lines in a shape

`@param shape function` the function of the shape to be throttled (Must have format function(x, y, world) where world is optional)  <br> `@param y_in int` height of tiles unaffected <br> `@param y_size int` total height of a single part of the throttled shape (affected tiles height equals `(y_size - y_in)/2`)

_Example_
<br>
Using a rectangle
```lua
-- creating the rectangle
local rectangle = b.rectangle(20, 20)

--applying throttle_y
local shape = b.throttle_y(rectangle, 2, 4)
```

Before throttle_y (Water added for illustrational purposes, acts as false) <br>
![image](https://user-images.githubusercontent.com/44922798/52725661-ee957580-2fb1-11e9-9339-4bcb1ea16bee.png) <br>
After throttle_y (Water added for illustrational purposes, acts as false) <br>
![image](https://user-images.githubusercontent.com/44922798/52726804-31584d00-2fb4-11e9-8c7a-c3d848018a89.png)

_Elaboration_
<br>
Calling `builders.throttle_y` with `y_in` as `2` and `y_size` as `4` results in the rectangle being cut into `20 / y_size <=> 5` pieces each with width `y_size <=> 4`. Since `y_in` is `2`, two tiles in the height are kept as land (true), while `(y_size - y_in) / 2 <=> 1` tile above and below is discarded as water/void (false).


## Builders.throttle_xy
Applies `Builders.throttle_x` and `Builders.throttle_y` to a shape

`@param shape function` the function of the shape to be throttled (Must have format function(x, y, world) where world is optional)  <br> `@param x_in int` width of tiles unaffected <br> `@param x_size int` total width of a single part of the throttled shape (affected tiles width equals `(x_size - x_in)/2`) <br> `@param y_in int` height of tiles unaffected <br> `@param y_size int` total height of a single part of the throttled shape (affected tiles height equals `(y_size - y_in)/2`)

_Example_
<br>
Using a rectangle
```lua
-- creating the rectangle
local rectangle = b.rectangle(20, 20)

--applying throttle_y
local shape = b.throttle_xy(rectangle, 2, 4, 2, 4)
```

Before throttle_xy (Water added for illustrational purposes, acts as false) <br>
![image](https://user-images.githubusercontent.com/44922798/52725661-ee957580-2fb1-11e9-9339-4bcb1ea16bee.png) <br>
After throttle_xy (Water added for illustrational purposes, acts as false) <br>
![image](https://user-images.githubusercontent.com/44922798/52727002-9a3fc500-2fb4-11e9-979f-20e652c6df78.png)

## Builders.throttle_world_xy
**TBC** <br>
Almost equivalent to `Builders.throttle_xy` but preferred

## Builders.choose
**TBC** <br>
Given three shapes if first shape returns true apply true_shape if it returns false apply false_shape

## Builders.if_else
Given two shapes if first shape returns false apply else_shape

## Builders.linear_grow
**TBC** <br>
No map currently uses `Builders.linear_grow`

## Builders.grow
**TBC** <br>
The hearts map uses `Builders.grow`

## Builders.project
**TBC** <br>

## Builders.project_pattern
**TBC** <br>

## Builders.project_overlap
**TBC** <br>

## Entity creation
| Function  | Description |
| ------------- | ------------- |
| [Entity](#buildersentity) | Applying a single entity |
| [Entity_function](#buildersentity_func) | Applying entities based on a custom function |
| [Resource](#buildersresource) | Fills a shape with a resource |
| [Apply entity](#buildersapply_entity)   |  _No Docs_ |
| [Apply entities](#buildersapply_entities)   |  _No Docs_ |


## Builders.entity
Returns a table with one entry named `name` whose value is `@param name string` if the supplied shape returns true <br>
Use case: Used to apply trees or rocks

```lua
local tree = b.entity(b.throttle_world_xy(b.rectangle(20, 10), 1, 3, 1, 3), 'tree-01')
```

## Builders.entity_func
Executes function `func` if the supplied shape returns true <br>
Use case: Used to apply a random rock or tree instead of a static one.

```lua
local rock_names = {'rock-big', 'rock-huge', 'sand-rock-big'}
local function rocks_func()
    local rock = rock_names[math.random(#rock_names)]
    return {name = rock}
end

local rocks = b.entity_func(b.throttle_world_xy(b.rectangle(20, 10), 1, 6, 1, 6), rocks_func)
```

## Builders.resource
Fills a shape with a resource

`@param shape function` shape to fill with resource <br> `@param resource_type string` prototype name of resource. Available types listed below <br> `@param amount_function function --optional` function in the format function(x, y), if `nil` value is set to 404 <br> `@param always_place boolean --optional` overrides surface.can_place_entity check <br>

Valid `resource_type`: <br>
`iron-ore`, `copper-ore`, `stone`, `coal`, `uranium-ore`, `crude-oil`

Effect of `always_place` <br>
![image](https://user-images.githubusercontent.com/44922798/52802769-de989700-3080-11e9-8e34-ff0a48a9cfc9.png)

_Example_
<br>
Simple resource generation using `Builders.manhattan_value`
```lua
-- creating a 100 x 100 square of land
local shape = b.rectangle(100, 100)

-- creating a circular shape with radius 10, to be filled with iron-ore
local ore_shape = b.circle(10)

-- creating the resource entity from the ore_shape using manhattan distance to increase ore count with distance from (0, 0)
local iron_ore = b.resource(ore_shape), 'iron-ore', b.manhattan_value(500, 1), false) 

-- applying resource entity to shape
local map = b.apply_entity(shape, iron_ore)
```

_Example_
<br>
Advanced resource generation with amount function
```lua
-- creates value function that returns a fixed amount
local function value(num)
    return function(x, y) -- the parameter x and y can be omitted in this case
        return num
    end
end

-- creating a 100 x 100 square of land
local shape = b.rectangle(100, 100)

-- creating a circular shape with radius 10, to be filled with iron-ore
local ore_shape = b.circle(10)

-- creating the resource entity from the ore_shape using a custom value function
local iron_ore = b.resource(ore_shape), 'iron-ore', value(500), false) 

-- applying resource entity to shape
local map = b.apply_entity(shape, iron_ore)
```

Other `amount_function`s: <br>
`Builders.manhattan_value`, `Builders.euclidean_value`, `Builders.exponential_value`

## Builders.apply_entity

## Builders.apply_entities

## Patterns
| Function  | Description |
| ------------- | ------------- |
| [Single pattern](#builderssingle_pattern)   |  Applies a single shape infinite |
| [Single pattern overlap](#builderssingle_pattern_overlap)   |  Applies a single shape infinite while allowing overlaps |
| [Single x pattern](#builderssingle_x_pattern)   |  Applies a single shape infinite along the x-axis |
| [Single y pattern](#builderssingle_y_pattern)   |  Applies a single shape infinite along the y-axis |
| [~~Single grid pattern~~](#builderssingle_grid_pattern)   |  **Depricated** _No Docs_ |
| [Pattern building](#pattern-building) | How to create shape patterns |
| [Grid x pattern](#buildersgrid_x_pattern)   |  _No Docs_ |
| [Grid y pattern](#buildersgrid_y_pattern)   |  _No Docs_ |
| [Grid pattern](#buildersgrid_pattern)   |  _No Docs_ |



## Builders.single_pattern
Applies a single shape infinite <br>

`@param shape function` the function of a shape to be duplicated in a pattern (Must have format function(x, y, world) where world is optional) <br> `@param width int` width of one unit of the base shape <br> `@param height int --optional` height of one unit of the base shape. If `nil` it equals to width

_Example_
Using a square
```lua
local shape = b.rectangle(10, 10)
local map = b.single_pattern(shape, 15, 15)
```

Since the shape's dimentions are 10 by 10 and the base pattern is 15 by 15 we end up with some void.

![image](https://user-images.githubusercontent.com/44922798/53287494-31f39f00-377d-11e9-9208-a79d0401f444.png)

## Builders.single_pattern_overlap
Applies a single shape infinite while allowing overlaps <br>

`@param shape function` the function of a shape to be duplicated in a pattern (Must have format function(x, y, world) where world is optional) <br> `@param width int` width of one unit of the base shape <br> `@param height int` height of one unit of the base shape <br> `@see Builders.single_pattern` for comparison

_Example_ (Better example wanted!)
Using an L-shape
```lua
local pre_shape = b.translate(b.rectangle(16, 8), 4, 0)
local shape = b.add(pre_shape, b.rotate(pre_shape, math.pi/2))

local map = b.single_pattern_overlap(shape, 25, 12)
```

With overlap
![image](https://user-images.githubusercontent.com/44922798/53287992-7f730a80-3783-11e9-8906-9b13278e4eca.png)

Without overlap
![image](https://user-images.githubusercontent.com/44922798/53287994-8dc12680-3783-11e9-9a27-22e962027219.png)


## Builders.single_x_pattern
Applies a single shape infinite along the x-axis <br>

`@param shape function` the function of a shape to be duplicated in a pattern (Must have format function(x, y, world) where world is optional) <br> `@param width int` width of one unit of the base shape <br> `@see Builders.single_pattern` for comparison

_Example_
Using a square
```lua
local shape = b.rectangle(10, 10)
local map = b.single_x_pattern(shape, 11)
```

Since the shape's dimentions are 10 by 10 and the base pattern is 11 by 11 we end up with some void.

![image](https://user-images.githubusercontent.com/44922798/53288040-2f487800-3784-11e9-8dfb-b522bb711251.png)


## Builders.single_y_pattern
Applies a single shape infinite along the y-axis <br>

`@param shape function` the function of a shape to be duplicated in a pattern (Must have format function(x, y, world) where world is optional) <br> `@param height int` height of one unit of the base shape <br> `@see Builders.single_pattern` for comparison

_Example_
Using a square
```lua
local shape = b.rectangle(10, 10)
local map = b.single_y_pattern(shape, 11)
```

Since the shape's dimentions are 10 by 10 and the base pattern is 11 by 11 we end up with some void.

![image](https://user-images.githubusercontent.com/44922798/53288074-a3831b80-3784-11e9-8094-a5faee7b4252.png)

## Builders.single_grid_pattern
**Depricated** <br>
Do not use, will be removed <br>
Equivalent to `Builders.single_pattern` except:

**Must specify both width and height parameters**

## Pattern building
The grid functions requires a new type of input a pattern. This pattern is a table containing the shapes in a grid like structure.

_Example_
Single row with three colomns
```lua
local pattern = {
    {shape1, shape2, shape3}
}
```
| | 1 | 2 | 3 |
| ------------- | ------------- | ------------- | ------------- |
|**1**| shape1 | shape2 | shape3 |

_Example_
Two row with two colomns
```lua
local pattern = {
    {shape1, shape2},
    {shape2, shape1}
}
```
| | 1 | 2 |
| ------------- | ------------- | ------------- |
|**1**| shape1 | shape2 |
|**2**| shape2 | shape1 |

You can build the patterns just as you'd like, each row is a new table inside the pattern table, and each column is a new value inside the nested table. For structual purposes it's advised to have a consistent amount of rows and colomns, like all of these examples.

_Example_
Five row with four colomns
```lua
local pattern = {
    {shape1, shape2, shape1, shape2},
    {shape2, shape1, shape2, shape1},
    {shape1, shape2, shape1, shape2},
    {shape2, shape1, shape2, shape1},
    {shape1, shape2, shape1, shape2}
}
```
| | 1 | 2 | 3 | 4 |
| ------------- | ------------- | ------------- | ------------- | ------------- |
|**1**| shape1 | shape2 | shape1 | shape2 |
|**2**| shape2 | shape1 | shape2 | shape1 |
|**3**| shape1 | shape2 | shape1 | shape2 |
|**4**| shape2 | shape1 | shape2 | shape1 |
|**5**| shape1 | shape2 | shape1 | shape2 |


## Builders.grid_x_pattern

## Builders.grid_y_pattern

## Builders.grid_pattern
**TBC**

_Example_
<br>
```lua
local b = require 'map_gen.shared.builders'

local water = b.tile('water')
local normal = b.full_shape

local pattern = {
    {normal, water},
    {normal, normal}
}

local map = b.grid_pattern(pattern, 2, 2, 1, 1)
map = b.scale(map, 32, 32)

return map
```

![image](https://user-images.githubusercontent.com/44922798/52890536-88f9e280-3185-11e9-9764-1912f866d8c1.png)


## Builders.grid_pattern_overlap

## Builders.grid_pattern_full_overlap

## Builders.circular_pattern

## Builders.single_spiral_pattern

## Builders.single_spiral_rotate_pattern

## Builders.circular_spiral_pattern

## Builders.circular_spiral_grow_pattern

## Builders.segment_pattern

## Builders.pyramid_pattern

## Builders.pyramid_pattern_inner_overlap

## Builders.grid_pattern_offset

## Builders.change_tile

## Builders.set_hidden_tile

## Builders.change_collision_tile

## Builders.change_map_gen_tile

## Builders.change_map_gen_hidden_tile

## Builders.change_map_gen_collision_tile

## Builders.change_map_gen_collision_hidden_tile

## Builders.overlay_tile_land

## Builders.fish

## Builders.apply_effect

## Builders.manhattan_value
Returns the product of the manhattan distance of the current coordinates from origo and a multiplier. A base number is added to this value <br>
Formula: `multiplier * (|x| + |y|) + base_value` <br>
Laymans' term: the horizontal and vertical distances, between (0, 0) and a given point, added together <br>
![image](https://user-images.githubusercontent.com/44922798/52806591-acd7fe00-3089-11e9-9862-c94803634545.png)<br>
The red line is the manhattan distance between the two points. This distance is equivalent to the blue and yellow line. The green line is the euclidean distance.


`@param base number` the base value or minimum value returned <br> `@param mult number` a number signifying the multiplication of the manhattan distance <br>

_Example_
<br>
Usage of the manhattan_value
```lua
-- returns the base for all coordinates
b.manhattan_value(500, 0) -- Always returns 500

-- returns the base + manhattan distance
b.manhattan_value(500, 1) -- Always returns >= 500 | eg. coordinate (10, 10) gives the manhattan distance of 20, resulting in the manhattan_value of 520.

-- returns the base + mult(manhattan distance)
b.manhattan_value(500, 2) -- Always returns >= 500 | eg. coordinate (10, 10) gives the manhattan distance of 20 (which needs to be multiplied by 2), resulting in the manhattan_value of 540
```

## Builders.euclidean_value
**TBC** <br>
Return the product of the euclidean distance of the current coordinates from origo and a multiplier. A base number is added to this value <br>
Formula: `multiplier * sqrt(x^2 + y^2) + base_value` <br>
Laymans' term: the diagonal distance from point (0, 0) to a given coordinate <br>
![image](https://user-images.githubusercontent.com/44922798/52806591-acd7fe00-3089-11e9-9862-c94803634545.png)<br>
The red line is the manhattan distance between the two points. This distance is equivalent to the blue and yellow line. The green line is the euclidean distance.

`@param base number` the base value or minimum value returned <br> `@param mult number` a number signifying the multiplication of the euclidean distance <br>

## Builders.exponential_value
**TBC** <br>
Formula: `base_value + multiplier * (x^2 + y^2)^(exponent/2)`

`@param base number` the base value or minimum value returned <br> `@param mult number` a number signifying the multiplication of the exponential value <br> `@param pow number` <br>

## Builders.prepare_weighted_array
