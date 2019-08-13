2019-Aug-12

Jeffrey Sarnoff 10:48
The IEEE committee is pushing the use of NaNs with circumstance or state specific unsigned payloads as a way to provide more efficient vector processing, SIMD solving, multithreaded computation and out-of-order calculation. Exception handling and other standard error recognition and reclamation approaches are taking an economic toll.
Moritz Schauer:juliamath: 10:50
Care you explain a bit?

Jeffrey Sarnoff 11:03
In each case the NaN is really a non-number that carries its own "payload" (essentially, an unsigned integer).  So, say we use this NaN <NaN with payload (123321)> to fill any uninitialized IEEEFloat values (e.g. Array{Float64, 1}(uninit, 16) the uninit value is not a flag or a singleton type, it is that NaN.  Now should that array be used improperly, and an uninitialized value is fetched and added into a running sum, at the end of the run .. that sum will be a NaN, in particular, the <NaN with payload (123321)>.  isnan(result) identifies the result as exceptional and then the new IEEE754-2019 function getpayload(x) recovers 123321.0 which is used to determine the nature of the exception (bad access logic).  Determining that at the end of the sum rather than tripping a catch during the summation allows all the non-exceptional code to run apace rather than being required to provide a second path for exception handling within the, perhaps GPU based computations.
SIMD likes to know what to do, do it, be completed.  SIMD is not well suited to do this, check that, do this more, check that more -- still good? be completed.

Moritz Schauer:juliamath: 11:07
This sounds reasonable, but you were worried about the drawbacks?

Jeffrey Sarnoff 11:08
I do not worry.  I am glad you asked me to elaborate. (edited) 

Jeffrey Sarnoff 11:20
This is a sea change in floating point exception handling.  Given that any economic advantage that goes with letting multisimdy go with its best feets forward will continue to grow nonlinearly .. this is of strategic moment.  Where an exception would have been caught, a NaN(specific payload) is substituted for the result that yields.  And it propagates without delay (hardware supported [although there is a caveat, op(NaN1, NaN2) is permitted to yield either .. that's ok, though in real life -- if there are two NaN operands, there there certainly has been a floating point processing exception and some info is better than no info]).  It will require some thought and planning for design to get this happening correctly and appropriately with Julia v2.  The more it be used, the greater the future advantage of having that expertise engineered into the performance of the language.
:raised_hands:
2

Moritz Schauer:juliamath: 11:22
Thank you for the insight :+1:

Foo Bar 15:36
I always enjoy the Agner Fog takes https://www.agner.org/optimize/blog/read.php?i=1012
What this imo boils down to is: IEEE needs to get their act together, then hardware manufacturers need to get their act together, and then programming languages and programmers. I guess for julia this means that nothing should change about nan handling in the near future (since we realistically program against intel/amd/arm/power/cuda/opencl, and not against ieee).

Jeffrey Sarnoff 16:01
We program to obtain advantage using intel/amd/../cuda/opencl.  That performant practice often entails some multithreading or SIMD or other parallelisms is not an IEEE position.  That judicious application of NaNs for this one purpose (allowing uninterrupted correct calculations while still catching errors, just later in the dataflow) is considered to work well and shave some unnecessary paths is useful even as others wait for Godot.
[ Agner Fog refined his thoughts on this here:   https://www.agner.org/optimize/nan_propagation.pdf ]

Harmen Stoppels 16:44
If NaN propagation makes certain vectorized operations fast, what would that mean for Julia’s arrays of floats with missing data? Missing data is not implemented in terms of signaling NaNs like R does, right?

Alex Arslan:megaman: 16:48
Correct it is not

Jeffrey Sarnoff 17:58
A great deal of design and optimization powers the way Julia handles vectors/arrays with eltype that is the Union of Missing or Nothing and a hardware Float or Int: Union{Missing, Float64}, Union{Nothing, Int32}.  Even three of these kinds is performant: Union{Missing, Float32, Int32}, Union{Missing, Float64, Float32}`.
The missing values are just carried within the sequence until accessed-and-applied, where missing often propagates and, when a function that has no dispatch path defined for missing as an operand, errors.  With functions that have specialized dispatch over missings, processing does not skip a beat and dataflow moves.
The utilization of NaN propagation alongside math with finite floats, possibly with missing data,  does deserve attention.  At present:  missing + NaN == NaN + missing == missing, missing dominates NaN and so erases any information that the NaN's payload would have carried.  We have at our fingertips this restatement
Base.:(+)(x::Missing, y::Float64) = isnan(y) ? y : x; Base.:(+)(x::Float64, y::Missing) = isnan(x) : x : y.
Essentially, o(missing, NaN) --> NaN, eats more entropy than --> missing.  It is aligned with Julia's ordering, too: sort([1.0, NaN, missing, 2.0, missing, NaN, 3.0]) == [1.0,  2.0,  3.0,  NaN,  NaN,  missing,  missing]
as I understand this today
