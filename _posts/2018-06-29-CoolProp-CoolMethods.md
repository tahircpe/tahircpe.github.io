---
layout: post
title: CoolProp — A Cool Package for Thermophysical Properties  
#featured-img: phase-diagram
mathjax: true
categories: CoolProp Thermophysical Python Chemical-Engineering
---

# CoolProp

![CoolProp](http://www.coolprop.org/_static/CoolPropLogo.png "CoolProp")


[CoolProp](http://www.coolprop.org/index.html) is a thermophysical property database which is available under MIT License as a free software. It has a databank of 122 components with thermodynamic and transport properties for pure components and their mixture. The components available in CoolProp are [listed here](http://www.coolprop.org/fluid_properties/PurePseudoPure.html#list-of-fluids). 

[CoolProp](http://www.coolprop.org/index.html) is developed as a C++ library. It has an interface available for several of the popular programming languages in the form of wrappers, such as Python, Modelica, Octave, MathCAD, and MATLAB. A complete list of available wrappers is [listed here](http://www.coolprop.org/coolprop/wrappers/index.html).

## Python wrapper
Here the use of Python wrapper is described which can be which can be installed using the `pip` package installation tool as

```bat
pip install CoolProp
```

For the conda package manager it can be installed using

```bat
conda install CoolProp
```

Before the wrapper install the system needs to have following requirements installed:

- Python (2.x 3.x)
- CMake (platform-independent software to generate makefiles)
- C++ compiler
- 7-zip

Microsoft Visual C++ Build Tools 2015 is required as C++ compiler for the latest [CoolProp 6.1.0](https://pypi.org/search/?q=coolprop) and Python 3.6. More details about the prerequisites for the Python wrapper can be found [here](http://www.coolprop.org/coolprop/wrappers/index.html). If the installer fails due to error *building wheel fails* try the following methods of install using that uses the latest release from the development server:

```bat
pip install -vvv --pre --trusted-host www.coolprop.dreamhosters.com --find-links http://www.coolprop.dreamhosters.com/binaries/Python/ -U --force-reinstall CoolProp
```

Python wrapper has two types of interfaces available:

- High-level interface
- Low-level interface


## High-level interface

The high-level interface offers an easy to use interface to get thermophysical properties. The high-level interface is provided using `PropsSI` function and it can be used to get properties for pure fluids, pseudo-pure fluids, and mixtures. `PropsSI` function is imported as follows:


```python
from CoolProp.CoolProp import PropsSI
```

### Pure component properties
Following examples showcase the use of `PropsSI` function to get pure component properties. The input format follows a generic template, a name of the parameter to be determined, followed by at least two of the state properties and the name of the component.

> **All the inputs are in SI units**

#### Example:
The boiling point of Ethanol at atmospheric pressure


```python
Tb = PropsSI('T', 'P', 101325, 'Q', 0, 'Ethanol')
print('Boiling point {0:.3f} K'.format(Tb))
```

    Boiling point 351.570 K
    

The density of Ethanol at 320 K and atmospheric pressure


```python
mass_D = PropsSI('Dmass', 'T', 320, 'P', 101325, 'Ethanol')
print('Mass Density is {0:.3f} kg/m^3'.format(mass_D))
mole_D = PropsSI('Dmolar', 'T', 320, 'P', 101325, 'Ethanol')
print('Molar Density is {0:.3f} mole/m^3'.format(mole_D))
```
    Mass Density is 766.014 kg/m^3
    Molar Density is 16627.741 mole/m^3
    

Full list of paramteres availble can is given [here](http://www.coolprop.org/coolprop/HighLevelAPI.html#table-of-string-inputs-to-propssi-function).

### Mixture properties

Mixtures require an additional input of compositions to completely define the thermodynamic state. The composition input is made using the following string format:

`HESO::Name[Mole Fraction]&Name[Mole Fraction]&...` 

Component name and [mole fractions] combinations joined by '&'.

#### Example:
The boiling point of Ethanol and Water mixture at atmospheric pressure:


```python
Tb = PropsSI('T', 'P', 101325, 'Q', 0, 'HEOS::Ethanol[0.20]&Water[0.80]')
print('Boiling point Ethanol/Water mixture {0:.3f} K'.format(Tb))
```

    Boiling point Ethanol/Water mixture 356.224 K
    

Transport properties of Ethanol and Water mixture at 320 K and atmospheric pressure:


```python
composition = 'HEOS::Ethanol[0.20]&Water[0.80]'
vis = PropsSI('V', 'T', 320, 'P', 101325, composition)
print('Dynamic viscosity of Ethanol/Water mixture {0:.3e} Pa s'.format(vis))
cond = PropsSI('L', 'T', 320, 'P', 101325, composition)
print('Thermal conductivity of Ethanol/Water mixture {0:.3e} W/m/K'.format(cond))
```

    Dynamic viscosity of Ethanol/Water mixture 7.259e-04 Pa s
    Thermal conductivity of Ethanol/Water mixture 7.344e-01 W/m/K
    

## Low-level interface

Low-level interface comes in handy when multiple property parameters are required. It works by defining a single system and its state and use `keyed_output` function to return paramter values. In principle, one can iterate over several [parameter keys](http://www.coolprop.org/_static/doxygen/html/namespace_cool_prop.html#a4b49eeb37210a720b188f493955d8364) to get their values. The state of the system can be defined using any of the [`input_pairs`](http://www.coolprop.org/_static/doxygen/html/namespace_cool_prop.html#a58e7d98861406dedb48e07f551a61efb).

#### Example
Following example illustrates the use of low-level interface to get multiple properties of Ethanol and Water mixture.

Define the system:


```python
import CoolProp
system = CoolProp.AbstractState("HEOS",'Ethanol&Water')
```

Define the composition:


```python
system.set_mole_fractions([0.2,0.8])
```

Define the state


```python
system.update(CoolProp.PT_INPUTS, 101325, 320)
```

Iterate over `keyed_output` to get multiple properties


```python
names = ['Mass enthalpy', 'Molar enthalpy', 'Viscosity', 'Thermal conductivity'] 
parameters = [system.keyed_output(k) for k in [CoolProp.iHmass, CoolProp.iHmolar, CoolProp.iviscosity, CoolProp.iconductivity ]]

for i in range(len(names)):
    print(names[i]+ '\t{0:0.3f}\n'.format(parameters[i]))
```

    Mass enthalpy	69851.271
    
    Molar enthalpy	1650.299
    
    Viscosity	0.001
    
    Thermal conductivity	0.734
    
    

## Summary

The methods described above demonstrate the use of CoolProp tool to get thermophysical properties using the two interface methods, namely, high-level interface and low-level interface. Its utility is in developing process and equipment models when detailed thermophysical properties are required. The great thing about CoolProp is that its free of cost and it has an easy to use interface. These are the reasons CoolProp is gaining popularaity among academic and industrial applications.
