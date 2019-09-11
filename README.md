# Solving the Poisson equation on the unit circle

Nice to meet you. My name is Koyama. Thank you for giving me the opportunity to speak today.
Today I'm going to make an announcement under the title of "The Poisson equation on the unit circle is solved.".

# Who are you?

First of all, who on earth are you? So let me introduce myself. I will explain the main activities.
TwitterID is @tkoyama010. This icon is active under the account name of "Get the finite element method". Please follow me if you like. Also, there is no blog, but Booth distributes technical coterie magazines. I have been developing civil engineering and architectural software and performing commissioned analysis at a CAE software vendor for about 8 years. My work includes programming in Fortran, Python, and C + +, as well as work automation using VBScript. I'm a numerical calculator. When I was a student, I was studying at the earthquake research laboratory of the architecture department.
At that time, I became interested in programming by studying the simulation of the behavior of structures. I'll post a link to the research paper, so if you're interested, you can access it later through this presentation.

# Who are you?

Since last year, he has been distributing a translation of the documentation of the OSS library GetFEM++ in the Technical Doujinshi Event Technical Book. It was the second time since I participated in Technical Book 5. He is part of the GetFEM + + project and calls himself a committer. but I have earned a lot of commits for most of my chores.
For example, I've been working on projects for about two years, doing FixTypo, refactoring, translating, and so on. I've learned a lot from just looking at the source code, but recently I wanted to add an object myself, so I added one earlier this year. We are currently adding a second object and hope to finish testing by the end of this month.
As far as I can see, I have about 50 commits at the moment, so I am aiming for 100 commits.

# Questionnaire

Well, this library GetFEM++, provides a framework for solving finite element methods. I would like to do a questionnaire here.
- If you have ever heard of the word "finite element method".
Thank you.
- If you have ever heard of the word "differential equation".
Thank you. Even if you have heard the word differential equation, I think most of you can eat it by finite element method. It's a short time, but I'd like to explain that part first.
Thank you in advance.

# What is the finite element method?

Physical phenomena such as electricity, heat, and the behavior of objects can all be expressed by equations called differential equations.
The finite element method is a method of solving differential equations.
For example, let's solve the phenomenon that the wheel touches the ground like this.
In this way, the area is divided into mesh and the calculation is performed.
The mesh is called an element, and the intersection of the mesh sides is called a node.
You must specify a finite element method and an integral method for each region of the mesh.
The finite element method calculates this unknown variable by converting it into a form of simultaneous equations.

# GetFEM++

A finite element library implemented in C++.
The Python interface is used primarily.
Install the python-getfem++ package on Ubuntu.
By the way, since Python 3 version is already supported, Debian package will be created soon.
Because of its object-oriented implementation, you can simply define an object and call a method to execute it.

# Release Notes

The release notes contain updates for each version, so you can see what's hot right now for development within the library.
It is September now, so the next version will be released in a while.。
The development itself started in the 90s, and the source code is refactored while maintaining backward compatibility.
The current development version supports C++14.

# Installation Instructions @ Ubuntu

Basically, if you use apt or aptitude, you can install it on Ubuntu.
But it's boring, so I'm going to compile and install it with various libraries.

# Installation Instructions @ Ubuntu

Use the usual./configure.
This will verify that the necessary libraries are installed.
The default user interface for GetFEM is Python 2, but you can optionally switch to Python 3.
The next slide describes the required libraries.

# Installation Instructions @ Ubuntu

First, QD and qhull. It's easy to say this from the start, but QD is a library that I'm not sure what it's used for.
The message also says don't worry because only certain people use it.
qhull is a library used to compute the convex structure, which cuts the mesh.

# Installation Instructions @ Ubunt

Then MUMPS, LAPACK/BLAS, and Numpy Scipy.
This area is so famous that you may not need to explain it.
Note that libopenblas-dev is faster than pure blas library.

# Installation Instructions @ Ubuntu

This completes the library installation.
Then you can do a compile-test install with make & & make check & & make install.
I recently noticed that parallel compilation with make-j8 is faster.
If the test is FAIL, please email the development team.
If you don't know, you can email the following mailing list in English.
However, if you want to know the inside of GetFEM before using it, and if you can use the finite element method, It is a good idea to compile.
But if you only want to use it, installing with apt is better.
Now you can install by apt with Windows 10 wsl.
The Python 3 version has not yet been registered in the package, but is pending.

# To create an object

Here is the flow of object creation.
First, you specify which geometries of the Mesher object solve the problem.
Next, you'll create the Mesh object described above, based on the Mesher object.
Create the MeshFem and MeshIm objects by defining the finite element and integral methods for each mesh in the Mesh object, respectively.
Finally, you populate the Model object with MeshFem and MeshIm objects to complete the object.

# This time's topic

This time, I will solve the MathWork problem.
Let's say you have a circle with a radius of 1.0 as shown here, and within that circle you have a differential equation like this one.
Also assume that the variable u is 0 at the boundary of the circle (It's called Dirichlet condition.).

# Creating the Mesher Object

Let's start by creating a Mesher object.

# Creating the Mesher Object

First, import getfem.
Then use the MeserObject function with the arguments' ball ', the center coordinate (1.0, 1.0), and a radius of 1.0 to create a 2-dimensional circle.
As you can see, the center coordinate should be (0.0, 0.0), but MeserObject cannot be negative.
So we'll build the mesh in the center (1.0, 1.0) and later modify the center coordinates to the (0.0, 0.0) location.

# Create a Mesh object

Next, create a Mesh object from the Mesher object.
First, set the approximate length h of the mesh.
In this case, we set 0.5 referring to MathWork's page.
Then use the Mesh function to create a Mesh object.
The arguments are 'generate' and the Mesher objects mo and h that you just created.
Finally, we set the element order to 2.

# Move Mesh and Set Region

The center of the resulting mesh object is in (1.0, 1.0), as described earlier.
Use the translate method to move the mesh into position (0.0, 0.0).
Also set boundaries for mesh objects.
The boundary of the circle must be set at the part where the value of 'u' is 0.0.
Use the set _ region method to set the boundary.
The first argument is the number of the boundary and the second argument is the face number of the boundary.
Since it is difficult to remember the boundary face numbers, we will replace them with OUTER _ BOUND and the variable.
You can also get the face number (In this case, it's a line because it's two-dimensional.) of the circle boundary used as the second argument fb in a method called outer _ faces.
Now that you have a mesh object, this time you have a very simple mesh shape.
Typically, meshes used in production are more complex.
To do so, create mesh objects by importing meshes created with other meshing software.

# Creating a MeshFEM Object

Next, you will create a MeshFEM object that assigns the finite element method to the Mesh. When defining the object, you need the dimension defined in the previous slide for the intersection of the mesh object and the mesh (They are called nodes.). In this case, the node has a dimension of 1, so set it to 1. Then assign the finite element method to the mesh. This time, we'll use the commonly used Lagrange element. The method uses set _ calsical _ fem. You must set the element order. Since we have set the mesh order to 2, let's also set the finite element method order to 2. We used the default finite element method, but GetFEM + + allows you to use other kinds of finite element methods. As you can see on this page, there are various methods, so if you are interested, please see them later.

# Create a Model object

Some of you may already be disappointed with the explanation so far, but it's half done. Please keep in touch with me for a while.
Finally, create a Model object.
This object manages variables and differential equation terms for solving simultaneous equations.
This time, define the variable md as a real Model object.
You then use the md method add _ fem _ variable to define the variable.
The required arguments are the variable name and the corresponding MeshFem object.
This time, use the expression to name the variable 'u', and the MeshFem object uses the mfu you just created.

# Adding Laplacian brick

Next, add a term for the differential equation to the Model object.
First, add this left-hand term of the differential equation.
This term is mathematically called Laplacian.
You use the model object's add _ Laplacian _ brick method to add terms.
The required arguments are a MeshIm object and a variable to add the term to.
The MeshIm object is used to assign the integration method to a mesh.
It is defined by the MeshIm function.
The argument must contain a mesh object and the order of the integration method.
The mesh object is identical to the MeshFEM object, and the degree defines the square of the element's degree.

# Setting Right-Edge Terms and Boundary Conditions

Then we set the right-hand term of the differential equation on the Model object.
We define the value of 1 with the variable name 'F'.
The add _ fem _ variable method was used to define the left term, but the set _ fem _ data method is used to specify the right term.
Specify the variable name and the MeshFEM object as you did for the variable method.
Then the add _ source _ term _ brick method gives the source term 'F' to the variable 'u'.
Since the source term 'F' is a known value before solving the problem, a value of 1.0 is required.
When you set the value of a variable, you use the set _ variable method to set a vector with a value of 1.0.
Finally, set the u = 0 condition for the boundary.
This is called a Dirichlet condition and uses the add _ Dirichlet _ condition _ with _ multipliers method.
The arguments are respectively the mim of the MeshIm object, the variable 'u' added earlier, and the value to be used as the coefficient in the Dirichlet condition (This is usually the element order minus 1.).
It also specifies the area number OUTER _ BOUND that you specified when you created the Mesh object.

# Calculate unknown variable 'u'

Now you have a complete Model object.
You can solve the problem by calling the solve method of the Model object.
You can then use the variable method to retrieve variable data of type nompy.array.
We recommend that you view the results in Mayavi, as introduced in the previous announcement.
Use MeshFem's vtk output to do this.
The method to use is export _ to _ vtk.
Specify the vtk filename and MeshFem object, the retrieved variable 'U', and the variable name to output to the vtk file.

# Calculation of unknown variable 'u'

This is the result of viewing the output vtk file in Mayavi.
As you can see, the values are distributed concentrically from the center.

# calculation of the theoretical solution

The theoretical solution to this problem is shown here.
By comparing with this theoretical solution, I would like to confirm the validity of the result calculated this time.
First, we get the coordinates of each node in nompy.array.
You can use the basic _ dof _ nodes method of the MeshFem object to get the coordinates of each node in nompy.array.
Now that we know the coordinates, we can calculate the theoretical solution and output it to a vtk file for comparison.

# calculation of the theoretical solution

You can see that the values are distributed concentrically, similar to the previous result.

# Comparison between calculated results and theoretical solutions
Compare the calculated results with the theoretical solution.
As a result, even the largest margin of error is 10^-6.

# Creating an Image on JupyterNotebook by Mayavi

Now, I have time to explain how I created this diagram in Mayavi.
Here's Mayavi running on Jupyter to create an image.
First, import mlab from mayavi.
Next, the init_notebook () method initializes the mlab information.
After initialization, the mlab.pipleline.open () method displays the vtk file.
Then use a function called mlab.pipeline.surface to display the contours of the surface.
You can use the scalarbar, show, and savefig methods to display the above diagram and save the image.
It was hard to find a way to view the results of a vtk file in Mayavi, so I hope you're familiar with it.

# Summary

That's all for my presentation, so let me summarize it at the end.
First of all, I briefly introduced what the finite element method is.
This method solves a differential equation on a mesh space by dividing the space with a mesh.
Next, we briefly described the internal structure of GetFEM.
First, you will create a Mesher object and use it to create a Mesh object.
After you create a Mesh object, you create a MeshFem object that assigns a finite element method to the mesh and a MeshIm object that assigns an integration method to the mesh.
Finally, you use the MeshFem and MeshIm objects to build differential equations and boundary conditions on the Model object.
After the build is finished, use the solve method to calculate the unknown variable and real _ variable to get the value of the variable.
MeshFEM methods can output results and Mayavi can plot them.
Finally, let me make an advertisement.
We are distributing technical coterie magazines with examples of such analyses.
The content is an official English tutorial translated into Japanese.
Please read this if you are interested.
Also, when we translate Doujinshi, we do it on a website called Transfex.
If you would like to participate in translation activities, please contact us.
We are also working on a Japanese translation of Mayavi within the same project.
We are working on the translation with the permission of the Mayavi developers on GitHub.
Thank you for your attention.
