# SimpleTools

Miscellaneous code that is possibly useful
for my `SimpleWorld` modules.


## Composition of dictionaries

Dictionaries are like functions and so it makes sense
to define a composition operation for them.

Suppose that `f` and `g` are dictionaries such
that all the values in `f` appear as keys in `g`.
Then, if `k` is any key of `f` the expression `g[f[k]]`
is defined. In this case, we may compute `g*f` to
yield a new dictionary `h` with the same keys as `f`
and for which `h[k] == g[f[k]]` for all keys `k`.

Warnings are issued under the following circumstances:
+ If some value of `f` is not a key of `g`.
+ If the type of the values in `f` doesn't match
the key type for `g`.

#### Examples

Here is an example without any warnings.

```julia
julia> f = Dict([("alpha", 1), ("bravo", 2)])
Dict{ASCIIString,Int64} with 2 entries:
  "alpha" => 1
  "bravo" => 2

julia> g = Dict([(1,3.14), (2,2.718), (3,1.618)])
Dict{Int64,Float64} with 3 entries:
  2 => 2.718
  3 => 1.618
  1 => 3.14

julia> g*f
Dict{ASCIIString,Float64} with 2 entries:
  "alpha" => 3.14
  "bravo" => 2.718
```

And this is an example in which problems arise
that are not so serious that the composition fails:

```julia
julia> f = Dict([("alpha", 1), ("bravo", 2)])
Dict{ASCIIString,Int64} with 2 entries:
  "alpha" => 1
  "bravo" => 2

julia> g = Dict([(1.0, 3.33)])
Dict{Float64,Float64} with 1 entry:
  1.0 => 3.33

julia> g*f
WARNING: Dictionary type mismatch
WARNING: 1 keys were not mapped
Dict{ASCIIString,Float64} with 1 entry:
  "alpha" => 3.33
```

## Counters

We often want to count things and a way to do that is to create a dictionary
that maps objects to their counts. A `Counter` object simplifies that
process. Say we want to count values of type `ASCIIString`. We would
create a counter for that type like this:
```julia
julia> c = Counter{String}()
Counter{String} with 0 entries
```

The two primary operations for a `Counter` are value increment and
value retrieval. To increment the value of a counter we do this:
```julia
julia> c["hello"] += 1
1
```
To access the count, we use square brackets:
```julia
julia> c["hello"]
1

julia> c["bye"]
0
```
Notice that we need not worry about whether or not a key is
already known to the `Counter`. If presented with an unknown key,
the `Counter` assumes its value is `0`.

A `Counter` may be assigned to like this `c["alpha"]=4` but
the more likely use case is `c["bravo"]+=1` invoked each
time a value, such as `"bravo"` is encountered.


#### Addition of counters

If `c` and `d` are counters (of the same type of object) their sum
`c+d` creates a new counter by adding the values in `c` and `d`. That
is, if `a=c+d` and `k` is any key, then `a[k]` equals `c[k]+d[k]`.

#### More functions

* `sum(c)` returns the sum of the values in `c`; that is, the total
of all the counts.
* `length(c)` returns the number of values held in `c`. Note that
this might include objects with value `0`.
* `nnz(c)` returns the number of nonzero values held
in `c`.
* `keys(c)` returns an iterator for the keys held by `c`.
* `showall(c)` gives a print out of all the keys and their nonzero
values in `c`.
* `reset!(c)` sets all counts in `c` to `0`.
* `clean!(c)` removes all keys from `c` whose value is `0`. This
won't change its behavior, but will free up some memory.

#### To do list

* Document how to use this for parallel computation.
* A `Counter` should be a subtype of `Associative` and so we need
to implement additional functions to make that legit.
