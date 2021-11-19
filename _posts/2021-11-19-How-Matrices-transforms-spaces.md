
---

title : "Matrices in linear Algebra:operating on vectors"



excerpt: "What Matriceeis does on vectors?"

categories:
  - Matrix Transformation
tags:
  - [Mathmathics,Machine Learning,Coursera,Linear algebra , matrix,vectors]

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

