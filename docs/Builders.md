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

`@param in_thickness int` defines the number of land tiles the spiral contains <br> `@param total_thickness int` defines the number of total tiles the spiral spans over (The gap is `total_thickness - in_thickness`)

_Example_
```lua
local shape = b.circular_spiral(5, 10) 
```

![image](https://user-images.githubusercontent.com/44922798/52704684-6e571c00-2f81-11e9-8038-b5e77ded4751.png)

## Builders.circular_spiral_grow
Creates an infinite growing circular spiral <br>

`@param in_thickness int` defines the number of land tiles the spiral contains <br> `@param total_thickness int` defines the number of total tiles the spiral spans over (The gap is `total_thickness - in_thickness`) <br> `@param grow_factor int` defines how quickly the spiral grow. (Lower numbers result in faster growth, larger number in slower.)

_Example_
```lua
local shape = b.circular_spiral(5, 10, 50) 
```

![image](https://user-images.githubusercontent.com/44922798/52706325-6b5e2a80-2f85-11e9-9cae-1502691a9750.png)

## Builders.circular_spiral_n_threads
**TBC**

## Builders.circular_spiral_grow_n_threads
**TBC**

## Builders.decompress
**TBC**

## Builders.picture
**TBC**

## Builders.translate
**TBC**

## Builders.rotate
**TBC**

## Builders.flip_x
**TBC**

## Builders.flip_y
**TBC**

## Builders.flip_xy
**TBC**

## Builders.any
**TBC**

## Builders.all
**TBC**

## Builders.combine
**TBC**

## Builders.add
**TBC**

## Builders.subtract

## Builders.invert

## Builders.throttle_x

## Builders.throttle_y

## Builders.throttle_xy

## Builders.throttle_world_xy

## Builders.choose

## Builders.if_else

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