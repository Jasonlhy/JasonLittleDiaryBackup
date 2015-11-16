title: 'Solve the problem in ghci : attempting to use module ... which is not loaded'
date: 2013-06-14 11:41:10
description:
categories: Problem
tags: Haskell
---

# Problem
I am reading [Learn you a Haskell for great Good](http://learnyouahaskell.com) and I faced the problem of "attempting to use module ... which is not loaded" when I am at the end of chapter module, finally I got the solution on it


# Steps of Solution
1. Load the source code
2. Set the module to Prelude
3. Import the modules


# Load the source code
Compile the source code into the GHCI

{% codeblock %}
*Geometry.Cuboid> :l Geometry/Cube.hs Geometry/Sphere.hs
[1 of 3] Compiling Geometry.Cuboid  ( Geometry/Cuboid.hs, interpreted )
[2 of 3] Compiling Geometry.Cube    ( Geometry/Cube.hs, interpreted )
[3 of 3] Compiling Geometry.Sphere  ( Geometry/Sphere.hs, interpreted )
Ok, modules loaded: Geometry.Sphere, Geometry.Cuboid, Geometry.Cube.
{% endcodeblock %}


# Set the module to Prelude
Change back the current module in the GHCI to Prelude, by default the last module loaded becomes the current module.

{% codeblock %}
*Geometry.Cube> :module Prelude
{% endcodeblock %}


# Import the modules

Import the modules we just loaded in the GHCI, and you can see the qualified name of the module will be append into the namespace.

{% codeblock %}
Prelude> import qualified Geometry.Sphere as Sphere 
Prelude Sphere> import qualified Geometry.Cuboid as Cuboid 
Prelude Sphere Cuboid> import qualified Geometry.Cube as Cube 
Prelude Sphere Cuboid Cube>

{% endcodeblock %}
