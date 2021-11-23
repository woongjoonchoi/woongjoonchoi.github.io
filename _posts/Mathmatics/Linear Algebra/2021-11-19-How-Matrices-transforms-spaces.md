---
title : "Matrices in linear Algebra:operating on vectors"



excerpt: "What Matriceeis does on vectors?"

categories:
  - Matrix Transformation
tags:
  - [Mathmathics,Machine Learning,Coursera,Linear algebra , matrix,vectors]
# classes : wide
toc: true
toc_sticky: true
---



This post is based on  MathMatics for Machine Learning  in Coursera.

## How Matrices transforms spaces

we studied idea that solving simultaenous equation problems using matrices.   And , we know that colums on matrices are transformed basis vectors. Now, we 'll see how matrices transform spaces. 





![img](data:image/png;base64,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)

we assume that $$e_1=(1,0) ,e_2=(0,1) ,\hat e_1=(2,10) , \hat e_2=(3,1) $$



We could make any vector in vector sum of $$\hat e_1 ,\hat e_2$$ . That means result of transformation going to be vector sum .

### Rule 

Here is rules that we defined.



> $$ A . r = \hat r $$ 
>
> $$ A \cdot (nr) = n \cdot \hat r $$
>
> $$ A \cdot (r + s) = Ar + As $$

Then , we induce that $$(A (n_1 \cdot e_1  + n_2 \cdot e_2 )  =  n_1 \cdot A \cdot e_1 + n_2 \cdot A \cdot e_2 = n_1 \cdot \hat e_1 + n_2 \cdot \hat e_2 $$ .

We see that vector sum rule(make any vector in vector sum) works well . Let's see concrete example. 

$$\begin{pmatrix}2&3\\10&1\end{pmatrix} \begin{pmatrix}3\\2\end{pmatrix} = \begin{pmatrix}32\\12\end{pmatrix} $$



$$ 3 \cdot \begin{pmatrix}2\\10\end{pmatrix}  + 2 \cdot \begin{pmatrix}3\\1\end{pmatrix} $$

So, it is just multplicatoin vector of vector sum of transformed vectors.



## Types of matrix Transformation

$$ n, m ( > 0) $$

- $$\begin{pmatrix}3&0\\0&2\end{pmatrix} \to $$scale x-asis factors n , scale y-axis factors m 
- $$\begin{pmatrix}-1&0\\0&2\end{pmatrix} \to $$ scale x-axis factors minus n ,scale y-axis factors n
- $$\begin{pmatrix}-1&0\\0&-1\end{pmatrix} \to $$ scale x-axis facotrs minus n , scale y-axis factors n
- $$\begin{pmatrix}0 & 1\\1 &0\end{pmatrix} \to $$ symmetric about $$ y =x $$  
- $$\begin{pmatrix}0 & -1\\-1 &0\end{pmatrix} \to $$ symmetric about $$ y =-x $$
- $$\begin{pmatrix}1 &1\\0 &1 \end{pmatrix} \to $$ common conversion
- $$\begin{pmatrix} \cos \theta & \sin \theta \\ - \sin \theta & \cos \theta \end {pmatrix}$$ rotation transformation
- $$\begin{pmatrix} \cos \theta & \sin \theta & 0 \\ - \sin \theta & \cos \theta & 0 \\ 0 & 0& 1 \end {pmatrix}$$ rotation transformation preserve z  in 3-D



## Combination and Composition of matrix transformation

$$ A_1 = \begin{pmatrix}0 & 1\\-1 &0\end{pmatrix}   A_2 = \begin{pmatrix}-1 & 0\\0 &1\end{pmatrix} $$ 

We show you two combination of matrix transformation

$$ A_1 \cdot A_2 = \begin{pmatrix}0 & 1\\-1 &0\end{pmatrix}  \cdot  \begin{pmatrix}-1 & 0\\0 &1\end{pmatrix} = \begin{pmatrix}0 & 1\\1 &0\end{pmatrix}$$ 



$$ A_2 \cdot A_1 = \begin{pmatrix}-1 & 0\\0 &1\end{pmatrix}  \cdot  \begin{pmatrix}0 & 1\\-1 &0\end{pmatrix} = \begin{pmatrix}0 & 1\\1 &0\end{pmatrix}$$ 



$$A_1 \cdot A_2\to $$ symmetric about $$ y =x $$ 

$$A_2 \cdot A_1\to $$ symmetric about $$ y =-x $$ 



> Matrices are associatve but not ***<u>commutative</u>***  

we should'be careful about matrix mulitplication

