# Composites Discoverer: A Computational Tool for the Optimal Design and Discovery of Multi-phase Composite Materials

## Overview
The library utilizes the tightest theoretical bounds on the effective properties of composite materials with unspecified microstructure – the Hashin-Shtrikman bounds – to identify candidate theoretical materials, find real materials that are close to the candidates, and determine the optimal volume fractions for each of the constituents in the resulting composite.

A genetic algorithm is used to optimize over the user-specified design space. The algorithm simultaneously minimizes absolute error from the desired composite properties and optimally distributes loads across constituent phases. Once the genetic algorithm has returned theoretical candidate materials, `hashin_shtrikman_mp` searches for real materials in the [Materials Project](https://next-gen.materialsproject.org/) database with properties close to those suggested by the genetic algorithm.

The library has been designed to handle 2- to 10-phase composite design.

***Aim***
`hashin_shtrikman_mp` is a Python library which recommends optimal material combinations to achieve desired composite properties. Composites are ubiquitous in engineering, as they often exhibit enhanced material properties as compared to their individual constituents. This library is intended to be a tool for materials designers who want to explore a new space of materials without incurring huge capital cost.

---
## Installation (🛑 under construction 🛑)
`hashin_shtrikan_mp` can be installed from [PyPi source](https://pypi.org/project/hashin_shtrikman_mp/) by running:
```
pip install hashin_shtrikman_mp
```

It can also be installed by cloning this repository, then following in the root of the repository:
```
pip install .
```

### Documentation (🛑 under construction 🛑)
Detailed documentation for this library can be found [here](https://mcgalcode.github.io/hashin_shtrikman_mp/)

### Jupyter Notebook Examples (🛑 under construction 🛑)
Example notebooks are included in [examples](https://github.com/carlacupcake/hashin_shtrikman_mp/tree/master/examples).

### Implementation Notes
- Ensure you have valid credentials for the Materials Project API, which you can find by registering yourself with Materials Project – [https://next-gen.materialsproject.org/](https://next-gen.materialsproject.org/).
- Optimization parameters (number of parents, children, etc.) should be chosen based on the complexity of the desired material properties and computational resources.
- The genetic algorithm's efficiency and effectiveness can vary greatly based on the optimization parameters and the definition of the cost function. Using defaults is recommended.
- Visualization of cost versus generation can provide insights into the convergence behavior of the genetic algorithm. Expect that the exact shape of the convergence plot will change every time the algorithm is run, due to the stocahstic nature of the algorithm.

### Miscallaneous features
**mpi4py support is added to append final_dict** 🛑 <-- still true ?? 🛑

To take advantage of `mpi` parallelization, one can run the following:
```
pip install mpi4py
```

In case installation via `pip` fails, you can use `brew` + `pip` instead:
```
brew install mpi4py
pip install mpi4py
```

Then run: 🛑 <-- THIS SCRIPT NO LONGER EXISTS 🛑
```
mpiexec -n 4 python orp_test.py
```

---

## Workflow

### User Input
- **Collect User Input** and instantiate a `UserInput` object with 1) the number of constituent materials desired in the composite, 2) the desired ultimate material properties and 3) upper and lower search bounds for the properties of each constituent material.

### Optimization
- **Instantiate an `Optimizer` Object** with Materials Project API credentials and user input.
- **(Optional) Set Optimization Parameters**: The genetic algorithm optimization requires values for the number of parents, children, members in a generation, number of generations, and weights for absolute error and load distribution. It is recommended to use the default settings.
- **Set Initial Population**: In each generation of the genetic algorithm, instantiate a `Population` object with the optimization parameters defined in the previous step. Each member of the population represents a candidate set of materials and their respective volume fractions in the composite.
- **Random Property Assignment**: Randomly property values and volume fractions to each member of the population using `Population.set_random_values`. Random values are constrained by the bounds provided by the user and by the necessity that the volume fractions sum to unity.
- **Evaluate Fitness**: Evaluate each member according to a cost function which penalizes deviations from desired properties and which penalizes uneven distribution of load. Do this by creating an instance of `Member` for each member in `Population` and calling `Member.get_cost`. This concludes generation 1.
- **Select Top Performers**: Sort the members by cost. A lower cost corresponds to a stronger performer and a higher cost to a weak performer. Retain the top `num_parents` members and discard the rest.
- **Breed and Produce Offspring**: Pairwise mate the top `num_parents` members to produce `num_kids` new members. Once again using `Population.set_random_values`, augment the population with new, random members to maintain the population size at `num_members`.
- **Evaluate Fitness of New Generation**: Evaluate each member according by the same cost function. This concludes generation 2.
- **Iterate Over Generations**: Repeat the selection of top performers, breeding, and fitness evaluation process for `num_generations`.

### Visualization and Match Finding
- **Obtain Convergence Plot**: Observe the monotonic decrease of the lowest cost observed across the population as the generations pass.
- **Recommend Theoretical Candidates**: After the final generation, for each of the top composite candidates display a table of 1) material properties for each constituent phase, 2) volume fractions for each constituent phase, and 3) the cost of that theoretical candidate.
- **Create a Material Properties Dictionary** keyed by `mp_ids` and their corresponding material properties of interest, gathered using the MP-API. The dictionary will be comprised of real materials that closely resemble the theoretical materials recommended by the genetic algorithm.
- **Create Populations of Real Composite Candidates**: For each set of candidate constituent materials, create a population by varying only the volume fractions of the composite constituents.
- **Find the Optimal Volume Fractions** by evaluating the  population with the same cost function used previously.
- **Display of Top-Performing Candidates**: Repeat the process for all possible combinations of materials and display the top-performers along with their volume fractions.
- **For 2-, 3-, and 4-phase Composites** view the phase diagram for each property of interest and view how changing constituent volume fractions changes the effective composite property.

---

## Example Visualizations


***Phase Diagram of Thermal Conductivity for 2-phase Composite***
![2phase](https://github.com/user-attachments/assets/00c7c051-f974-4437-9490-e08a6d8afc1c)

***Phase Diagram of Thermal Conductivity for 3-phase Composite***
![3phase](https://github.com/user-attachments/assets/a375ff79-1c54-463f-92a2-c294b96d159c)

***Phase Diagram of Thermal Conductivity for 4-phase Composite***
![4phase](https://github.com/user-attachments/assets/df5b06fc-b64e-4ad4-82df-52c235211d62)
