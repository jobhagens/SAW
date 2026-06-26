# User Manual: Self-Avoiding Walk (SAW) Simulator

## Scope
This manual is aimed at our Proffesor(s assinstants) but is free to be used by anyone who likes.

The documentation focuses on how to create and customize SAW's.

---

## Overview

### What is the SAW Simulator?
The SAW Simulator creates a path that never visits the same point, on a lattice of your choice.

These simulations are usefull for fields such as chemestry regarding polymers (for more information see the report).

### Features and Capabilities
* Create a SAW on multi-dimensional hypercubic grids (Square 2D and Cubic 3D).
* Create a SAW on hexagonal grids (Honeycomb).
* Calculate average distance from origin for walks.
* Visualisation of SAWs for 2- 3D Orthogonal lattices.

---

## Installation & Setup

To visualise the walks you will need to install matplotlib, all other packages automatically come installed with python.

To instal Matplotlib, open the **terminal**  and run either of the following commands (depending on which one you use):
```bash
pip install matplotlib
anaconda install matplotlib
```
If this doesn't work go to matplotlibs site and follow their guide (https://matplotlib.org/stable/install/index.html)

---

## Lattice/Grid Creation

Before starting a walk, you must create a grid(`Lattice`). The code supports two distinct grid systems:

### 1. Box/Cube Grids (`Orthogonal_Lattice`)
A standard grif of dimension n, just like $\mathbb{N}^n$ with 90 degree angles.

**Configuring of an Orthagonal Lattice** 
    You need to provide the number of cells you want and the number of neighbors. The dimension is automatically calculated based of off the number of neighbors (4 neighbors = 2D).

  The number of neighbors has to be a multiple of 2 (since that is the structure of an orthangonal lattice), and will be rounded down if it is not.

  *(Note: the amount of cells will be rounded down to a perfect i^n, addition of extra loose cells could be addad in the future)*

(0,0,0) is seen as the most bottomleft square, so any negative coordinate will always be uitside of the grid.

* Example of creating a 2D 6x6 latice (36 cells, 49 nodes) 
  ```python
  grid_2d = Orthogonal_Lattice(size=36, neighbors=4)
  ```
* Example of creating a 3D 5x5x5 latice (125 cells, 216 nodes)
  ```python
  grid_3d = Orthogonal_Lattice(size=125, neighbors=6)
  ```

### 2. Hexagonal Grids (`Honeycomb_Lattice`)
A honeycomb patern in 2d. Note that this has 3 possible move directions (excluding the negative version) so will be treated as 3 dimensional in the code.

Here again: the number of cells will be rounded down to make sure only full 'layers' will be created.

The inteded (2d) directions of the base vectors are ,

* (1,0,0) -> right

* (0,1,0) -> topright

* (1,0,0) -> topleft

These are good to know if you want to check the route yourself or want to implement a visualisation later on. (For more explanation of the use of base vectors, see report).

The code is written so that (0,0,0) is seen as the most bottomleft hex, which is good to know when choosing your origin. You could change the code so that (0,0,0) is seen as the middle by changing the "in_bounds()" function (as is seen in an older version of the code on Github).

* Example making a Hexagonal grid with 3 layers (assuming having only 1 hex means 1 layer)
  ```python
  hex_grid = Honeycomb_Lattice(size=19) 
  ```

---

## Running Simulations

Once your environment is built, use the `Self_Avoiding_Walk` Class to create a walk.

### Executing a Single Walk
Initialize the walk by pairing it with your grid and setting an `origin` of your choice. 

The origin should be a tuple consisting of n integers, the same as the dimension of your grid. Also make sure the coordinates of the origin are inside the boundries of the grid, or no walk will be generated. 

* Note that the new number of nodes in the final walk is one lower than the max_nodes because the origin is automatically added, this was chosen because it felt more intuitive, but could be changed to your liking. 
* Example of initializing a SAW with a 2d square grid starting at point (0,0) and then generating a walk of 20 steps

```python
# Create the simulation instance
sim = Self_Avoiding_Walk(lattice=grid_2d, origin=(0, 0))

# Generate a path of exactly 20 steps
path = sim.generate(max_nodes=20)
```


> If the engine gets trapped in a dead-end and cannot fulfill your requested target length, it **fails silently** and returns just your starting point (`[origin]`). This should only happen if your origin is outside of the grid, or your max_nodes or more than the size of the grid.

### Running Batch Runs
You can compute the average end-to-end straight-line distance of walks over multiple random iterations to observe its behaviour (as done in the report):

Give the size of the walk, and the amount of iterations to as input.

```python
# Calculates average displacement for 10-step walks across 100 iterations
avg_disp = sim.average_length(length_walk=10, iterations=100)
print(f"Average straight-line displacement: {avg_disp}")
```

---

## Visualizing Paths

The system uses Matplotlib to allow you to visualise the walks for 2-3D orthangonal walks.

### Displaying a Walk
Creates a graph using the coordinates of the walks to show the walk
* for 2D
```python
grid_2d.visualise_R2(path)
```

* for 3D
```python
grid_3d.visualise_R3(path)
```

### Changing the look
If you are not satified with the look, you can customize things like linethickness and -transparancy within the code(only 3D because it has no use in 2D) to fit the size of your walk.  
For larger walks use a lower transparancy and thickness, but ensure the line is still properly visible.
You can also change the amount of grid lines shown to your liking.

To change the amount of lines, change x/y/z ticks, to change transparancy change alpha ([0,1]), and for linethickness use linethickness (similair for 2D)

for example if you want a line every 2 values and a lower transprancy with only gridlines on the axis
```python
ax.set_xticks(range(0 ,x_bound + 1, 2))
ax.set_yticks(range(0, y_bound + 1, 2))
ax.set_zticks(range(0, z_bound + 1, 2))
ax.grid(which="both", alpha=0.25, axis="x")

```

---

## Full example

```python
# 1. Setup a 5x5x5 cube framework
grid = Orthogonal_Lattice(size=125, neighbors=6)

# 2. Attach simulation engine
sim = Self_Avoiding_Walk(lattice=grid, origin=(0, 0, 0))

# 3. Generate and visualize path
path = sim.generate(max_steps=25)
grid.visualise_R3(path)
```
