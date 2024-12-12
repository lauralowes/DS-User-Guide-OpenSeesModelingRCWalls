/// html | header

## Seismic Response of Concrete Walls

Modeling the Earthquake Response of Reinforced Concrete Walls Using OpenSees and Jupyter Notebooks

///

**Josh Stokley - Boeing Corporation**  <br>
**Laura Lowes - University of Washington**  

*Key Words: OpenSees, Jupyter, HPC*

The purpose of this use case is to facilitate OpenSees simulation and evaluation of simulation results for the flexure and flexure-shear controlled reinforced concrete wall tests documented in the [Shegay et al. (2021)](https://doi.org/10.17603/ds2-r12q-t415). Wall test specimen response to axial and cyclic lateral loading is simulated using a layered shell element as well as the plane stress concrete constitutive model developed and implemented for use in OpenSees and OpenSeesMP by [Lu et al.](https://http://www.luxinzheng.net/download/OpenSEES/En_THUShell_OpenSEES.html). A Jupyter notebook is provided that creates, for each wall specimen identified by the user, a tcl script that builds an OpenSees model of the wall test specimen including boundary conditions to support OpenSees analysis to simulate wall response for the axial load and lateral displacement histories applied in the laboratory. Additional Jupyter notebooks are provided that post-process OpenSees simulation data to compare measured and simulated load-displacement histories as well as movies of simulated stress fields and simulated crack patterns. Results are provided below for planar wall specimen RW1 tested by Thomsen and Wallace [Thomsen and Wallace, 2004] to facilitate understand of the Jupyter notebook workflow and its products. 

### Resources
 
#### Jupyter Notebooks
The following Jupyter notebooks are available to support model building for OpenSees and visualization of simualtion results. These notebooks are described in detail below. These notebooks can accessed and executed on DesignSafe by clicking on the "Open in DesignSafe" button.

| Scope | Notebook |
| :-------: | :---------:  |
| Matlab to Python | Matlab_to_Python.ipynb <br> [![Open In DesignSafe](https://raw.githubusercontent.com/geoelements/LearnMPM/main/DesignSafe-Badge.svg)](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/Matlab_to_Python.ipynb) |
| Tcl-Script Creator | TCL_Script_Creator.ipynb <br> [![Open In DesignSafe](https://raw.githubusercontent.com/geoelements/LearnMPM/main/DesignSafe-Badge.svg)](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/TCL_Script_Creator.ipynb) |
| Load-Displacement History | DisplacementLoadHistory.ipynb <br> [![Open In DesignSafe](https://raw.githubusercontent.com/geoelements/LearnMPM/main/DesignSafe-Badge.svg)](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/DisplacementLoadHistory.ipynb) |
| Cross-Sectional Analysis | CrossSectionSteelConcreteProfile.ipynb | [![Open In DesignSafe](https://raw.githubusercontent.com/geoelements/LearnMPM/main/DesignSafe-Badge.svg)](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/CrossSectionSteelConcreteProfile.ipynb)<br> -- this notebook has been removed|
| Movies | Movies.ipynb <br> [![Open In DesignSafe](https://raw.githubusercontent.com/geoelements/LearnMPM/main/DesignSafe-Badge.svg)](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/Movies.ipynb) |
| Crack Angle Viz | CrackedModel.ipynb <br> [![Open In DesignSafe](https://raw.githubusercontent.com/geoelements/LearnMPM/main/DesignSafe-Badge.svg)](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/CrackedModel.ipynb) |

#### DesignSafe Resources
The following DesignSafe resources were used in this use case.

* [Jupyter Notebook on DesignSafe](https://www.designsafe-ci.org/rw/workspace/#!/Jupyter::Analysis) <br/> 
* [Simulation on DesignSafe - OpenSees](https://www.designsafe-ci.org/rw/workspace/#!/OpenSees::Simulation)


### Background

#### Citation and Licensing

* Please cite [Lowes LN and Stokley J (2023)](https://doi.org/10.1007/978-3-031-32511-3_69) to acknowledge the use of this use case.

* Please cite [Shegay et al. (2021)](https://doi.org/10.17603/ds2-r12q-t415) to acknowledge the use of wall test data included in this use case.

* Please cite [Lu XZ et al. (2015)](http://www.luxinzheng.net/download/OpenSEES/En_THUShell_OpenSEES.htm) to acknowledge the use of the OpenSees wall models employed in this use case.
  
* Please cite [McKenna F et al. (2010)](https://doi.org/10.1061/(ASCE)CP.1943-5487.0000002) to acknowledge the use of OpenSees platform in this use case.

* Please cite [Rathje et al. (2017)](https://doi.org/10.1061/(ASCE)NH.1527-6996.0000246) to acknowledge the use of DesignSafe resources.  

* This software is distributed under the [GNU General Public License](https://www.gnu.org/licenses/gpl-3.0.html).  

### Description
#### Data  

The data characterizing the RC wall tests used in this use case were assembled and archived in the DesignSafe Data Depot by [Shegay et al. (2021)](https://doi.org/10.17603/ds2-r12q-t415). These data are provided as a MATLAB 'structure' datatype named "WallData.mat". WallData.mat characterizes 142 walls tests; a subset of these tests were considered in the use case. WallData.mat has a tree-like structure and comprises several top levels, each of which comprises multiple variables with some "variables" comprising multiple sub-levels. In most cases, each entity within the data structures is 1x142 dimension array, with data ordered within the array consistently throughout the database based on the order of wall test specimens in the 'UniqueID' variable included in WallData.mat. In a few cases, which are utilized in this use case, arrays embedded within the data structure included a subset of the 142 "UniqueIDs" and associated data.     

#### Modeling

In this use case, walls are modeled using the OpenSees [ShellDKGQ element] (http://www.luxinzheng.net/download/OpenSEES/En_THUShell_OpenSEES.htm); however, the [MITC4 shell element] (https://opensees.berkeley.edu/wiki/index.php/Shell_Element) could also be used. The Opensees [MembranePlateFiberSection](https://opensees.github.io/OpenSeesDocumentation/user/manual/section/LayeredMembraneSection.html) is used to create a shell section comprising confined and unconfined concrete and transverse reinforcing steel; the volume of the discrete transverse steel bars within the perimeter of the element is represented by the volume of a single steel layer within the layered shell element. This is illustrated in Figure 1. Vertical reinforcing steel is modeled using discrete truss elements and the [Steel02 material model](https://opensees.berkeley.edu/wiki/index.php/Steel02_Material_--_Giuffr%C3%A9-Menegotto-Pinto_Model_with_Isotropic_Strain_Hardening) For each wall specimen, a mesh was constructed with nodes located horizontally to exactly represent vertical bar locations and vertically to create elements with vertical-to-horizontal aspect ratios as close to 1.0 as possible. To accomplish this objective as well as ensure that the number of elements in the model did not result in excessive computational demand, cover concrete at the horizontal ends of the wall was ignored. 

![SchematicView](img/ShellEle.JPG)  
Figure 1: Smeared shell element representation  

### Example 

RW1 is modeled from the database to produce a tcl file that represents the geometry, material, and simulation history of the wall. The wall is 150 inches high, 46.37 inches long, and 4 inches thick. It consists of 1292 amount of nodes, 1200 amount of shell elements, and 900 amount of steel truss elements. MITC4 shell elements are used to smear the concrete and transverse steel into the thickness while the vertical reinforce bars are modeled as truss elements. RW1 had a compression buckling failure mode in the lab. More information on RW1 and its experimental results can be found here: [Wallace et al. (2004)](https://ascelibrary.org/doi/10.1061/%28ASCE%290733-9445%282004%29130%3A4%28618%29#:~:text=Application%20of%20displacement%2Dbased%20design,for%20a%20given%20top%20displacement.){:target="_blank"} 

The use case workflow involves the following steps:

* Using Jupyter notebook modeling script to create input file for OpenSees
* Running input file through HPC on DesignSafe
* Using Jupyter notebook post processing scripts to evaluate model


### Create Input File using Modeling Script

The modeling script is broken up into 2 notebooks, the first notebook imports the variables to build the wall into an array. The second notebook builds out the tcl file that will be ran through openseees. The sections defined below are from the second notebook.  
The matlab to python script can be found here: [Matlab_to_Python.ipynb](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/Matlab_to_Python.ipynb){:target="_blank"} 
The jupyter notebook that creates the OpenSees input file can be found here: [TCL_Script_Creator.ipynb](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/TCL_Script_Creator.ipynb){:target="_blank"} 

#### Reinforced Concrete Wall Database   

Each wall in the database has a number corresponding to its unique ID. This number will be the single input to the modeling script to create the script. The use case will loop through multiple numbers to create multiple files at once and run them through opensees. Variables are separated in the database by sections. For example, under the section 'Geometry', one can find the heights of the walls, the thickness of walls, the aspect ratios, and so on. By parsing through these sections, the necessary information can found and imported into the modeling script to build out the wall.

RW1 is wall 33 in the database (with the first wall index starting at 0) and using that index number, the modeling script can grab everything that defines RW1.  

#### Modeling Script  

The sections of the modeling script are:  
##### Section 1: Initialization of the model
   * The degrees of freedom and the variables that carry uncertainty are defined.  
  
##### Section 2: Defines nodal locations and elements 
   * Nodes are placed at the locations of the vertical bars along the length of the wall.
   * If the ratio of the length of the wall to the number of elements is too coarse of a mesh, additional nodes are placed in between the bars.
   * The height of each element is equal to the length of the nodes in the boundary to create square elements up the wall.  
  
##### Section 3: Defines material models and their variables
   * The crushing energy and fracture energy are calculated and wrote to the .tcl file. The equations for these values come from ([Nasser et al. (2019)](https://ascelibrary.org/doi/pdf/10.1061/%28ASCE%29ST.1943-541X.0002311){:target="_blank"} ) Below is the code: 

```py
self.gtcc = abs((0.174*(.5)**2-0.0727*.5+0.149)*((self.Walldata[40]*1000*conMult)/1450)**0.7) #tensile energy of confined
self.gtuc = abs((0.174*(.5)**2-0.0727*.5+0.149)*((self.Walldata[40]*1000)/1450)**0.7) # tensile energy of unconfined
self.gfuc = 2*self.Walldata[40]*6.89476*5.71015 #crushing energy of unconfined
self.gfcc = 2.2*self.gfuc #crushing energy of confined
```

   * The crushing strain (epscu) and fracture strain (epstu) can then be calculated from the energy values.
   * The material models are then defined.
   * The concrete material opensees model: nDmaterial PlaneStressUserMaterial $matTag 40 7 $fc $ft $fcu $epsc0 $epscu $epstu $stc.
      * 'fc' is the compressive strength, 'ft' is the tensile strength, 'fcu' is the crushing strength, and 'epsc0' is the strain at the compressive strength.
   * The steel material opensees model: uniaxialMaterial Steel02 $matTag $Fy $E $b $R0 $cR1 $cR2.
      * 'Fy' is the yield strength, 'E' is the youngs modulus, 'b' is the strain hardening ratio, and 'R0', 'cR1', and 'cR2' are parameters to control transitions from elastic to plastic branches.
   * minMax wrappers are applied to the steel so that if the steel strain compresses more than the crushing strain of the concrete or exceeds the ultimate strain of the steel multiplied by the steel rupture ratio, the stress will go to 0.  
  
##### Section 4: Defines the continuum shell model
   * The shell element is split up into multiple layers of the cover concrete, transverse steel, and core concrete.
   * The cover concrete thickness is defined in the database, the transverse steel thickness is calculated as:
      * total layers of transverse steel multiplied by the area of the steel divided by the height of the wall.
   * The total thickness of the wall is defined in the database so after the cover concrete and steel thicknesses are subtracted, the core concrete takes up the rest.
  
##### Section 5: Defines the elements
   * The shell element opensees model is: element ShellMITC4 $eleTag $iNode $jNode $kNode $lNode $secTag
      * 'eleTag' is the element number, the next four variables are the nodes associated to the element in ccw, and 'secTag' is the section number that defines the thickness of the element.
   * There are usually two sections that are defined, the boundary and the web. Based on how many nodes are in the boundary, the script will print out the elements for the left side of the boundary, then for the entire web region, and lastly for the right side of the boundary. This process is repeated until the elements reach the last row of nodes.
   * For the vertical steel bars, the truss element opensees model is used: element truss $eleTag $iNode $jNode $A $matTag.
      * The node variables are defined as going up the wall so if a wall has 10 nodes across the base, the first truss element would connect node 1 to node 11.
      * 'A' is the area of the bar and 'matTag' is the material number applied to the truss element.
   * The script prints out truss elements one row at a time so starting with the left furthest bar connecting to each node until the height of the wall is reached and then next row is started the bar to the right.  
      
##### Section 6: Defines constraints
   * The bottom row of nodes are fixed in all degrees of freedom.

##### Section 7: Defines recorders
   * The first two recorders capture the force reactions in the x-direction of the bottom row of nodes and the displacements in the x-direction of the top row of nodes. These recorders will be used to develop load-displacement graphs.
   * The next eight recorders capture stress and strain of the four gauss points in the middle concrete fiber of all the elements and store them in an xml file. These recorders will be used to develop stress and strain profile movies, give insight to how the wall is failing, and how the cross section is reacting.
   * The last two recorders capture the stress and strain of all the truss elements. These will be used to determine when the steel fails and when the yield strength is reached.
   * Recorders are defined as below where 'firstRow' and 'last' are the nodes along the bottom and top of the wall 'maxEle' is the total amount of shell elements and 'trussele' is the total elements of shell elements and truss elements in the wall. 

```python
self.f.write('recorder Node -file baseReactxcyc.txt  -node {}  -dof 1 reaction\n'.format(' '.join(firstRow)) )         
self.f.write('recorder Node -file topDispxcyc.txt    -node {}  -dof 1 disp \n'.format(' '.join(last)))
self.f.write('recorder Element -xml "elementsmat1fib5sig.xml"  -eleRange 1 ' + str(self.maxEle) + ' material 1    fiber 5    stresses\n')
self.f.write('recorder Element -xml "elementsmat2fib5sig.xml"  -eleRange 1 ' + str(self.maxEle) + ' material 2    fiber 5    stresses\n')
self.f.write('#recorder Element -xml "elementsmat3fib5sig.xml"  -eleRange 1 ' + str(self.maxEle) + ' material 3    fiber 5    stresses\n')
self.f.write('#recorder Element -xml "elementsmat4fib5sig.xml"  -eleRange 1 ' + str(self.maxEle) + ' material 4    fiber 5    stresses\n')
self.f.write('recorder Element -xml "elementsmat1fib5eps.xml"  -eleRange 1 ' + str(self.maxEle) + ' material 1    fiber 5    strains\n')
self.f.write('recorder Element -xml "elementsmat2fib5eps.xml"  -eleRange 1 ' + str(self.maxEle) + '  material 2    fiber 5    strains\n')
self.f.write('#recorder Element -xml "elementsmat3fib5eps.xml"  -eleRange 1 ' + str(self.maxEle) + '  material 3    fiber 5    strains\n')
self.f.write('#recorder Element -xml "elementsmat4fib5eps.xml"  -eleRange 1 ' + str(self.maxEle) + ' material 4    fiber 5    strains\n')
self.f.write('recorder Element -xml "trusssig.xml"  -eleRange ' + str(self.maxEle+1) + ' ' + str(self.trussele)+ ' material stress\n')
self.f.write('recorder Element -xml "trussseps.xml" -eleRange ' + str(self.maxEle+1) + ' ' + str(self.trussele)+ ' material strain\n')
```

##### Section 8: Defines and applies the gravity load of the wall
   * The axial load of the wall is defined in the database and distributed equally amongst the top nodes and a static analysis is conducted to apply a gravity load to the wall.
  
##### Section 9: Defines the cyclic analysis of the wall
   * The experimental displacement recording of the wall is defined in the database and the peak displacement of each cycle is extracted.
   * If the effective height of the wall is larger than the measured height, a moment is calculated from that difference and uniformly applied in the direction of the analysis to each of the top nodes.
   * The displacement peaks are then defined in a list and ran through an opensees algorithm.
      * This algorithm takes each peak and displaces the top nodes of the wall by 0.01 inches until that peak is reached, it then displaces by 0.01 inches back to zero where it then takes on the next peak. This process continues until failure of the wall or until the last peak is reached.  
  
The last section of this notebook creates a reference file that holds variables needed for postprocessing. In order, those variables are:
  * Total nodes along the width of the wall
  * Total nodes along the width of the wall that are connected to a truss element
  * Total nodes in the file
  * Total elements in the file
  * Displacement peaks in the positive direction
  * Fracture strength of the concrete
  * total layers of elements in the file
  * Unique ID of the wall
  * filepath to the folder of the wall
  * filepath to the tcl file  

  
### Running Opensees through HPC

(Script needs to be established on design safe. I have a working notebook, just need to connect it with modeling script)


### Post Processing

After the script is finished running through OpenSees, there are multiple post-processing scripts that can be used to analyze the simulation and compare it to the experimental numbers.

#### Load-Displacement Graph

The Load-Displacement script compares the experimental cyclic load history to the simulated cyclic load output. The x axis is defined as drift % which is calculated as displacement (inches) divided by the height of the wall. The y axis is defined as shear ratio and calculated as force (kips) divided by cross sectional area and the square root of the concrete compressive strength.  This Script can be found here: [DisplacementLoadHistory.ipynb](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/DisplacementLoadHistory.ipynb){:target="_blank"} 

![SchematicView](img/disp.JPG)

#### Cross Sectional Analysis of Concrete and Steel

The cross sectional script shows stress and strain output across the cross section of the first level for the concrete and steel at various points corresponding with the positive displacement peaks.  This script can be found here: [CrossSectionSteelConcreteProfile.ipynb](https://www.designsafe-ci.org/data/browser/public/designsafe.storage.community/Use%20Case%20Products/QUOFEM){:target="_blank"}   

![SchematicView](img/cs1.JPG)  

![SchematicView](img/cs1.JPG)  

#### Stress and Strain Profile Movies

The Stress/Strain profile movie script utilizes plotly to create an interactive animation of stresses and strains on the wall throughout the load history. The stress animations are vertical stress, shear stress, and maximum and minimum principal stress. The strain animations are vertical strain, shear strain, and maximum and minimum principal strain. This script can be found here: [Movies.ipynb](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/Movies.ipynb){:target="_blank"} 

![SchematicView](img/movies.JPG)  

#### Crack Angle of Quadrature Points

The crack angle script will show at what angle each quadrature point cracks. When the concrete reaches its fracture strength in the direction of the maximum principal stress, it is assumed that it cracked and the orientation at that point is then calculated shown on the graph. The blue lines indicates the crack angle was below the local x axis of the element and the red line means the crack angle was above the local x axis of the element.  This script can be found here: [CrackedModel.ipynb](https://jupyter.designsafe-ci.org/hub/user-redirect/lab/tree/CommunityData/Use%20Case%20Products/QUOFEM/CrackedModel.ipynb){:target="_blank"}  

![SchematicView](img/cracked.JPG)  
