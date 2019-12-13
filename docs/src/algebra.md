# Grassmann elements and geometric algebra Λ(V)

The Grassmann `Basis` elements `vₖ` and `wᵏ` are linearly independent vector and covector elements of `V`, while the Leibniz `Operator` elements `∂ₖ` are partial tangent derivations and `ϵᵏ` are dependent functions of the `tangent` manifold.
Higher `grade` elements correspond to `SubManifold` subspaces, while higher `order` function elements become homogenous polynomials and Taylor series.

Combining the linear basis generating elements with each other using the multilinear tensor product yields a graded (decomposable) tensor `Basis` ⟨w₁⊗⋯⊗wₖ⟩, where `grade` is determined by the number of anti-symmetric basis elements in its tensor product decomposition.
The algebra is partitioned into both symmetric and anti-symmetric tensor equivalence classes.
Higher-order composite tensor elements are oriented-multi-sets.
Anti-symmetric indices have two orientations and higher multiplicities of them result in zero values, so the only interesting multiplicity is 1.
The Leibniz-Taylor algebra is a quotient polynomial ring  so that `ϵₖ^(μ+1)` is zero.

By virtue of Julia's multiple dispatch on the field type `T`, methods can specialize on the dimension `N` and grade `G` with a `VectorBundle{N}` via the `TensorAlgebra{V}` subtypes, such as `Basis{V,G}`, `Simplex{V,G,B,T}`, `Chain{V,G,T}`, `SparseChain{V,G,T}`, `MultiVector{V,T}`, and `MultiGrade{V,G}` types.

For the oriented sets of the Grassmann exterior algebra, the parity of `(-1)^P` is factored into transposition compositions when interchanging ordering of the tensor product argument permutations.
The symmetrical algebra does not need to track this parity, but has higher multiplicities in its indices.
Symmetric differential function algebra of Leibniz trivializes the orientation into a single class of index multi-sets, while Grassmann's exterior algebra is partitioned into two oriented equivalence classes by anti-symmetry.
Full tensor algebra can be sub-partitioned into equivalence classes in multiple ways based on the element symmetry, grade, and metric signature composite properties.
Both symmetry classes can be characterized by the same geometric product.

Grassmann's exterior algebra doesn't invoke the properties of multi-sets, as it is related to the algebra of oriented sets; while the Leibniz symmetric algebra is that of unoriented multi-sets.
Combined, the mixed-symmetry algebra yield a multi-linear propositional lattice.
The formal sum of equal `grade` elements is an oriented `Chain` and with mixed `grade` it is a `MultiVector` simplicial complex.
Thus, various standard operations on the oriented multi-sets are possible including `∪,∩,⊕` and the index operation `⊖`, which is symmetric difference operation `⊻`.

The elements of the `Algebra` can be generated in many ways using the `Basis` elements created by the `@basis` macro,
```Julia
julia> using Grassmann; @basis ℝ'⊕ℝ^3 # equivalent to basis"-+++"
(⟨-+++⟩, v, v₁, v₂, v₃, v₄, v₁₂, v₁₃, v₁₄, v₂₃, v₂₄, v₃₄, v₁₂₃, v₁₂₄, v₁₃₄, v₂₃₄, v₁₂₃₄)
```
As a result of this macro, all of the `Basis{V,G}` elements generated by that `VectorBundle` become available in the local workspace with the specified naming.
The first argument provides signature specifications, the second argument is the variable name for the `VectorBundle`, and the third and fourth argument are the the prefixes of the `Basis` vector names (and covector basis names). By default, `V` is assigned the `VectorBundle` and `v` is the prefix for the `Basis` elements.
```Julia
julia> V # Minkowski spacetime
⟨-+++⟩

julia> typeof(V) # dispatch by vector space
VectorBundle{4,0,0x0000000000000001}

julia> typeof(v13) # extensive type info
Basis{⟨-+++⟩,2,0x0000000000000005}

julia> v13∧v2 # exterior tensor product
-1v₁₂₃

julia> ans^2 # applies geometric product
1v

julia> @btime 2v1+v3 # vector element
  37.794 ns (3 allocations: 80 bytes)
2v₁ + 0v₂ + 1v₃ + 0v₄

julia> @btime $ans⋅$ans # inner product
  15.266 ns (3 allocations: 48 bytes)
-3v
```
It is entirely possible to assign multiple different bases with different signatures without any problems. In the following command, the `@basis` macro arguments are used to assign the vector space name to `S` instead of `V` and basis elements to `b` instead of `v`, so that their local names do not interfere:
```Julia
julia> @basis "++++" S b;

julia> let k = (b1+b2)-b3
           for j ∈ 1:9
               k = k*(b234+b134)
               println(k)
       end end
0 + 1v₁₄ + 1v₂₄ + 2v₃₄
0 - 2v₁ - 2v₂ + 2v₃
0 - 2v₁₄ - 2v₂₄ - 4v₃₄
0 + 4v₁ + 4v₂ - 4v₃
0 + 4v₁₄ + 4v₂₄ + 8v₃₄
0 - 8v₁ - 8v₂ + 8v₃
0 - 8v₁₄ - 8v₂₄ - 16v₃₄
0 + 16v₁ + 16v₂ - 16v₃
0 + 16v₁₄ + 16v₂₄ + 32v₃₄
```
Alternatively, if you do not wish to assign these variables to your local workspace, the versatile `Grassmann.Algebra{N}` constructors can be used to contain them, which is exported to the user as the method `Λ(V)`,
```Julia
julia> G3 = Λ(3) # equivalent to Λ(V"+++"), Λ(ℝ^3), Λ.V3
Grassmann.Algebra{⟨+++⟩,8}(v, v₁, v₂, v₃, v₁₂, v₁₃, v₂₃, v₁₂₃)

julia> G3.v13 * G3.v12
v₂₃
```
The *geometric algebraic product* is the oriented symmetric difference operator `⊖` (weighted by the bilinear form `g`) and multi-set sum `⊕` applied to multilinear tensor products `⊗` in a single operation.
Symmetry properties of the tensor algebra can be characterized in terms of the geometric product by two averaging operations, which are the symmetrization `⊙` and anti-symmetrization `⊠` operators.
These products satisfy various `MultiVector` properties, including the associative and distributive laws.


It is possible to assign the **quaternion** generators `i,j,k` with
```Julia
julia> i,j,k = hyperplanes(ℝ^3)
3-element Array{Simplex{⟨+++⟩,2,B,Int64} where B,1}:
 -1v₂₃
 1v₁₃
 -1v₁₂

julia> @btime $i^2, $j^2, $k^2, $i*$j*$k
  0.027 ns (0 allocations: 0 bytes)
(-1v, -1v, -1v, -1v)

julia> @btime -(j+k) * (j+k)
  97.373 ns (4 allocations: 176 bytes)
2.0v⃖

julia> @btime -(j+k) * i
  67.695 ns (3 allocations: 144 bytes)
0.0 - 1.0v₁₂ - 1.0v₁₃
```
Alternatively, another representation of the quaternions is
```Julia
julia> basis"--"
(⟨--⟩, v, v₁, v₂, v₁₂)

julia> v1^2, v2^2, v12^2, v1*v2*v12
(-1v, -1v, -1v, -1v)
```
With the preliminary implementations of the `exp` and `log` functions one can compute
```Julia
julia> exp(0.5π/2*(i+j)/sqrt(2))
0.7071067811865476 + 0.5v₁₃ - 0.5v₂₃

julia> ans == (sqrt(2)+j+i)/2
true

julia> log1p(i)
0.34657359027997264 - 0.7853981633974485v₂₃

julia> log(i)
0.0 - 1.5708963467978978v₂₃
```
The parametric type formalism in `Grassmann` is highly expressive to enable the pre-allocation of geometric algebra computations for specific sparse-subalgebras, including the representation of rotational groups, Lie bivector algebras, and affine projective geometry.

Together with [LightGraphs,jl](https://github.com/JuliaGraphs/LightGraphs.jl), [GraphPlot.jl](https://github.com/JuliaGraphs/GraphPlot.jl), [Cairo.jl](https://github.com/JuliaGraphics/Cairo.jl), [Compose.jl](https://github.com/GiovineItalia/Compose.jl) it is possible to convert `Grassmann` numbers into graphs.
```Julia
using Grassmann, Compose # environment: LightGraphs, GraphPlot
x = Grassmann.Algebra(ℝ^7).v123
Grassmann.graph(x+!x)
draw(PDF("simplex.pdf",16cm,16cm),x+!x)
```
![paper/img/triangle-tetrahedron.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/triangle-tetrahedron.png)

Due to [GeometryTypes,jl](https://github.com/JuliaGeometry/GeometryTypes.jl) `Point` interoperability, plotting and visualizing with [Makie.jl](https://github.com/JuliaPlots/Makie.jl) is easily possible. For example, the `vectorfield` method creates an anonymous `Point` function that applies a versor outermorphism:
```Julia
using Grassmann, Makie
basis"2" # Euclidean
streamplot(vectorfield(exp(π*v12/2)),-1.5..1.5,-1.5..1.5)
streamplot(vectorfield(exp((π/2)*v12/2)),-1.5..1.5,-1.5..1.5)
streamplot(vectorfield(exp((π/4)*v12/2)),-1.5..1.5,-1.5..1.5)
streamplot(vectorfield(v1*exp((π/4)*v12/2)),-1.5..1.5,-1.5..1.5)
@basis S"+-" # Hyperbolic
streamplot(vectorfield(exp((π/8)*v12/2)),-1.5..1.5,-1.5..1.5)
streamplot(vectorfield(v1*exp((π/4)*v12/2)),-1.5..1.5,-1.5..1.5)
```
![paper/img/plane-1.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/plane-1.png) ![paper/img/plane-2.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/plane-2.png)
![paper/img/plane-3.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/plane-3.png) ![paper/img/plane-4.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/plane-4.png)
![paper/img/plane-3.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/plane-5.png) ![paper/img/plane-4.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/plane-6.png)

```Julia
using Grassmann, Makie
@basis S"∞+++"
f(t) = (↓(exp(π*t*((3/7)*v12+v∞3))>>>↑(v1+v2+v3)))
lines(points(f,V(2,3,4)))
@basis S"∞∅+++"
f(t) = (↓(exp(π*t*((3/7)*v12+v∞3))>>>↑(v1+v2+v3)))
lines(points(f,V(3,4,5)))
```
![paper/img/torus.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/torus.png) ![paper/img/helix.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/helix.png)

```Julia
using Grassmann, Makie; @basis S"∞+++"
streamplot(vectorfield(exp((π/4)*(v12+v∞3)),V(2,3,4)),-1.5..1.5,-1.5..1.5,-1.5..1.5,gridsize=(10,10))
```
![paper/img/orb.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/orb.png)

```Julia
using Grassmann, Makie; @basis S"∞+++"
streamplot(vectorfield(exp((π/4)*(v12+v∞3)),V(2,3,4),V(1,2,3)),-1.5..1.5,-1.5..1.5,-1.5..1.5,gridsize=(10,10))
```
![paper/img/wave.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/wave.png)

```Julia
using Grassmann, Makie; @basis S"∞+++"
f(t) = ↓(exp(t*v∞*(sin(3t)*3v1+cos(2t)*7v2-sin(5t)*4v3)/2)>>>↑(v1+v2-v3))
lines(points(f,V(2,3,4)))
```
![paper/img/orb.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/orbit-2.png)

```Julia
using Grassmann, Makie; @basis S"∞+++"
f(t) = ↓(exp(t*(v12+0.07v∞*(sin(3t)*3v1+cos(2t)*7v2-sin(5t)*4v3)/2))>>>↑(v1+v2-v3))
lines(points(f,V(2,3,4)))
```
![paper/img/orb.png](https://raw.githubusercontent.com/chakravala/Grassmann.jl/master/paper/img/orbit-4.png)

## Approaching ∞ dimensions with `SparseAlgebra` and `ExtendedAlgebra`

In order to work with a `TensorAlgebra{V}`, it is necessary for some computations to be cached. This is usually done automatically when accessed.
```Julia
julia> Λ(7) + Λ(7)'
Grassmann.SparseAlgebra{⟨+++++++-------⟩*,16384}(v, ..., v₁₂₃₄₅₆₇w¹²³⁴⁵⁶⁷)
```
One way of declaring the cache for all 3 combinations of a `VectorBundle{N}` and its dual is to ask for the sum `Λ(V) + Λ(V)'`, which is equivalent to `Λ(V⊕V')`, but this does not initialize the cache of all 3 combinations unlike the former.

Staging of precompilation and caching is designed so that a user can smoothly transition between very high dimensional and low dimensional algebras in a single session, with varying levels of extra caching and optimizations.
The parametric type formalism in `Grassmann` is highly expressive and enables pre-allocation of geometric algebra computations involving specific sparse subalgebras, including the representation of rotational groups.

It is possible to reach `Simplex` elements with up to `N=62` vertices from a `TensorAlgebra` having higher maximum dimensions than supported by Julia natively.
```Julia
julia> Λ(62)
Grassmann.ExtendedAlgebra{⟨++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++⟩,4611686018427387904}(v, ..., v₁₂₃₄₅₆₇₈₉₀abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ)

julia> Λ(62).v32a87Ng
-1v₂₃₇₈agN
```
The 62 indices require full alpha-numeric labeling with lower-case and capital letters. This now allows you to reach up to `4,611,686,018,427,387,904` dimensions with Julia `using Grassmann`. Then the volume element is
```Julia
v₁₂₃₄₅₆₇₈₉₀abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
```
Full `MultiVector` allocations are only possible for `N≤22`, but sparse operations are also available at higher dimensions.
While `Grassmann.Algebra{V}` is a container for the `TensorAlgebra` generators of `V`, the `Grassmann.Algebra` is only cached for `N≤8`.
For the range of dimensions `8<N≤22`$, the `Grassmann.SparseAlgebra` type is used.
```Julia
julia> Λ(22)
Grassmann.SparseAlgebra{⟨++++++++++++++++++++++⟩,4194304}(v, ..., v₁₂₃₄₅₆₇₈₉₀abcdefghijkl)
```
This is the largest `SparseAlgebra` that can be generated with Julia, due to array size limitations.

To reach higher dimensions with `N>22`, the `Grassmann.ExtendedAlgebra` type is used.
It is suficient to work with a 64-bit representation (which is the default). And it turns out that with 62 standard keyboard characters, this fits nicely.
```Julia
julia> V = ℝ^22
⟨++++++++++++++++++++++⟩

julia> Λ(V+V')
Grassmann.ExtendedAlgebra{⟨++++++++++++++++++++++----------------------⟩*,17592186044416}(v, ..., v₁₂₃₄₅₆₇₈₉₀abcdefghijklw¹²³⁴⁵⁶⁷⁸⁹⁰ABCDEFGHIJKL)
```
At 22 dimensions and lower there is better caching, with further extra caching for 8 dimensions or less.
Thus, the largest Hilbert space that is fully reachable has 4,194,304 dimensions, but we can still reach out to 4,611,686,018,427,387,904 dimensions with the `ExtendedAlgebra` built in.
It is still feasible to extend to a further super-extended 128-bit representation using the `UInt128` type (but this will require further modifications of internals and helper functions.
To reach into infinity even further, it is theoretically possible to construct ultra-extensions also using dictionaries.
Full `MultiVector` elements are not representable when `ExtendedAlgebra` is used, but the performance of the `Basis` and sparse elements should be just as fast as for lower dimensions for the current `SubAlgebra` and `TensorAlgebra` types.
The sparse representations are a work in progress to be improved with time.

## Null-basis of the projective split

In the following example, the null-basis from the projective split is used:
```Julia
julia> using Grassmann; @basis S"∞∅++"
(⟨∞∅++⟩, v, v∞, v∅, v₁, v₂, v∞∅, v∞₁, v∞₂, v∅₁, v∅₂, v₁₂, v∞∅₁, v∞∅₂, v∞₁₂, v∅₁₂, v∞∅₁₂)

julia> v∞^2, v∅^2, v1^2, v2^2
(0v, 0v, v, v)

julia> v∞ ⋅ v∅
-1v

julia> v∞∅^2
v

julia> v∞∅ * v∞, v∞∅ * v∅
(-1v∞, v∅)

julia> v∞ * v∅, v∅ * v∞
(-1 + 1v∞∅, -1 - 1v∞∅)
```

## Differential forms and Leibniz tangent algebra

Multiplication with an `ϵᵢ` element is used help signify tensor fields so that differential operators are automatically applied in the `Basis` algebra as ∂ⱼ⊖(ω⊗ϵᵢ) = ∂ⱼ(ωϵᵢ) ≠ (∂ⱼ⊗ω)⊖ϵᵢ.
```Julia
julia> using Reduce, Grassmann; @mixedbasis tangent(ℝ^2,3,2);

julia> (∂1+∂12) * (:(x1^2*x2^2)*ϵ1 + :(sin(x1))*ϵ2)
0.0 + (2 * x1 * x2 ^ 2)∂₁ϵ¹ + (cos(x1))∂₁ϵ² + (4 * x1 * x2)∂₁₂ϵ¹
```

The product rule is encoded into `Grassmann` algebra when a `tangent` bundle is used, demonstrated here symbolically with `Reduce` by using the dual number definition:
```Julia
julia> using Grassmann, Reduce
Reduce (Free CSL version, revision 4590), 11-May-18 ...

julia> @mixedbasis tangent(ℝ^1)
(⟨+-₁¹⟩*, v, v₁, w¹, ϵ₁, ∂¹, v₁w¹, v₁ϵ₁, v₁∂¹, w¹ϵ₁, w¹∂¹, ϵ₁∂¹, v₁w¹ϵ₁, v₁w¹∂¹, v₁ϵ₁∂¹, w¹ϵ₁∂¹, v₁w¹ϵ₁∂¹)

julia> a,b = :x*v1 + :dx*ϵ1, :y*v1 + :dy*ϵ1
(xv₁ + dxϵ₁, yv₁ + dyϵ₁)

julia> a * b
x * y + (dy * x + dx * y)v₁ϵ₁
```
Higher order and multivariable Taylor numbers are also supported.
```Julia
julia> @basis tangent(ℝ,2,2) # 1D Grade, 2nd Order, 2 Variables
(⟨+₁₂⟩, v, v₁, ∂₁, ∂₂, ∂₁v₁, ∂₂v₁, ∂₁₂, ∂₁₂v₁)

julia> ∂1 * ∂1v1
∂₁∂₁v₁

julia> ∂1 * ∂2
∂₁₂

julia> v1*∂12
∂₁₂v₁

julia> ∂12*∂2 # 3rd order is zero
0v

julia> @mixedbasis tangent(ℝ^2,2,2); # 2D Grade, 2nd Order, 2 Variables

julia> ∇ = ∂1v1 + ∂2v2 # vector field
0v₁₂ + 1∂₁v₁ + 0∂₂v₁ + 0∂₁v₂ + 1∂₂v₂ + 0∂₁₂

julia> ∇ ⋅ ∇ # Laplacian
0.0v₁ + 0.0v₂ + 1∂₁∂₁ + 1∂₂∂₂

julia> ans*∂1 # 3rd order is zero
0.0v⃖
```
Although fully generalized, the implementation in this release is still experimental.

## Symbolic coefficients by declaring an alternative scalar algebra

Due to the abstract generality of the code generation of the `Grassmann` product algebra, it is easily possible to extend the entire set of operations to other kinds of scalar coefficient types.
```Julia
julia> using GaloisFields, Grassmann

julia> const F = GaloisField(7)
𝔽₇

julia> basis"2"
(⟨++⟩, v, v₁, v₂, v₁₂)

julia> @btime F(3)*v1
  21.076 ns (2 allocations: 32 bytes)
3v₁

julia> @btime inv($ans)
  14.965 ns (0 allocations: 0 bytes)
5v₁
```
By default, the coefficients are required to be `<:Number`. However, if this does not suit your needs, alternative scalar product algebras can be specified with
```Julia
Grassmann.generate_algebra(:AbstractAlgebra,:SetElem)
```
where `:SetElem` is the desired scalar field and `:AbstractAlgebra` is the scope which contains the scalar field.

With the usage of `Requires`, symbolic scalar computation with [Reduce.jl](https://github.com/chakravala/Reduce.jl) and other packages is automatically enabled, e.g.
```Julia
julia> using Reduce, Grassmann
Reduce (Free CSL version, revision 4590), 11-May-18 ...

julia> basis"2"
(⟨++⟩, v, v₁, v₂, v₁₂)

julia> (:a*v1 + :b*v2) ⋅ (:c*v1 + :d*v2)
(a * c + b * d)v

julia> (:a*v1 + :b*v2) ∧ (:c*v1 + :d*v2)
0.0 + (a * d - b * c)v₁₂

julia> (:a*v1 + :b*v2) * (:c*v1 + :d*v2)
a * c + b * d + (a * d - b * c)v₁₂
```
If these compatibility steps are followed, then `Grassmann` will automatically declare the product algebra to use the `Reduce.Algebra` symbolic field operation scope.

```Julia
julia> using Reduce,Grassmann; basis"4"
Reduce (Free CSL version, revision 4590), 11-May-18 ...
(⟨++++⟩, v, v₁, v₂, v₃, v₄, v₁₂, v₁₃, v₁₄, v₂₃, v₂₄, v₃₄, v₁₂₃, v₁₂₄, v₁₃₄, v₂₃₄, v₁₂₃₄)

julia> P,Q = :px*v1 + :py*v2 + :pz* v3 + v4, :qx*v1 + :qy*v2 + :qz*v3 + v4
(pxv₁ + pyv₂ + pzv₃ + 1.0v₄, qxv₁ + qyv₂ + qzv₃ + 1.0v₄)

julia> P∧Q
0.0 + (px * qy - py * qx)v₁₂ + (px * qz - pz * qx)v₁₃ + (px - qx)v₁₄ + (py * qz - pz * qy)v₂₃ + (py - qy)v₂₄ + (pz - qz)v₃₄

julia> R = :rx*v1 + :ry*v2 + :rz*v3 + v4
rxv₁ + ryv₂ + rzv₃ + 1.0v₄

julia> P∧Q∧R
0.0 + ((px * qy - py * qx) * rz - ((px * qz - pz * qx) * ry - (py * qz - pz * qy) * rx))v₁₂₃ + (((px * qy - py * qx) + (py - qy) * rx) - (px - qx) * ry)v₁₂₄ + (((px * qz - pz * qx) + (pz - qz) * rx) - (px - qx) * rz)v₁₃₄ + (((py * qz - pz * qy) + (pz - qz) * ry) - (py - qy) * rz)v₂₃₄
```

It should be straight-forward to easily substitute any other extended algebraic operations and fields; issues with questions or pull-requests to that end are welcome.
