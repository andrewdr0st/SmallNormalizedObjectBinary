# Small Normalized Object Binary
File format for storing triangle meshes in a compact way


Layout:
4 bytes magic number: snob
1 byte version number
3 bytes flags
2 bytes octant 0 set count
2 bytes octant 1 set count
2 bytes octant 2 set count
2 bytes octant 3 set count
2 bytes octant 4 set count
2 bytes octant 5 set count
2 bytes octant 6 set count
2 bytes octant 7 set count
2 bytes vert count
2 bytes face count

3 bytes per set (x,y,z)
sets encode pos, texture coords, and normals

6 bytes per vert (s0, s1, s2)
s0 = pos
s1 = tc (ignore z)
s2 = normal

6 bytes per face (v0, v1, v2)


Notes:
All values are big endian and unsigned
Total set count must not exceed 2^16

octant layout:
o|x|y|z
-------
0|+|+|+
1|+|+|-
2|+|-|+
3|+|-|-
4|-|+|+
5|-|+|-
6|-|-|+
7|-|-|-

flag use cases:
set compress - max of 256 sets, only 3 bytes needed per vert
vert compress - max of 256 verts, only 3 bytes needed per face
face compress? - max of 256 faces (only use is reducing face count in header by 1 byte - not worth it?)
include tc - whether or not to include tc value in vertex - reduce size by 2 bytes per vert
tc compress - indicate all tc are within first 256 sets, only 1 byte per tc
include normal - indeicate whether or not to include normal value in vertex - reduce size by 2 bytes per vert
normal compress - indicate all normals are within first 32 sets of given octant, only 1 byte per normal (first 3 bits = octant, last 5 bits = index)
octant compress - max 256 verts per octant
rescale? - indicate addtional 4 header bytes that store a float used to rescale to intended size

final byte could be used to indicate mirroring/rotation for a given octant

how could mirroring work?
o1 mirror o0 ->
all sets that do not lie on the plane separating o0 and o1 are mirrored across the plane into o1
accounting for index?
let s0 s1 s2 in o0 s0 lies on plane between o0 and o1
o1 will contain s3 and s4, mirrored versions of s1 and s2
first set in o2 is s5, even though it is only 4th set in file (s3)

if mirror flag is set for an octant, only 1 byte is needed for length in header. that byte represents which octant it should mirror, rather than a set count
to mirror, multiply all x y z by -1 if sign for the octant differs in the dimension (eg o1 -> o2 = +x -y -z)

