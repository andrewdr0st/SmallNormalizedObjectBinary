# Small Normalized Object Binary
File format for storing triangle meshes in a compact way


Layout:
4 bytes magic number: snob
1 byte version number
1 byte flags
2 bytes q0 vert count
2 bytes q1 vert count
2 bytes q2 vert count
2 bytes q3 vert count
2 bytes q4 vert count
2 bytes q5 vert count
2 bytes q6 vert count
2 bytes q7 vert count
2 bytes texture coord count
2 bytes face count

3 bytes per vert (x,y,z)
2 bytes per tex coord (u,v)

18 bytes per face (v0, v1, v2, tc0, tc1, tc2, n0, n1, n2)
potential to compress to 16 bytes?
or only 8 if tc and normals are not needed?



Notes:
All values are big endian and unsigned
Normals are stored as verticies
Total vertex count must not exceed 2^16


