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

local shape = b.rectangle(20,20)

return shape
```

Useful for testing:
```lua
/c local x = 256 game.forces.player.chart(game.player.surface, {lefttop = {x = -x, y = -x}, rightbottom = {x = x, y = x}})
```
The generate module works after the charting has been executed! Wait a while for it to apply

# Functions

## Notes
Every shape is a function with the format function(x, y, world), during map generation the shape function is called with the current x and y coordinate. These coordinates are from the center of a tile resulting in always ending with .5 (eg. 10.5 instead of 10) to get the correct coordinate you can use `world.x` or `world.y` (eg. `x` = 10.5 `world.x` = 10)

Another usage of `world` is when manipulating a shape (eg. by translation) this changes the x and y coordinates while `world.x` and `world.y` will return the true coordinates

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
local square = b.line_y(10)
```


![image](https://user-images.githubusercontent.com/44922798/52701076-a9555180-2f79-11e9-9abf-f4a65d9cb7bd.png)

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

`@param shapes table of functions` table/array of all shapes to be combined (Must have format function(x, y, world) where world is optional) <br> `@see Bilders.any for comparison`

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


## Builders.grow

## Builders.project

## Builders.project_pattern

## Builders.project_overlap

## Builders.enitity

## Builders.entity_func

## Builders.resource

## Builders.apply_entity

## Builders.apply_entities

## Builders.single_pattern

## Builders.single_pattern_overlap

## Builders.single_x_pattern

## Builders.single_y_pattern

## Builders.single_grid_pattern

## Builders.grid_x_pattern

## Builders.grid_y_pattern

## Builders.grid_pattern

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

## Builders.euclidean_value

## Builders.exponential_value

## Builders.prepare_weighted_array
