# Small Normalized Object Binary
File format for storing triangle meshes in a compact way


### Layout:  
4 bytes magic number: snob  
1 byte version number  
3 bytes flags  
4 bytes rescale float (if enabled)  
2 bytes set count per octant  (or 1 if set compress or vert compress enabled)  
2 bytes vert count (or 1 if vert compress enabled)  
2 bytes transformed vert count?  
2 bytes tri count  
2 bytes quad count  
2 bytes tranformed face count?

3 bytes per set (x,y,z)  
sets encode pos, texture coords, and normals  

6 bytes per vert (s0, s1, s2)  
s0 = pos  
s1 = tc (ignore z)  
s2 = normal   

6 bytes per face (v0, v1, v2)  

### Octant layout:
|Oct|x|y|z|
|---|---|-|-|
|0|+|+|+|
|1|+|+|-|
|2|+|-|+|
|3|+|-|-|
|4|-|+|+|
|5|-|+|-|
|6|-|-|+|
|7|-|-|-|

### flag use cases:  
signed normalized - indicate bytes are signed rather than unsigned. Only one octant is needed. values range from -1.0 to 1.0 (-127 to 127).  
set compress - max of 256 sets, only 3 bytes needed per vert  
vert compress - max of 256 verts, only 3 bytes needed per face  
face compress? - max of 256 faces (only use is reducing face count in header by 1 byte - not worth it?)  
include tc - whether or not to include tc value in vertex - reduce size by 2 bytes per vert  
tc compress - indicate all tc are within first 256 sets, only 1 byte per tc  
include normal - indeicate whether or not to include normal value in vertex - reduce size by 2 bytes per vert  
normal compress - indicate all normals are within first 32 sets of given octant, only 1 byte per normal (first 3 bits = octant, last 5 bits = index)  
octant compress - max 256 verts per octant  
rescale - indicate addtional 4 header bytes that store a float used to rescale to intended size  


Notes:  
All values are big endian and unsigned  
Total set count must not exceed 2^16  
