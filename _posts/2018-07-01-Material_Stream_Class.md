---
layout: post
title: Developing a Material Stream Class in Python using CoolProp  
#featured-img: phase-diagram
mathjax: true
categories: Material-Balance CoolProp Thermophysical Python Object-Oriented-Programming
---

# Material Stream in Process Modelling

Any chemical and biochemical process consists of material streams interacting with a series of unit operations and unit processes. Information about material stream's state and associated properties is passed on to these operations. These operations than alter the stream's state and its properties. Hence, for the process modeling of any chemical or biochemical process, the state and properties of material streams are the essential information needed to model the unit operations and unit processes.

Using python object-oriented programming the information and functionality of a material stream can be bundled in a python class. Creating an instance of the stream class as an object makes it easy to deploy in the process model and interact with unit operation models. Instances of the class object can be used to model all the process streams in the process model.

# Use of [CoolProp](http://www.coolprop.org/index.html)

[CoolProp](http://www.coolprop.org/index.html) is a an open source thermodyamic databank. It has a wrapper interface available for python. It can be used to get the thermodynamic and pyhsical properties of the material stream. More details about use of CoolProp and its python interface to get themophysical properties is given here [CoolProp — A Cool Package for Thermophysical Properties]({% post_url 2018-06-29-CoolProp-CoolMethods %}).

# Material Stream Class

Functionality needed of a material stream object varies with the type of process and detail of the process model. Hence, developing a generic material class streams that can be used in all sorts of chemical and biochemical applications is a tough task. However, a basic skeleton class that contains the essential elements can be constructed --- that can also be extended on the need basis. Following are the essential physical variables needed for process modeling of a material stream:

- State variables: temperature, pressure, composition, mass or mass flowrate.
- Thermodynamic properties: specific enthalpy, specific entropy, specific volume, quality (vapor fraction) etc.
- Transport properties: density, viscosity, thermal conductivity.

The basic functionality needed of material stream class can be of following:

- Estimate of the thermodynamic and transport properties given a defined state.
- Mixing of streams
- Splitting the streams


The minimum number of independent variables needed to define thermodynamic state of a stream depend on the number of components and number of phases. This is determined using the famous phase rule, deduced by Josiah Willard Gibbs (1875), given below.  

$$
F = 2 - \pi + N
$$

Here $\pi$ is the number of phases, $N$ number of components, and $F$ is the number of independent variables needed to define the state of a material stream. 

For a pure component existing in one phase ($\pi  = 1$) any two of the following can be specified: Temperature, pressure, vapor fraction (quality), or specific thermodynamic properties such as specific enthalpy, specific entropy, specific volume. In the case of a mixture, in addition to above, the composition needs to be defined.

# Demo
A stream class is developed and its use is demonstrated below. Code for the stream class is given at the [end](#stream class code).

First of all import the CoolProp package and the `material_stream` class


```python
import CoolProp as CP
from stream import material_stream
```

Lets define a sample material stream (`MS100`) to showcase the basic usage of `material_stream` class

> **Note:** All units are in SI and flowrate (`flow`) is in mol/sec


```python
comp1 = {'Water': 0.70, 'Acetone': 0.30}  # Composition of the stream in mole fraction
state1 = {'temp': 350, 'pres': 101325, 'flow': 2} # State variabels in K, pa, and mol/sec respectively 
MS100 = material_stream(composition=comp1, state=state1)
```

One can get basic info about the stream using `print` function


```python
print(MS100)
```

    Stream containing ['Water', 'Acetone']
    

And get the stream properties as follow.


```python
print('Density: mass {0:.3f} kg/m^3; molar {1:.3f} mol/m^3'.format(MS100.Dmass, MS100.Dmolar))
print('Enthalpy: mass {0:.3f} J/kg; molar {1:.3f} J/mol'.format(MS100.Hmass, MS100.Hmolar))
print('Specific heat: mass {0:.3f} J/kg/K; molar {1:.3f} J/mol/K'.format(MS100.Cpmass, MS100.Cpmolar))
print('Viscosity: {0:.3e} Pa s; Thermal conductivity {1:.3e} W/m/k'.format(MS100.Cpmass, MS100.Cpmolar))
```

    Density: mass 1.074 kg/m^3; molar 35.755 mol/m^3
    Enthalpy: mass 1412882.498 J/kg; molar 42435.120 J/mol
    Specific heat: mass 1820.375 J/kg/K; molar 54.674 J/mol/K
    Viscosity: 1.820e+03 Pa s; Thermal conductivity 5.467e+01 W/m/k
    

> **Note:** Only limited number of properties are added in the `material_stream` class. More properties are available in the CoolProp package and can be added accordingly.

## Mixing streams


```python
# stream 2
comp2 = {'Water': 0.70, 'Ethanol': 0.30}
state2 = {'temp': 300, 'pres': 1.01e5, 'flow': 15}

MS200 = material_stream(composition=comp2, state=state2)
MS_add2 = MS100+MS200
print('New composition:\n', MS_add2.composition)
print('New state: \n', MS_add2.state)
```

    New composition:
     {'Water': 0.7000000000000001, 'Acetone': 0.03529411764705882, 'Ethanol': 0.2647058823529412}
    New state: 
     {'temp': 298.80945246731676, 'pres': 101000.0, 'flow': 17}
    

Mixing multiple streams


```python
# stream 3
comp3 = {'Water': 1.0}
state3 = {'temp': 320, 'pres': 101325, 'flow': 2}
MS300 = material_stream(composition=comp3, state=state3)
MS_add3 = MS100 + MS200 + MS300
print('New composition:\n', MS_add3.composition)
print('New state: \n', MS_add3.state)
```

    New composition:
     {'Water': 0.7315789473684211, 'Acetone': 0.031578947368421054, 'Ethanol': 0.23684210526315788}
    New state: 
     {'temp': 278.2947705104923, 'pres': 101000.0, 'flow': 19}
    

# Summary

Python's object-oriented programming offers a versatile platform to develop modules for modeling chemical engineering problems. The class object can hold data and functionalities for a unit operation. Creating a class instance as an object makes it easy to deploy the unit operation on demand and also interact with other section of the process.

# Stream Class Code

<script src="https://gist.github.com/tahircpe/6c0f09505dd7b55760e23305ac0aec11"></script>