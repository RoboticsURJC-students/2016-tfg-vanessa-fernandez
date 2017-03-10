# Release Notes


# OMPL.app 1.2.1 (July 1, 2016)

- New simplified installation instructions. There is now also a [installation script](http://ompl.kavrakilab.org/install-ompl-ubuntu.sh) that will download and install OMPL and all its dependencies on Ubuntu 14.04, 15.10, and 16.04.
- Fixed python bindings for gcc5. Python bindings still take a [very long time](https://github.com/gccxml/pygccxml/issues/56) to generate.
- Misc. small bug fixes.


# OMPL.app 1.2.0 (June 20, 2016)

- C++11 is now **required**. A lot of Boost usage in older versions of OMPL has been replaced with C++11 STL equivalents.
- Added several new planners and improved existing planners:
  - Stable Sparse RRT, both a [geometric](\ref gSST) and a [control-based](\ref cSST) version. SST is an asymptotically near-optimal incremental version of RRT.
  - [Vector Field RRT](\ref gVFRRT), a tree-based motion planner that tries to minimize the so-called upstream cost of a path. The upstream cost is defined by an integral over a user-defined vector field.
  - [A bidirectional version of FMT](\ref gBFMT), small fixes in FMT.
  - A new version of [Expansive Space Trees](\ref gEST) that does not rely on projections to estimate sampling density. Instead, it estimates density directly in the configuration space. This is closer to the original paper and actually works really well! There is now also a [bidirectional version of EST](\ref gBiEST). The previous implementation of EST has been renamed to [ProjEST](\ref gProjEST).
- Minimum Boost version is now 1.54.


# OMPL.app 1.1.0 (October 28, 2015)

- Added several new and updated planners:
  - [Lightning](\ref Lightning): the Lightning Framework is a experienced-based motion planner that recalls from a database of previously generated paths the most similar one to the current planning problem and attempts to repair it, while at the same time planning from scratch in a different thread.
  - [Thunder](\ref Thunder): the Thunder Framework is essentially an improved version of Lightning. It stores previously generated paths in a combined roadmap, thereby offering more opportunities for reuse of partial paths. The roadmap is sparse while still guaranteeing asymptotic near-optimality. This is done by borrowing ideas from the SPARS algorithm.
  - [Informed RRT*](\ref gInformedRRTstar): a variant of RRT* that uses heuristics to bound the search for optimal solutions.
  - [Batch Informed Trees (BIT*)](\ref gBITstar): an anytime asymptotically optimal algorithm that uses heuristics to order and bound the search for optimal solutions.
  - An updated version of the Lower-Bound Tree Rapidly-expanding Random Tree (LBT-RRT) and a lazy version, LazyLBTRRT. These algorithms use Lifelong Planning A* and Dynamic Single-Source Shortest Path over graphs as subroutines, but these subroutines might be more generally useful in other algorithms as well.
  - An updated version of [TRRT](\ref gTRRT) as well as a bidirectional version of TRRT ([BiTRRT](\ref gBiTRRT))
  - An updated version of [FMT*](\ref gFMT) that, among other things, caches collision checks.
- [New web-based based version of OMPL.app!](http://omplapp.kavrakilab.org) The web app has all the functionality of the standalone GUI. In addition, it allows you to interactively construct benchmark jobs that can be submitted to a benchmark server. We have a public version of the web app and benchmarking server running at http://omplapp.kavrakilab.org, but the web app and benchmark server can also be run locally.
- There are two new concepts, ompl::base::InformedSampler and ompl::base::InformedStateSampler, that capture the idea of using information about the state space and the current solution cost to limit future search to a planning subproblem that contains all possibly better solutions. The ompl::base::PathLengthDirectInfSampler is derived from InformedStateSampler and can be used to limit sampling to only those states that can lead to a shorter path than the best-found solution so far. This sampler is used in ompl::geometric:InformedRRTstar and ompl::geometric::BITstar.
- The ompl::geometric::PathSimplifier can now also optimize a path with respect to a (sampleable) goal. This means, for example, that a solution path is no longer “stuck” with an awkward inverse kinematics solution for a goal.
- Added a `plannerarena` script to simplify running [Planner Arena](http://plannerarena.org) locally.
- Added a new planner termination conditions that allow one to terminate after a fixed number of iterations.
- The GNAT data structure for nearest neighbor queries has been updated and should be faster in general. There is now also non-threadsafe version of the GNAT data structure that is automatically selected for single-threaded planners. This version should be even faster.
- Added an option to turn off the path simplification in benchmarking.
- Added support for parametrized benchmarks. Planner Arena can show performance across values for a given parameter.
- Made it easier to get repeatable runs of an algorithm by enabling the user to set the seed of the *local* random number generators (i.e., not just the global seed).
- The [OMPL blog](http://ompl.kavrakilab.org/blog.html) is now [Jekyll](http://www.jekyllrb.com)-based and hosted as a [repository on GitHub](https://github.com/ompl/blog). If you have a project that uses OMPL, you can send us a pull request (please check with us first whether it would be appropriate for the blog before you write content).


# OMPL.app 1.0.0 (October 26, 2014)

- Added many new planners:
  - [Linear Temporal Logical Planner (LTLPlanner)](\ref cLTLPlanner): a planner that finds solutions for kinodynamic motion planning problems where the goal is specified by a Linear Temporal Logic (LTL) specification.
  - [Fast Marching Tree algorithm (FMT∗)](\ref gFMT): a new asymptotically optimal algorithm contributed by Marco Pavone's Autonomous Systems Laboratory at Stanford.
  - [Coupled Forest of Random Engrafting Search Trees (CForest)](\ref gCForest): a meta-planner that runs several instances of asymptotically optimal planners in different threads. When one thread finds a better solution path, the states along the path are passed on to the other threads.
  - [Anytime Path Shortening](\ref gAPS): a generic wrapper around one or more geometric motion planners that repeatedly applies [shortcutting](\ref ompl::geometric::PathSimplifier) and [hybridization](\ref ompl::geometric::PathHybridization) to a set of solution paths. Any number and combination of planners can be specified, each is run in a separate thread.
  - [LazyPRM](\ref gLazyPRM) / [LazyPRMstar](\ref gLazyPRMstar): not entirely new, but completely re-implemented.
- RRT* has a new option to periodically prune parts of the tree that are guaranteed *not* to contain the optimal solution. This idea was introduced in CForest, but it useful independently of the CForest parallelization. Although pruning is almost always useful, it is *disabled* by default to preserve the original behavior.
- Created consistent behavior across all planners that can optimize paths. Calls to the solve method of RRT*, PRM*, SPARS, SPARStwo, and LBTRRT will terminate when (1) the planner termination condition is true or (2) the optimization objective is satisfied. To make these planners terminate when *any* solution is found, you can set the cost threshold for the optimization objective to, e.g., OptimizationObjective::infiniteCost(). For most of these planners, asymptotic (approximate) optimality is only guaranteed when using the PathLengthOptimizationObjective class.
- Most control-based planners can now use steering functions. The user simply needs to override ompl::control::StatePropagator::steer() and ompl::control::StatePropagator::canSteer() in a derived class.
- Several improvements to benchmarking functionality:
  - [Planner Arena](http://plannerarena.org) has been relaunched and can be used to interactively visualize benchmark results.
  - ompl_benchmark_statistics.py can now also parse MoveIt! benchmark log files using the flag `--moveit`.
  - The config files for ompl_benchmark and the OMPL.app GUI can now be used to specify control-based problems and optimization objectives.
- Added ompl::tools::PlannerMonitor class, which periodically prints planner progress properties in a separate thread. Useful for developing / debugging new planners.
- Updated Py++ toolchain. If you previously installed Py++ and have trouble generating the OMPL Python bindings, you may need to run "make installpyplusplus" again.
- Minimum Boost version is now 1.48 and minimum CMake version is now 2.8.7.
- Bug fixes.


# OMPL.app 0.14.2 (May 23, 2014)

- Changed the [benchmark database schema](\ref benchmark_database) to make it easier to create your own plots.
- Real-valued state components are now also stored when exporting PlannerData objects in [GraphML](http://graphml.graphdrawing.org) format.
- Bug fixes.
- Documentation fixes.
- New menu option in the OMPL.app GUI to save the roadmap/tree created by a planner in GraphML format.
- Update FCL bindings to be compatible with FCL 0.3.0.


# OMPL.app 0.14.1 (January 23, 2014)

- Update PlannerData to use new cost infrastructure.
- Bug fixes.


# OMPL.app 0.14.0 (December 20, 2013)

- Added new cost infrastructure to plan optimal paths with respect to arbitrary cost functions. Several common cost objective functions have been defined (such as path length and mechanical work) and new ones can easily be defined.
- RRT*, PRM*, TRRT, and PRM have been updated to use this cost infrastructure.
- BallTreeRRT* has been removed since it assumed that the path cost is equal to the path length.
- Added two new planners:
    - [Lower Bound Tree RRT (LBTRRT)](\ref gLBTRRT), a near asymptotically-optimal incremental sampling-based motion planning algorithm.
    - [Search Tree with Resolution Independent Density Estimation (STRIDE)](\ref gSTRIDE), an EST-like planner that uses an extension of the GNAT nearest neighbor data structure that supports sampling states inversely proportional to the density of previously sampled states.
- Added support for MORSE. Through a plugin you can use OMPL with Blender and MORSE to plan kinodynamic paths. See [the gallery](gallery.html) for some examples.
- Added functionality to the Benchmark class to keep track of user-specified properties at regular intervals. This is especially useful for asymptotically/approximately optimal or anytime planners.
- Added more [demo programs](group__demos.html).
- Added several new planning problems for use with the GUI and ompl_benchmark program.
- Updated gccxml snapshot. If you use gcc 4.8 and the OMPL Python bindings, you need to run "make installpyplusplus" again.
- Bug fixes.


# OMPL.app 0.13.0 (August 5, 2013)

- Added several new planners:
    - [SPARS](\ref gSPARS) and [SPARS2](\ref gSPARStwo) are roadmap-based planners that operate similarly to Visibility-based PRM, but provide asymptotic near-optimality guarantees.
    - Path-Directed Subdivision Tree (PDST). There are actually two versions: [one for geometric planning](\ref gPDST) and [one for control-based planning](\ref cPDST).
    - [Lazy PRM](\ref gLazyPRM), a variant that lazily evaluates the validity of nodes and edges in a roadmap.
- Various improvements and bug fixes in the RRTstar and BallTreeRRTstar planners.
- Various optimizations in the Syclop planner.
- The Syclop planner can now operate over triangular decompositions. This is enabled if the [triangle](http://www.cs.cmu.edu/~quake/triangle.html) package is installed.
- Significantly reworked and extended unit tests for planning algorithms.
- Added method to extract a ompl::base::StateStorage object from ompl::base::PlannerData.
- State spaces can indicate whether they are metric.
- Certain path shortcutting techniques are disabled for non-metric state spaces during path simplification.
- Extended ProblemDefinition API to support path optimizing planners.
- Added printAsMatrix method to ompl::geometric::PathGeometric and ompl::control::PathControl to facilitate plotting of paths. Added a [tutorial on path visualization](pathVisualization.html).
- The path format produced by the printAsMatrix method is also used by the [OMPL.app GUI](gui.html).
- Added more [demo programs](group__demos.html).
- Use the officially released version of [OdeInt](http://www.boost.org/libs/numeric/odeint) that comes with Boost 1.53 for numerical integration. The bundled version of OdeInt is not used or installed if the user has Boost >= 1.53 installed.
- Updated Py++ toolchain (gccxml, pygccxml, pyplusplus). If you use gcc 4.7/4.8 or Boost 1.54, you need to run "make installpyplusplus" again.
- Various fixes for Python 3.x. The python bindings and GUI can now be used with Python 3.x.
- Bug fixes.


# OMPL.app 0.12.2 (January 22, 2013)

- Initial support for FLANN, a library for approximate nearest neighbors.
- Documentation updates.
- Bug fixes.


# OMPL.app 0.12.1 (12/12/12)

- Stricter checking for FCL and CCD versions, to avoid link problems with ROS Fuerte.
- Bug fixes.


# OMPL.app 0.12.0 (November 20, 2012)

- Added representation of costs to ompl::base::StateValidityChecker.
- Added the notion of objectives to be optimized: ompl::base::OptimizationObjective.
- Added \ref gTRRT "T-RRT", a planner for planning low-cost paths.
- Planners now have the option to report the non-existence of a solution (ompl::base::SolutionNonExistenceProof).
- Replaced PQP with FCL as the default collision checker.
- In the GUI the trees/roadmap produced by a planner can now be visualized.
- Improved infrastructure for creating python bindings for planners (see [tutorial](pybindingsPlanner.html) for details).
- The GUI widgets for planners and their parameters are automatically constructed, which should make it easier to add new planners to the GUI.
- Documentation updates.
- Bug fixes.


# OMPL.app 0.11.1 (July 26, 2012)

- Fixed bug in RRT* where nearest neighbor radius shrinks too fast.
- Added option for versioned installs: multiple versions of OMPL.app can be installed simultaneously (disabled by default). This is enabled by running `cmake -DOMPL_VERSIONED_INSTALL=ON`.
- Added support for Assimp 3 (Assimp 2 is still supported as well).
- Bug and documentation fixes.


# OMPL.app 0.11.0 (June 30, 2012)

- PlannerData now uses the Boost Graph Library (BGL). This offers much more flexibility and power in the inspection of planner data structures. Rather than storing only the data common to all planners, each planner can store its own (meta)data per vertex and edge in a graph. All graph algorithms that exists in BGL can be applied directly.
- Added PlannerDataStorage object for serialization/deserialization of PlannerData
- Logging mechanism is now using macros instead of the msg::Interface class. The file name and line number where a message originated is printed out for every message type.
- Changed the return type for Planner::solve from bool to an enumeration (base::PlannerStatus).  This allows for more refined failure/success reporting.  A cast to bool exists for a quick success check.
- Added serialization for controls and a signature for the control space.
- Moved solution paths from Goal to ProblemDefinition.
- Implementation of PRM is now threaded (one thread for growing the roadmap, one thread for monitoring whether the problem is solved).
- Improvements to sampleUniformNear() for SO3.
- Options were added to the benchmark configuration file syntax to save solution paths. Either all paths, no paths, or the shortest path can be saved.
- A TeamCity Continuous Integration server has been set up at http://teamcity.kavrakilab.org.
- The public repository, issue tracker, and downloads have been moved to Bitbucket. Sourceforge is being phased out for these services.
- Reduced dependencies: GTest has been replaced by Boost Test, subversion and CVS are no longer needed to get some optional dependencies.
- Bug fixes.


# OMPL.app 0.10.2 (March 21, 2012)

- Created a blog to highlight new features.
- Added support for multi-robot rigid-body motion planning.
- Updated FCL (which now uses FLANN instead of ANN for nearest neighbors)
- Bug fixes.


# OMPL.app 0.10.1 (February 27, 2012)

- Added representation of discrete control spaces.
- Fixes for XCode 4.3 when compiling the Python bindings.
- Updates to support Python 3.x in existing demo programs.
- Removed mutex from FCL collision checking wrapper. The newer version of the FCL library is thread-safe.
- Bug fixes.


# OMPL.app 0.10.0 (February 16, 2012)

- Minimum Boost version is now 1.44.
- Added a primer on sampling-based motion planning and OMPL.
- Added an implementation of SyCLoP, a new multilayered meta-planner that combines discrete search with a sampling-based motion planner. Implementations of Syclop with RRT and EST as the low-level planners are provided.
- Added an implementation of EST for planning with controls.
- New generic interface for parameter settings for almost anything that is user-configurable. This was done to simplify reading parameters from an input file (see next bullet).
- New benchmark program that allows one to easily specify benchmark problems with simple configuration files.
- New state spaces for Dubins vehicles and Reeds-Shepp vehicles. This allows one to use any geometric planner to compute feasible paths for such vehicles. See the demo program demo_GeometricCarPlanning.
- Added state serialization, deserialization, state space signatures.
- Added the notion of DirectedControlSampler.
- Added path hybridization, a technique for combining several solutions to a motion planning query to form a better bath. We also introduced a utility class called ParallelPlan that runs several planner instances in parallel and (optionally) uses path hybridization.
- Added a more advanced path shortcutting method.
- Added support for FCL collision checking library. PQP is actually still faster, and is therefore still the default.
- All versions of EST and SBL use the new PDF class to sample more efficiently from empirical probability distribution functions.
- Added a wrapper for boost::numeric::odeint to support high-order numerical integration of ordinary differential equations.
- Fixed issues with static variables for console.
- Added RPY sampling to the RNG class.
- Made some public members protected for GoalState and GoalStates; improvements to GoalLazySamples.
- Added support for multiple solution paths in a Goal datastructure.
- Added StateSpace::getValueAddressAtLocation() to allow faster indexing in states using state space names.
- The C++ code switched to the preferred boost::function syntax. A general method for exposing boost::function objects to Python was added, but this requires a patch to pygccxml.
- To make installing and patching Py++, pygccml, and gccxml easier there is now a script called installPyPlusPlus.sh that does this. One can call this script after running cmake by typing “make installpyplusplus”.


# OMPL.app 0.9.5 (October 4, 2011)

- Added control sampler functions that allow specifying an intended direction of propagation
- Made python, Boost.Python, PyOpenGL, and PyQt optional; the omplapp library is useful even without the GUI or python bindings.
- Removed pre-generated Python binding code. It turns out that they are dependent on the particular Boost version.
- Bug fixes.


# OMPL.app 0.9.4 (August 16, 2011)

- Renamed StateManifold to StateSpace and ControlManifold to ControlSpace
- Added RRTstar contribution
- Added GNAT nearest neighbors datastructure
- Added representation of a discrete state space (DiscreteStateSpace)
- Added representation of probability density functions (PDF)
- Replaced the implementation of BasicPRM with PRM. Thanks to James Marble, the new implementation uses BGL.
- Moved state propagation functionality from ControlSpace to a separate StatePropagator class
- Added SimpleSetup-derived classes for several control-based systems: kinematic car, Reeds-Shepp car, Dubins car, dynamic car, blimp, and quadrotor. Made them accessible through the GUI.
- Added isStraightLinePathValid() to PlannerDefinition
- Using boost ublas for real vector projections
- Add sanity checks for state spaces
- Benchmarked planners are now run in a separate thread (and termination conditions are evaluated separately, to detect crashes)
- Added getType() for Goal and replaced getType() for planners by getSpecs()
- Generalized planner termination conditions. The user can now call terminate() at any time to signal a planner it should stop its computation
- Improvements to control::KPIECE1, so that it considers goal biasing more appropriately
- Move code for extracting machine properties from util/ to benchmark/
- Documentation fixes


# OMPL.app 0.9.3 (May 2, 2011)

- Added support for visualizing internal planner data
- Planning for 2D environments as well (SE2)
- Significant reorganization of code in OMPL.app
- Added path smoothing with splines
- Added a bi-directional implementation of KPIECE (BKPIECE)
- Support for computation of clearance and gradients that move away from invalid regions
- Separate "magic constants" to a separate, visible, location
- A number of bug fixes


# OMPL.app 0.9.2 (February 21, 2011)

- Updates to operations on states: indexing in states can be done using state spaces or using integer values
- Bug fixes for planning with controls
- Minor doc improvements


# OMPL.app 0.9.1 (December 17, 2010)

- Minor updates to build system

# OMPL.app 0.9.0 (December 15, 2010; initial release)

- Implementations of many state-of-the-art sampling-based motion planning algorithms. For purely geometric planning, there are implementations of KPIECE, SBL, RRT, RRT Connect, EST, PRM, Lazy RRT, and others. For planning with differential constraints there are implementations of KPIECE and RRT. Addition of new planners poses very few constraints on the added code.
- A flexible mechanism for constructing arbitrarily complex configuration spaces and control spaces from simpler ones.
- A general method of defining goals: as states, as regions in configuration space, or implicitly.
- Various sampling strategies and an easy way to add other ones.
- Automatic selection of reasonable default parameters. Performance can be improved by tuning parameters, but solutions can be obtained without setting any parameters.
- GUI for rigid body motion planning.
- Support for planning with the Open Dynamics Engine, a popular physics simulator.
- Tools for systematic, large-scale benchmarking.