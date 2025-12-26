## References
- **Tsoding** (About the formula for 3d graphics):
https://www.youtube.com/watch?v=qjWkNZ0SXfo
- **Rotation matrix derivation video**:
https://www.youtube.com/watch?v=EZufiIwwqFA
- **Megumin staff** (You can find the json file inside the zip file):
https://modrinth.com/resourcepack/megumin-rework
- **OpenCourseWare | Chapter 5: Graph theory**:
https://ocw.mit.edu/courses/6-042j-mathematics-for-computer-science-fall-2010/resources/mit6_042jf10_chap05/
- **Perspective projection**:
https://en.wikipedia.org/wiki/3D_projection?referrer=grok.com#Perspective_projection

## How to convert Blockbench models to a single graph
(no subgraphs needed. since it only projects one model)  

<img width="150" height="170" alt="cube graph" src="https://github.com/user-attachments/assets/2de09232-055d-4a2f-b055-f2ef20a0fc1c" />

If we were to translate a simple cube into a graph by using graph theory,  
Notice that, we have 8 corners which we can directly notate as:  
```
V_n = {1, 2, ... , n} ⇒ V_8 = {1, 2, 3, 4 ,5 ,6 ,7 ,8}
```
We can also refactor the set by defining a new object that holds an xyz coordinate:  
```
v_n = {x , y, z}
```
The set of vertices (V) becomes:
```
V_n = {v_1, v_2, ... , v_n}
```
Now we defined the cuboid in a graph form. We can now move onto translating two points (min & max) into 8 vertices.  
In a Blockbench model JSON, each element is defined as an axis-aligned cuboid using exactly two opposite corners:  
```
"from": [minX, minY, minZ],
"to": [maxX, maxY, maxZ]
```
Where "from" is the minimum corner (one vertex) and "to" is the maximum corner (the diagonally opposite vertex).
```
a = {x, y, z}
b = {x, y, z}
```
These two points define the bounding box of the cuboid. In graph theory terms, they are just two vertices in the full set V, but they allow us to create the remaining six vertices systematically.  

<img width="310" height="324" alt="coordinates" src="https://github.com/user-attachments/assets/9d35c83d-4ebd-4d20-9d88-9cd58d8d8ef0" />


The complete set of 8 vertices for one cuboid element can be constructed by taking all combinations of the min/max coordinates along each axis:  
```
V_8 = {
  v_1: {x_a, y_a, z_a},  // from corner
  v_2: {x_b, y_a, z_a},
  v_3: {x_b, y_b, z_a},
  v_4: {x_a, y_b, z_a},
  v_5: {x_a, y_a, z_b},
  v_6: {x_b, y_a, z_b},
  v_7: {x_b, y_b, z_b},
  v_8: {x_a, y_b, z_b}   // to corner
}
```
This gives us the vertex set for a single unrotated cuboid.  
