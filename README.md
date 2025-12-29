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
V_i = {0, 1, ... , i - 1} ⇒ V_8 = {0, 1, 2, 3, 4 ,5 ,6 ,7}
```
We can also refactor the set by defining a new object that holds an xyz coordinate:  
```
v = {x , y, z}
```
The set of vertices (V) becomes:
```
V_i = {v_0, v_1, ... , v_(i - 1)}
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
  {x_a, y_a, z_a},  // from corner (v_0)
  {x_b, y_a, z_a},
  {x_b, y_b, z_a},
  {x_a, y_b, z_a},
  {x_a, y_a, z_b},
  {x_b, y_a, z_b},
  {x_b, y_b, z_b},
  {x_a, y_b, z_b}   // to corner (v_7)
}
```
This gives us the vertex set for a single unrotated cuboid.  
But Blockbench elements may have a "rotation" field.
```
"rotation": {
  "origin": [o_x, o_y, o_z],
  "axis": "x" | "y" | "z",
  "angle": degrees
}
```
When the element has rotation, we need to transform each of the 8 vertices around a given origin using the standard rotation matrix for a specified axis.  
For each raw vertex v_i generated above:  
```
v_i' = R(θ) * (v_i - o) + o
```
1. Translate the origin.  
```
dv_i = v_i - o
```
2. Apply the rotation (eg. Around the X-Axis):  
<img width="868" height="533" alt="image" src="https://github.com/user-attachments/assets/5d07f76b-6ed7-47ce-8334-e53c6626c9cc" />  <br/>
Mathematical notation:  
<img width="237" height="66" alt="rotation-matrix-around-x" src="https://github.com/user-attachments/assets/c3b5ed42-a18e-430a-b2cc-789885d722d0" />  <br/>
Pseudocode:  
```
x' = dx
y' = dy * cos(θ) - dz * sin(θ)
z' = dy * sin(θ) + dz * cos(θ)
```
3. Translate back.  
```
v_i' = {x' + o_x, y' + o_y, z' + o_z}
```
The resulting 8 transformed vertices v_0' … v_7' replace the axis-aligned ones in the vertex set.  
After processing all elements in the model, we collect every transformed vertex into the global vertex list:  
```
VS = [V_0', V_1', ..., V_(n - 1)']   // n = number of elements
```
Each element contributes exactly 8 vertices (even if shared in theory, we duplicate for simplicity in this basic renderer).



