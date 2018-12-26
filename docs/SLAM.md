#SLAM Introduction

the sparse map of SLAM  
![sparse](resources/Sparse.gif)

the dense map of SLAM  
![dense](resources/dense.gif)

#Cylinder Block Model
![Cylinderblockmodel](resources/Cylinderblockmodel.gif)

a Cylinder block model has six degree of freedom, rotation has three and translation has three. which can be described as follow.  



#Pinhole Camera Model
![pinhole](resources/pinhole.gif)
a pinhole camera coordinate can be described as follow  
<div align="center">![](http://latex.codecogs.com/gif.latex?{\begin{bmatrix}u \\\ u \end{bmatrix}} = 
	{\begin{bmatrix}
	fx & 0 & cx\\\ 
	0 & fy & cy\\\
	0 & 0 & 1\\\
	\end{bmatrix}}
	{\begin{bmatrix}
    {R_{3\*3}}&{t_{3\*1}}\\\
    {0_{1\*3}}&{1_{1\*1}}\\\
	\end{bmatrix}} \* {P_w})   

![](http://latex.codecogs.com/gif.latex?{{P_c} = {\begin{bmatrix}
    {R_{3\*3}}&{t_{3\*1}}\\\
    {0_{1\*3}}&{1_{1\*1}}\\\
	\end{bmatrix}} \* {P_w}})

![](http://latex.codecogs.com/gif.latex?{R_{3\*3}}{\text{ is a Orthogonal matrix}}\\\
P_w{\text{ is point} (X, Y, Z, 1) \text{in world coordinate}}\\\
P_c{\text{ is point} (X_c, Y_c, Z_c, 1) \text{in camera coordinate}})
