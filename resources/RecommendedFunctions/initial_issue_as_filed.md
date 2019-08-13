2019-Aug-12

Jeffrey Sarnoff
https://github.com/JuliaLang/julia/issues/32877


from IEEE 754-2019: sec 9.2 Additional mathematical operations

"Language standards should define, to be implemented according to this subclause, as many of the operations in Table 9.1 as is appropriate to the language. As with other operations of this standard, the names of the operations in Table 9.1 do not necessarily correspond to the names that any particular programming language would use. In this subclause the domain of an operation is that subset of the affinely extended reals for which the corresponding mathematical function is well defined. A conforming operation shall return results correctly rounded for the applicable rounding direction for all operands in its domain."

these functions include

rsqrt(x) ≝ 1 / sqrt(x)
compound(x, n) ≝ (1 + x)^n

rootn(x, n) ≝ x^(1/n)
pown(x,n) ≝ x^n
pow(x, y) ≝ x^y [-Inf, +Inf] x [-Inf, +Inf] 
powr(x,y) ≝ x^y [0,+Inf] x [-Inf, +Inf]

exp2m1(x)  ≝ 2^x - 1
exp10m1(x) ≝ 10^x - 1

log2p1(x)  ≝ llog2(1+x)
log10p1(x) ≝ log10(1+x)


sinpi(x) ≝ sin(π * x)
cospi(x) ≝ cos(π * x)
tanpi(x) ≝ tan(π * x)

asinpi(x) ≝ asin(x) / π
acospi(x) ≝ acos(x) / π
atanpi(x) ≝ atan(x) / π

atan2pi(y, x) is the angle subtended at the origin by the point (x, y)
    and the positive x-axis. The range of atan2pi is [−1, +1].
there are short tables of required y = f(x) where x in {±0, ±∞}
for many of these functions

getpayload(x)
    if the source operand is a NaN, the result is the payload
        as a non-negative floating-point integer.
    if the source operand is not a NaN, the result is −1.

setpayload(x)
    If the source operand is a non-negative floating-point integer whose value
        is one of an implementation-defined set of admissible payloads for
        the operation, the result is a quiet NaN with that payload. 
    Otherwise, the result is +0, with a preferred exponent of 0.

setpayloadsignaling(x)
    like setpayload, the result is a signaling NaN
