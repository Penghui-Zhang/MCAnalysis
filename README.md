Author: Penghui Zhang   
Email: penghui@tongji.edu.cn    
Environemet: Successfully excucted in python 3.8
______
# MCAnalysis
This is an auto moment-curvature analysis tool. Mesh generation is accomplished by gmesh, and  moment-curvature analysis is running in openseespy platform.

The program has been adopted in the following studies. Please cite the paper when using or referencing the code：
Zhang P, Wang Z, Ge J, Yan X and Liu S. Full-scale experimental study on precast bridge column with grouted sleeve connections and large-diameter reinforcing bars. Engineering Structures 2023; 294: 116747.
![image](https://github.com/Penghui0616/MCAnalysis/assets/42617930/3df97159-5c82-43ff-94d7-e4c02ed38f48)

Zhang P, Zhou L, Guo J, Wang Z. Strut‐and‐tie model for column‐to‐drilled shaft connections in reinforced concrete bridge columns subjected to lateral loads. Struct Concr 2024;25:4183–201.
![image](https://github.com/user-attachments/assets/2266f618-aec3-4360-8ec1-88e25c85828f)

## Tutorials 
1. download the zip file.
2. download gmsh.exe ([download gmsh](https://gmsh.info/)) that satifies your operation system,and put gmsh.exe to your unzip file in step 1. 
3. confirm the openseespy, matplotlib, numpy, pygmesh (Version 6.1.0) are instelled.

The followings are some basic examples, and you can also find them in the download files.

## Moment-curvature analysis of a circular section
```python 
#Define section
sectName = 'CircularPier'
outD = 2  # the diameter of the outside circle(m)
coverThick = 0.06  # the thinckness of the cover concrete(m)
outbarD = 0.032  # outside bar diameter(m)
outbarDist = 0.119  # outside bar space(m)
coreSize = 0.2  # the size of core concrete fiber
coverSize = 0.3  # the size of cover concrete fiber
plotState = True # plot the fiber or not plot=True or False
coreFiber, coverFiber, barFiber = circleSection(sectName, outD, coverThick, outbarD, outbarDist, coreSize, coverSize, plotState)

# Define material
roucc = np.sum(barFiber, axis=0)[2]/np.sum(coreFiber, axis=0)[2]
material = Material(sectName)
barParameter = material.barParameter("HRB400")
coverParameter = material.coverParameter("C40")
coreParameter = material.coreParameterCircular("C40", "Spiral", outD, coverThick, roucc, 0.1, 0.014, 400)

#Estimate the yield curvature of circular section
kx = 2.213*barParameter[0]/barParameter[2]/outD
ky =kx
np.savetxt(sectName+"/yieldCurvature.txt", [kx, ky],  fmt="%0.6f")

#Moment curvature analysis
mcInstance = MC('CircularPier', 'X')
mcInstance.MCAnalysis(4244, 0)
momEff = mcInstance.MCCurve()
```
<img src="https://github.com/Penghui0616/MCAnalysis/blob/master/Circular.png" div align="center">

## Moment-curvature analysis of a rectangular section 
```python 
#Define section
sectName = 'RectangularPier'
outSideNode = {1: (0.8,1.6), 2: (-0.8,1.6), 3: (-0.8,-1.6), 4: (0.8,-1.6)}
outSideEle = {1: (1, 2), 2: (2, 3), 3: (3, 4), 4: (4, 1)}

coverThick = 0.06  # the thinckness of the cover concrete(m)
coreSize = 0.2  # the size of core concrete fiber
coverSize = 0.3  # the size of cover concrete fiber
outBarDist = 0.1846153846 #bar space(m)
outBarD = 0.028 #bar diameter(m)
plotState = True # plot the fiber or not plot=True or False
plotState=True  # plot the fiber or not plot=True or False
autoBarMesh=True #if false provide the barControlNodeDict and barEleDict
userBarNodeDict=None # {1:(y1,z1),2:(y2,z2),...} bar line end nodes
userBarEleDict=None # {1:(nodeI,nodeJ,barD,barDist),...}  bar line end nodes number and diameter and distance
coreFiber,coverFiber,barFiber=polygonSection(sectName, outSideNode, outSideEle, coverThick, coreSize, coverSize,
								   outBarD, outBarDist,plotState,autoBarMesh)

# Define material
roucc = np.sum(barFiber, axis=0)[2]/np.sum(coreFiber, axis=0)[2]
lx = 1.6 #length of the outer section in x direction(m)
ly = 3.2 #length of the outer section in y direction(m)
sl = 0.1846153846 #longitudinal bar space(m)
dsl = 0.028 #longitudinal bar diameter(m)
roux = 0.005 #stirrup reinforcement ratio in x direction
rouy = 0.005 #stirrup reinforcement ratio in y direction
st = 0.15 #transverse bar space(m)
dst = 0.012 #transverse bar diameter(m)
fyh = 400 #transverse bar yield stress(MPa)

material = Material(sectName)
barParameter = material.barParameter("HRB400")
coverParameter = material.coverParameter("C40")
coreParameter = material.coreParameterRectangular("C40", lx, ly, coverThick, roucc, sl, dsl, roux, rouy, st, dst, fyh)

#Estimate the yield curvature of rectangular section
kx = 1.957*barParameter[0]/barParameter[2]/lx
ky = 1.957*barParameter[0]/barParameter[2]/lx
np.savetxt(sectName+"/yieldCurvature.txt", [kx, ky],  fmt="%0.6f")

#Moment curvature analysis
mcInstance = MC('RectangularPier', 'X')
mcInstance.MCAnalysis(23000, 0)
momEff = mcInstance.MCCurve()
```
<img src="https://github.com/Penghui0616/MCAnalysis/blob/master/Rectangular.png" div align="center">
