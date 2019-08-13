2019-Aug-11
Jeffrey Sarnoff  1 day ago

from IEEE 754-2019: sec 9.2 Additional mathematical operations
"Language standards should define, to be implemented according to this subclause, as many of the operations in Table 9.1 as is appropriate to the language. As with other operations of this standard, the
names of the operations in Table 9.1 do not necessarily correspond to the names that any particular
programming language would use. In this subclause the domain of an operation is that subset of the affinely extended reals for which the corresponding mathematical function is well defined. A conforming operation shall return results correctly rounded for the applicable rounding direction for all operands in its domain."
to include

rsqrt(x) = 1 / sqrt(x)

compound(x, n) = (1 + x)^n

rootn(x, n) = x^(1/n)
pown(x,n) = x^n
pow(x, y) = x^y [-Inf, +Inf] x [-Inf, +Inf] 
powr(x,y) = x^y [0,+Inf] x [-Inf, +Inf]

exp2m1(x)  = 2^x - 1
exp10m1(x) = 10^x - 1

log2p1(x)  = llog2(1+x)
log10p1(x) = log10(1+x)


trigonometric functions

sinpi(x) = sin(π * x)
cospi(x) = cos(π * x)
tanpi(x) = tan(π * x)


asinpi(x) = asin(x) / π
acospi(x) = acos(x) / π
atanpi(x) = atan(x) / π

atan2Pi(y, x) is the angle subtended at the origin by the point (x, y)
    and the positive x-axis. The range of atan2Pi is [−1, +1].

    atan2pi(+Inf) = +0.5
    atan2pi(-Inf) = -0.5
there are other defined values (for this and the above ops)

  

Stefan Karpinski  5 hours ago
@jeffrey_sarnoff, you should post these in an issue so that we make sure to get them all implemented

Stefan Karpinski  5 hours ago
having the fill modern IEEE suite of functions would be a good feature

Jeffrey Sarnoff  5 hours ago
ok will do

Jeffrey Sarnoff  5 hours ago
https://github.com/JuliaLang/julia/issues/32877
