# Introduction to FEM Analysis with Python

Nice to meet you. My name is Koyama. Thank you for giving me the opportunity to speak today.
Today I'm going to talk under the title of "Introduction to FEM Analysis with Python".

# Who are you? 👀

First of all, who I are you? So let me introduce myself. I will explain the main activities.
TwitterID is @tkoyama010. I have been developing civil engineering and architectural software and performing commissioned analysis at a CAE software vendor for about 8 years. My work includes programming in Fortran, Python, and C ++, as well as work automation using VBScript. I'm a numerical calculator. 

# Who are you? 👀

I has been distributing a translation of the documentation of the OSS library which called GetFEM++ . 
For example, I've been working on projects for about two years, doing FixTypo, refactoring, translating, and so on. I've learned a lot from just looking at the source code, but recently I wanted to add an object myself, so I added two object this year.
As far as I can see, I have about 50 commits.
My first goal is 100 commits.

# Agenda

This 

# 📝 Question❗

- If you have ever heard of the word "finite element method".
Thank you.
- If you have ever heard of the word "differential equation".
Thank you. Even if you have heard the word differential equation, I think most of you don't know finite element method. It's a short time, but I'd like to explain that part first.

# 🤔 What is PDE and FEM❓

TODO: change to the equation.
PDE is a equation using in physics, engineering, Such as electricity, heat, and the behavior of objects can all be expressed by PDE.

# 🤔 What is PDE and FEM❓

The finite element method is a method of PDE.
FEM solves the problem by converting PDE into simultaneous equations like this.

# 👨‍💻What is GetFEM++❓

This library provides a framework for solving PDE by FEM.
A library it'self is coded C++.
But the Python interface is used primarily.

# 📄Release Notes

The release notes contain updates for each version, so you can see what's hot right now for development within the library.
It is September now, so the next version will be released in a while.
The development itself started in the 90s, and the source code is refactored while maintaining backward compatibility.

# 🔰Installation@ Ubuntu

Now let's install it! In Ubuntu, you can install it by apt command. It is very easy.
But it's boring, so let's going to compile and install it with various libraries.
You can get source code by wget command.

# 🔰Installation@ Ubuntu

Afeter downloading sources. Run ./configure command.
There are some options.
This will verify that the necessary libraries are installed.
The default user interface for GetFEM is 32bit and Python 2, but you can optionally switch to 64bit Python 3.
So which libray is need?

# 📗 Using Library

There are 4 library need for compile. Qhull, MUMPS, LAPACK/BLAS and Numpy/Scipy.
Qhull is a library used to compute the convex structure, which cuts the mesh.
FEM use mesh structure to solve, like this.
MUMPS is the library for parallel calculation which is import to solve big size of FEM.
LAPACK/BLAS is the library for Linear Algebra.
Numpy/Scipy is as you know, the library of Numercal computation in Python.

# 📗 Using Library

After install these library.
You can compile make command, make check command to test annd make install command to install.

# 🏃 Tutorial

Now let's try tutorial!
In this tutorial, I will solve the this problem.
Problem is a circle with a radius of 1.0 as shown here, and within that circle you have a PDE which called Lapacian.
Let's call this circle "omega".
RHS of the equation is 1.0.
This means the there is a load at all of the circle.
The value of u at side of "omega" which calle delta-omega is zero.

# 🏃 Mesher Object

First of all we have to make the geometory of the circle by using MesherObject.
As a args, set string 'ball' as a shape of geometory and center of coordinates and radius size.

# 🏃 Mesh object

As you can see, the center coordinate should be x=0.0, and y=0.0, but MeserObject cannot set negative value.
So we'll build the mesh in the center (1.0, 1.0) and later modify the center coordinates to the (0.0, 0.0) location.
When you build object you can use Mesh object.
The args are string to 'generate' mesh, mesher object, size of approximate mesh length, and the order of element which set 2 in this problem.

# 🏃 Move Mesh and Set Region

Next move center of circle of the mesh to (0.0, 0.0).
And set the outer bound region.

# 🏃 MeshFem Object

Next link the FEM to Mesh Object. You can use MeshFem object for this task.
In this problem, we'll set the element which called classical Lagrange element as you can see in this picture.
Make MeshFem object by the args mesh object and the degree of freedom of these nodes.
Then set classical Lagrange element by set classical fem method.

# 🏃 Model Object

At the end, make the Model object and build the simultaneous equations.
Set the MeshFem object as the unknown variable of simultaneous equations.

# 🏃 Laplacian Brick, RHS and condition
TODO: change simultunious equation to the using equation.
Next, add a term of PDE, RHS and condition to the Model object.
First, use add Laplacian birck method to add term of Laplacian term.
Next, set the RHS by using add source term brick method.
At the end, set the outer bound region by using the add Dirichlet condition with multipliers method.

# 🏃 Solve u

Now we can set the all of the parameter.
Are you tired of setting so many parameters?
But the task is only make objects one by one.
So that makes you eay.
After make Model object, solve the simultanious equation and export the value of u.

# 🎉 variable u

This is the result of 'u'.
The contour makes the circle.
But this result is correct?

# theoretical solution

Let's compare with the theoretical solution.
The theoretical solution of this problem is this.
Let's export to figure and see it.

# theoretical solution

This is the result of theoretical solution.
It seems to equal to the value of u which we solve by GetFEM.

### 🎉 Compare

And this is the diffrence of the GetFEM++'s result and the theoretical solution.
The diffrence is small and the correctness of GetFEM++ can be probed.

# Summary

I talk about these contents.
Overview of the GetFEM++
Installation of the GetFEM++
Example of the GetFEM++

### Thank you 🙇

And Thank you for listening my presentation.
