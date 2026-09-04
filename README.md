What is this?
A from-scratch C++ Rubik's Cube engine that models the cube in three different internal representations, implements four graph search algorithms to solve it, and uses a Korf-style Pattern Database (IDA*) as the primary solver for optimal solutions. Also includes a physical cube scanner that reads a real cube's state via webcam and feeds it directly into the solver.

Features

4 Solving Algorithms — DFS, BFS, IDDFS, and IDA* with admissible heuristic
3 Cube Representations — 3D array, 1D array, and Bitboard (each with different performance trade-offs)
Corner Pattern Database — Pre-computed lower-bound heuristic for IDA*, enabling near-optimal solving of deeply shuffled cubes
Physical Cube Scanner — Webcam-based scanner (CubeScanner) that reads a real cube's face colours and constructs the internal state automatically
Full Move Set — All 18 standard moves (U, D, L, R, F, B and their primes) implemented across all representations
Equality, Hashing & Assignment — Custom hash functions per representation enabling use in unordered_map for BFS/IDA*
CMake Build System — Clean, cross-platform build configuration


Algorithms
AlgorithmClassBest ForNotesDFSDFSSolverShallow shuffles (≤8 moves)Depth-limited; fast but not optimalBFSBFSSolverShallow shuffles (≤6 moves)Optimal but high memory usageIDDFSIDDFSSolverMedium shuffles (≤7 moves)Optimal, low memory; slower than BFSIDA*IDAstarSolverDeep shuffles (any depth)Optimal; uses Corner DB as heuristic
The IDA* solver with Corner Pattern Database is the primary solver — it finds optimal solutions for arbitrarily shuffled cubes by using precomputed corner distances as an admissible heuristic to prune the search space.

Cube Representations
RepresentationClassDescription3D ArrayRubiksCube3dArrayIntuitive 6×3×3 face representation1D ArrayRubiksCube1dArrayFlattened array; faster iterationBitboardRubiksCubeBitboardBit-packed state; minimal memory, fastest hashing — used by default in IDA*

Project Structure
rubik_cube/
├── Model/
│   ├── RubiksCube.h              # Abstract base class — move enum, print, isSolved
│   ├── RubiksCube3dArray.cpp     # 6×3×3 array representation
│   ├── RubiksCube1dArray.cpp     # Flattened 1D array representation
│   └── RubiksCubeBitboard.cpp   # Bitboard representation (used in IDA*)
├── Solver/
│   ├── DFSSolver.h               # Depth-First Search solver
│   ├── BFSSolver.h               # Breadth-First Search solver
│   ├── IDDFSSolver.h             # Iterative Deepening DFS solver
│   └── IDAstarSolver.h          # IDA* solver with pattern DB heuristic
├── PatternDatabases/
│   ├── CornerPatternDatabase.h   # Corner DB lookup (8 corners × orientations)
│   └── CornerDBMaker.h          # BFS to pre-compute and persist the Corner DB
├── Databases/
│   └── cornerDepth5V1.txt        # Pre-computed corner pattern database file
├── Scanner/
│   └── CubeScanner.h            # Webcam-based physical cube state reader
├── bits/
│   └── (bit manipulation utilities)
├── main.cpp
└── CMakeLists.txt

Getting Started
Prerequisites

C++17 or later
CMake 3.15+
OpenCV (for the CubeScanner webcam feature)

Build
bash# Clone the repository
git clone https://github.com/Nitin75408/Rubrik_cube-_solver.git
cd rubik_cube

# Create build directory
mkdir build && cd build

# Configure and build
cmake ..
make
Run
bash./rubik_cube
By default, main.cpp runs the CubeScanner + IDA* pipeline — it scans a physical cube via webcam and outputs the solution moves.
To test individual solvers, uncomment the relevant section in main.cpp (each algorithm has a clearly labelled test block).

How the IDA* Solver Works

Pre-computation — CornerDBMaker runs a BFS from the solved state over all 8 corner cubies and stores the minimum number of moves to solve each corner configuration to disk
Heuristic — At each node during IDA*, the Corner Pattern Database provides a lower-bound estimate of remaining moves (admissible heuristic → guarantees optimal solution)
IDA* Search — Iteratively deepens the cost threshold, pruning branches where current_depth + heuristic > threshold
Output — Returns the sequence of moves (e.g. U R F' L2 D B') that solves the cube optimally


Example Output
Current State:
W W W
W W W
W W W
...

Shuffle Moves: U R F L2 D'
Solution Moves: D L2 F' R' U'

Solved State:
W W W
W W W
W W W
...

Screenshots

Add a screenshot or GIF of the scanner reading a physical cube and outputting solution moves


Author
Nitin https://github.com/Nitin75408/Rubrik_cube-_solver.git 
