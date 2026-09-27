# Language: Programming (§6.8–§6.20, pp. 60–110)
> Read this when drawing: algorithmic figures, custom data structures, data processing, mathematical transformations, functions and arrays, custom operators, module import/organization, or collection types.

## Syntax recap
```asy
// Canonical Asymptote programming patterns: inference, structures, arrays, operators
var origin = (0, 0);                               // 'var' infers pair from initializer
struct Node {
  pair pt;
  string label;
  void operator init(pair pt, string label="") {    // constructor: void return type
    this.pt = pt;
    this.label = label;
  }
}
Node a = Node((1, 2), "A");
Node b = a;                                        // REFERENCE semantics: b aliases a
b.pt = (3, 4);                                     // mutates both b.pt and a.pt (alias(a,b) == true)

int quotient = 7 # 2;                              // '#' is integer division (3); '/' returns real (3.5)
real power = 2^3;                                  // '^' or '**' is exponentiation (8.0), NOT xor
int i = 0;
int next = ++i;                                    // prefix ++ only; postfix i++ DOES NOT EXIST

real[] vals = {1.5, 2.5, 3.5};
vals.push(4.5);                                    // virtual member method on arrays
real[] sub = vals[1:3];                            // half-open slice [m,n) -> {2.5, 3.5}
real[] scaled = sequence(new real(int k) { return 3k^2; }, 5); // sequence generator + implicit scaling 3k

for (var val : vals) {                             // range-based iterator loop
  if (val > 2.0) write(val);
}
```

## Vocabulary

### Variables, Type Inference & Default Initializers (§6.8)
| Type | Default Initializer | Notes |
| :--- | :--- | :--- |
| `int`, `real`, `pair` | `0`, `0.0`, `(0,0)` | Numeric primitives zeroed |
| `triple` | `O` $\equiv (0,0,0)$ | 3D origin point |
| `bool` | `false` | Boolean false |
| `string` | `""` | Empty string |
| `transform` | `identity` | Identity transformation |
| `path`, `guide` | `nullpath` | Empty path/guide |
| `pen` | `defaultpen` | Default black pen, 0.5bp |
| `frame`, `picture` | empty frame / picture | Fresh blank instance |
| `file` | `null` | Null file handle |
| `T[]` | `{}` (length 0) | Empty array; distinct from `null` array |
| `struct T` | `new T` | Fresh instance; inside struct body during def, defaults to `null` |
| `code` | *none* | Must be explicitly initialized via `quote { ... }` |

- `var x = expr;` — type inference; infers static type of variable from unique type of `expr`.
- `T operator init()` — redeclares default initializer for type `T` (used for custom default values).

### Structures & OOP Semantics (§6.9)
- `struct T { ... }` — defines structure `T`; members are `public` by default.
- `restricted <type> <field>;` — readable anywhere, writable only inside enclosing structure methods.
- `private <type> <field>;` — readable and writable only inside enclosing structure methods.
- `this` — expression referencing the current structure instance.
- `new T` — creates and returns a fresh instance reference of structure `T`; top-level body statements run on creation.
- `void operator init(<params>) { ... }` — defines a constructor; return type MUST be `void`. Generates implicit constructor `T(<params>)` and autounravels `from T unravel T;` into enclosing scope.
- `bool alias(T a, T b)` — returns `true` if `a` and `b` reference the identical structure instance (or both are `null`).
- `==`, `!=` on structs — default to `alias(a,b)` and `!alias(a,b)`; can be overridden by user-defined `operator ==`.
- Inheritance-by-containment idiom — embed parent as member `parent parent;`, delegate methods `void f() = parent.f;`, reassign for virtual dispatch `parent.v = v;`, provide upcast `parent operator cast(child c) { return c.parent; }`.

### Operators & Expressions (§6.10)
- `+`, `-`, `*`, `/` — standard arithmetic; `/` between integers returns `real` (e.g. `7/2 == 3.5`).
- `int quotient(int x, int y)` — integer quotient $\lfloor x/y \rfloor$.
- `#` — integer division operator: `x # y` is equivalent to `quotient(x,y)`.
- `%` — modulo operator: result takes the sign of divisor `y` ($q \cdot (p \# q) + p \% q == p$).
- `^`, `**` — power operator: integer exponent uses repeated multiplication; real exponent uses log/exp; boolean operands perform logical xor.
- `==`, `!=`, `<`, `<=`, `>=`, `>` — relational comparisons.
- `&&`, `||`, `!` — logical operators (`&&` and `||` short-circuit).
- `&`, `|`, `^` — boolean bit-equivalent operators (no short-circuit).
- `cond ? expr1 : expr2` — ternary conditional operator.
- `+=`, `-=`, `*=`, `/=`, `#=`, `%=`, `^=` — compound self-assignment operators.
- `++i`, `--i` — prefix increment and decrement. **POSTFIX `i++` AND `i--` DO NOT EXIST.**
- `T interp(T a, T b, real t)` — computes $(1-t)a + tb$; pens are promoted to common color space.
- `int AND(int, int)`, `int OR(int, int)`, `int XOR(int, int)`, `int NOT(int)` — bitwise integer functions.
- `int CLZ(int)`, `int CTZ(int)`, `int popcount(int)`, `int bitreverse(int a, int bits)` — bit-manipulation functions.
- User-definable operators: `- + * / % ^ ! < > == != <= >= & | ^^ .. :: -- --- ++` and (higher precedence) `<< >> $ $$ @ @@ <>`.
- `V operator [](K key)` — defines bracket lookup notation `a[i]`; single arg, non-void return.
- `void operator [=](K key, V value)` — defines bracket assignment notation `a[i] = b`; two args, void return.

### Implicit Scaling (§6.11)
- Numeric literal placed immediately before a variable, parenthesis, or function call acts as multiplication:
  `3x` $\equiv 3 \cdot x$, `2.5x`, `-1.602e-19 y`, `0.5(x,y)`, `2x^2` $\equiv 2(x^2)$, `3(x+2y)`, `3sin(x)`, `10cm` (where `cm = 72/2.540005`).

### Functions & Closures (§6.12)
- `<type> <name>(<params>) { <body> }` — declares a function as a variable with a signature.
- `new <type>(<params>) { <body> }` — anonymous function expression / closure.
- `using <alias> = <type>(<params>);` — signature alias (synonym: `typedef <type> <alias>(<params>);`).
- `<param> = <default>` — default argument; may appear anywhere in parameter list; evaluated in defining scope.
- `f(4, x=3)` — named argument call; keywords matched before positionals. Enclose in parens `(x=3)` for local assignment.
- `<type> keyword <name>` — keyword-only parameter; forces call site to use named syntax `f(name=val)`.
- `...<type>[] <name>` — rest parameter (packing trailing arguments into an array); `f(...arr)` unpacks array.
- Overloading — distinguished by signatures; exact match scores higher than cast match, which scores higher than rest-packing.

### Mathematical Functions (§6.12.4)
- libm `real(real)`: `sin`, `cos`, `tan`, `asin`, `acos`, `atan`, `exp`, `log`, `pow10`, `log10`, `sinh`, `cosh`, `tanh`, `asinh`, `acosh`, `atanh`, `sqrt`, `cbrt`, `fabs`, `expm1`, `log1p`, `identity`.
- Bessel & Special: `Jn(int n, real x)`, `Yn(int n, real x)`, `gamma(real x)`, `erf(real x)`, `erfc(real x)`.
- Binary: `atan2(real y, real x)`, `hypot(real x, real y)`, `fmod(real x, real y)`, `remainder(real x, real y)`.
- Degree conversions: `degrees(real rad)`, `radians(real deg)`, `Degrees(real rad)` (returns angle in $[0, 360)$).
- Degree trig: `Sin`, `Cos`, `Tan`, `aSin`, `aCos`, `aTan`.
- Complex: `sqrt(pair)`, `sin(pair)`, `cos(pair)`, `exp(pair)`, `log(pair)`, `gamma(pair)`.
- Rounding: `floor(real)`, `ceil(real)`, `round(real)` — **ALL RETURN `int`**.
- Saturating rounding: `Floor(real)`, `Ceil(real)`, `Round(real)` — return `int`, saturate to `intMax`/`intMin` instead of overflow.
- Signs & absolute: `sgn(real)` (returns `int` $-1, 0, 1$), `abs(int)` (int), `abs(real)`/`fabs(real)` (real), `abs(pair)` (real length).
- Combinatorics: `factorial(int n)`, `choose(int n, int k)`.
- Random: `srand(int seed)` (seed < 0 uses entropy; default `srand(-1)`), `int rand(int a=0, int b=randMax)`, `real unitrand()` in $[0,1)$, `string randString(int len, string chars="")`.
- Numerical: `real newton(int iterations=100, real f(real), real fprime(real), real x, bool verbose=false)`, `real simpson(real f(real), real a, real b, real acc=realEpsilon, real dxmax=b-a)`, `cputime cputime()`.

### Arrays & Vectorized Operations (§6.13)
- `T[] A;` — empty array (length 0).
- `T[] A = null;` — null array (no length; cannot read, assign, or dereference).
- `new T[n]` — allocates array of length `n`; uninitialized unless elements are arrays (then initialized to empty arrays).
- `{0, 1, 2}` — explicit array literal; `{...A}` splices copy of `A`; `{4 ...A}` requires space before `...`.
- `A[B]` — gather: indexes array `A` with integer array `B`.
- Virtual fields/methods on `T[] A`:
  - `int length` — length of array.
  - `bool cyclic` — when `true`, indices wrap modulo `length`; prevents out-of-bounds errors and auto-resizing.
  - `int[] keys` — evaluates to array of initialized indices in ascending order.
  - `T push(T x)` — appends `x` to end of array and returns `x`.
  - `void append(T[] a)` — appends all elements of `a` onto end.
  - `T pop()` — removes and returns last element.
  - `void insert(int i, ...T[] x)` — inserts elements `x` starting at index `i`.
  - `void delete(int i, int j=i)` — deletes elements in index range $[i, j]$ inclusive, shifts higher elements down.
  - `void delete()` — deletes all elements (clears array).
  - `bool initialized(int n)` — checks if element at index `n` is initialized.
- Built-in array routines:
  - `T[] array(int n, T value, int depth=intMax)` — returns `n` deep copies of `value`.
  - `int[] sequence(int n)` — returns $\{0, 1, \dots, n-1\}$.
  - `int[] sequence(int n, int m, int skip=1)` — returns $\{n, n+\text{skip}, \dots, m\}$.
  - `T[] sequence(T f(int), int n)` — returns $\{f(0), f(1), \dots, f(n-1)\}$.
  - `T[] map(T f(T), T[] a)` — applies `f` to each element.
  - `int find(bool[] a, int n=1)` — index of $n$-th `true` value ($-1$ if absent; negative $n$ searches from end).
  - `int[] findall(bool[] a)` — indices of all `true` values.
  - `int search(T[] a, T key [, bool less(T,T)])` — index $i$ such that $a[i] \le \text{key} < a[i+1]$.
  - `T[] copy(T[] a)` — returns deep copy of array `a`.
  - `T[] concat(...T[][] a)` — concatenates 1D arrays.
  - `T[] sort(T[] a [, bool less(T,T), bool stable=true])` — sorts copy of array.
  - `T[][] sort(T[][] a)` — row-sorts 2D array by column 0, breaking ties with successive columns.
  - `T[][] transpose(T[][] a)` — matrix transpose.
  - `T sum(T[] a)` — sum of elements (for `bool[]`, counts `true` elements).
  - `T min(T[] a)`, `T max(T[] a)` — min/max element (also 2D/3D).
  - `T[] min(T[] a, T[] b)`, `T[] max(T[] a, T[] b)` — elementwise min/max.
  - `pair[] pairs(real[] x, real[] y)` — combines coordinate arrays into pair array.
  - `real[] solve(real[][] a, real[] b, bool warn=true)` — solves $ax = b$ via LU decomposition.
  - `real[] tridiagonal(real[] a, real[] b, real[] c, real[] f)` — periodic tridiagonal solver.
  - Vectorized operations: `a + b`, `a * b`, `a == b` (returns `bool[]`), `a >= 2` (returns `bool[]`).
  - `bool all(bool[] a)` — returns `true` iff every element is `true`.
  - Vectorized conditional: `(a >= 2) ? a : b`.

### Slices (§6.13.1)
- `A[m:n]` — half-open sub-array from index $m$ up to but not including $n$. Defaults: $m=0$, $n=\text{length}$.
- `A[:]` — non-cyclic deep copy of `A`.
- `A[m:n] = new T[] { ... }` — slice assignment; inserts/removes elements to accommodate replacement length.
- `A[m:m] = y` — inserts array `y` before index `m`.
- `A[m:n] = A` — self-slice assignment; implicitly copies `A` first.

### Casts (§6.14)
- Built-in implicit casts: `int` $\to$ `real` $\to$ `pair` $\to$ `path`/`guide`; `path` $\leftrightarrow$ `guide`; `real` $\to$ `pen`; `pair[]` $\to$ `guide[]`/`path[]`; `path`/`guide` $\to$ `path[]`.
- `explicit` — formal parameter qualifier; disables implicit casting into that parameter.
- `(type) expr` — explicit cast: `(int) 2.5`, `(string) 2.5`, `(int[]) a` (truncates reals).
- `bool initialized(T x)` — detects uninitialized variables (e.g. after failed `(int) "abc"` string cast).
- `T operator cast(S x)` — defines user implicit cast from `S` to `T`.
- `T operator ecast(S x)` — defines user explicit cast from `S` to `T`.

### Modules & Imports (§6.15)
- `access moduleName;` — loads module as a structure; members accessed qualified (`moduleName.item`).
- `from moduleName access item1, item2 as alias;` — imports specific members into scope.
- `from moduleName access *;` — imports all public/restricted members.
- `import moduleName;` — equivalent to `access moduleName; unravel moduleName;`.
- `unravel structOrMod;` — unpacks structure or module fields into current scope.
- `include moduleName;` — verbatim textual file inclusion.
- `eval(string s, bool embedded=false)` — evaluates Asymptote code dynamically (`embedded=true` uses current environment).
- `code c = quote { ... }; eval(c, true);` — evaluates quoted code block.
- Templated imports: module header `typedef import(T, S);`; call site: `from mod(T=int, S=string) access Item_T_S as Item, operator ==;`.

### Iterators, Static & Autounravel (§6.16–§6.18)
- `for (var x : iterable)` — desugars to `for (var it=iterable.operator iter(); it.valid(); it.advance()) { var x=it.get(); ... }`.
- `Iterable_int range(int n [, int m, int skip=1])` — $O(1)$-memory integer range iterable.
- `static` — allocates variable in block where enclosing function or structure was defined. Loops allocate fresh frame per iteration. Top-level `static` emits a warning.
- `autounravel` — automatically unravels member when struct is defined, accessed, or imported; implies `static`. Top-level `autounravel` is a compile ERROR.

### Hash Functions (§6.19)
- `int hash()` — member method returning non-negative `int`. Equal objects (`==`) MUST produce equal hash values.
- Built-in hashes: `(3).hash()`, `(3.14).hash()`, `("abc").hash()` (parentheses required on literals).
- `int hash(int[] a)` — standard helper to combine hashed fields into a composite hash.

### Collections (§6.20)
- `collections.genericpair(K,V)`:
  - `Pair_K_V makePair(K k, V v)` — generic pair with fields `.k` and `.v`.
- `collections.iter(T)`:
  - `Iter_T`, `Iterable_T` — iterator structs with fields `T get()`, `void advance()`, `bool valid()`.
  - `(T[]) iterable` — explicit cast from `Iterable_T` to array `T[]`.
- `collections.zip2(K,V)` & `collections.zip(T)`:
  - `zip(Iterable_K a, Iterable_V b [, Pair_K_V keyword default=null])` — iterates over pairs.
  - `zip([T keyword default,] ...Iterable_T[] iterables)` — iterates over multi-element arrays.
- `collections.enumerate(T)`:
  - `Iterable_Pair_int_T enumerate(Iterable_T iterable)` — attaches 0-based counter `.k` to items `.v`.
- `collections.hashmap(K,V)` & `collections.btreemap(K,V)`:
  - `HashMap_K_V([V keyword nullValue, bool keyword isNullValue(V)])` — hash table (requires `hash()`).
  - `BTreeMap_K_V([V keyword nullValue, bool keyword isNullValue(V)])` — balanced search tree (requires `<`).
  - Methods: `int size()`, `bool empty()`, `bool contains(K key)`, `V operator[](K key)`, `void operator[=](K key, V value)` (assigning `nullValue` deletes key), `void delete(K key)`, `Iter_K operator iter()`, `Iterable_K_V pairs()`, `K[] keys()`, `void add(Iterable_K_V)`.
- `collections.hashset(T)` & `collections.btree(T)`:
  - `HashSet_T([T nullT, bool equiv(T,T)=operator ==, bool isNullT(T)])` — hash set.
  - `BTreeSet_T([T nullT, bool isNullT(T)])` — ordered set.
  - Methods: `int size()`, `bool empty()`, `bool contains(T item)`, `bool add(T item)`, `void add(Iterable_T)`, `bool delete(T item)`, `void delete(Iterable_T)`, `Iter_T operator iter()`, `Set_T newEmpty()`, `T get(T item)`, `T push(T item)`, `T extract(T item)`.
  - Set operators: `+` (union), `-` (difference), `&` (intersection), `^` (symmetric difference), `<=`, `>=`, `==`, `!=`.
- `collections.sortedset(T)`:
  - `SortedSet_T` — extends `Set_T` with: `T min()`, `T max()`, `T popMin()`, `T popMax()`, `T after(T item)`, `T atOrAfter(T item)`, `T before(T item)`, `T atOrBefore(T item)`.
- `collections.queue(T)`:
  - `Queue_T makeQueue(T[] initialData)` — FIFO queue (use `makeQueue(new T[])` for empty).
  - Methods: `void push(T item)`, `T pop()`, `T peek()`, `int size()`, `Iter_T operator iter()`.
- `collections.wraparray(T)`:
  - `Array_T wrap(T[] data [, int hashfn(T)])` — wraps raw array to provide scalar `==` and `.hash()` for map/set compatibility. Methods `length()`, `cyclic()`, `keys()` require parentheses.

## Options/parameters

| Function / Constructor | Parameter | Default | Effect |
| :--- | :--- | :--- | :--- |
| `sort` | `bool less(T i, T j)` | `operator <` | Custom comparator; returns `true` if `i` precedes `j` |
| `sort` | `bool stable` | `true` | Preserves original relative order when elements compare equal |
| `array` | `int depth` | `intMax` | Limits recursion levels when deep-copying nested arrays |
| `find` | `int n` | `1` | Finds $n$-th true element; negative $n$ searches backwards from end |
| `solve` | `bool warn` | `true` | If `false`, suppresses warning on singular matrix and returns empty array |
| `fft` | `int sign` | `1` | FFT transform sign ($1$ or $-1$); unnormalized |
| `sequence` / `range` | `int skip` | `1` | Step size between generated integers |
| `zip2` | `Pair_K_V default` | `null` | Keyword-only; pads shorter iterable to match longer length |
| `zip` | `T default` | *none* | Keyword-only; pads iterables to length of longest iterable |
| `HashMap_K_V` | `V nullValue` | *none* | Keyword-only; returned on missing key instead of throwing runtime error |
| `HashMap_K_V` | `bool isNullValue(V)` | `v == nullValue` | Keyword-only; assigning a value matching this predicate deletes key |
| `HashSet_T` | `T nullT` | *none* | Sentinel value returned when element is not found |
| `HashSet_T` | `bool equiv(T, T)` | `operator ==` | Equivalence relation for set members |
| `BTreeSet_T` | `bool lessThan(T, T)` | `operator <` | Strict weak ordering comparator (module `btreegeneral`) |
| `rand` | `int a`, `int b` | `0`, `randMax` | Closed interval $[a, b]$ for uniform random integer |
| `randString` | `string chars` | *alphanumeric* | Character pool; char frequencies weight selection probability |
| `newton` | `int iterations` | `100` | Maximum Newton-Raphson iterations before returning `realMax` |
| `newton` | `bool verbose` | `false` | Prints diagnostic output for each iteration |
| `simpson` | `real acc` | `realEpsilon` | Desired relative accuracy for adaptive Simpson integration |
| `eval` | `bool embedded` | `false` | If `true`, evaluates code in current top-level namespace |
| `list` | `bool imports` | `false` | If `true`, includes imported variables and functions in listing |

## Traps

1. **Integer division `/` vs `#`**: `/` always converts both integers to `real` and returns `real` (`7/2 == 3.5`). Integer division is `#` (`7#2 == 3`).
2. **Modulo `%` sign follows divisor**: `(-7) % 3` is `2`, because Asymptote preserves $q \cdot (p \# q) + p \% q == p$ with divisor sign rule.
3. **`^` is power, NOT bitwise xor**: `2^3` evaluates to `8.0` (synonym: `2**3`). For bitwise integer operations, use functions `AND`, `OR`, `XOR`, `NOT`.
4. **Postfix `++`/`--` do not exist**: `i++` and `i--` cause syntax errors (ambiguity with path-join `--`). Always use prefix `++i` or substitute `(++i-1)`.
5. **Pair `*` is complex multiplication**: `(x1, y1) * (x2, y2)` computes $(x_1 x_2 - y_1 y_2, x_1 y_2 + y_1 x_2)$. For dot product, use `dot(p1, p2)` or `p1.x*p2.x + p1.y*p2.y`.
6. **Vectorized array comparisons return `bool[]`**: `a == b`, `a < b`, `a >= 2` evaluate to boolean arrays, NOT a scalar `bool`. Writing `if (a == b)` is a compile type-error. Use `if (all(a == b))`.
7. **Raw arrays cannot be compared or hashed**: Because `T[]` operator `==` returns `bool[]`, raw arrays cannot be elements of `Pair_K_V` or keys in `HashMap`. Use `collections.wraparray` to wrap them in `Array_T`.
8. **Shallow array assignment**: `real[] B = A;` copies only the reference pointer. Mutating `B[0]` mutates `A[0]`. Use `real[] B = copy(A);` or `A[:]` for deep copies.
9. **Array write resizes; read past end errors**: Assigning `A[10] = x` on a length-3 array silently expands length to 11 (unassigned intermediate indices are uninitialized). Reading `A[10]` out-of-bounds throws a runtime error.
10. **Space required before array splice `...`**: `{4 ...A}` requires whitespace between literal `4` and `...`. `4...A` is tokenized as `4.0 .. A`, causing a syntax error.
11. **Slice assignment changes array length**: `A[1:3] = new int[] {10, 20, 30, 40};` replaces 2 elements with 4, growing the array.
12. **Non-cyclic negative slice index is illegal**: `A[-1:3]` is an error on normal arrays. On cyclic arrays (`A.cyclic = true;`), negative indices wrap modulo length.
13. **Cyclic bridging slices assignable ONLY with exact matching length**: If a cyclic slice crosses array boundaries (e.g. `A[8:12]` on length 10), assigning an array of different length throws an error.
14. **Structures have REFERENCE semantics**: `T b = a;` makes `b` and `a` point to the identical instance. `==` checks reference identity via `alias(a, b)`.
15. **Recursive struct field default initializer**: Within a struct definition, fields of the struct's own type default to `null` to prevent infinite recursion (e.g. `struct tree { tree left, right; }`). Outside the definition, `T var;` defaults to `new T`.
16. **`operator init` void vs non-void**: Constructors MUST return `void` (`void operator init(...)`). A non-void `T operator init()` defines the default initializer for uninitialized declarations of type `T`.
17. **Cast operator recursion**: `real operator cast(int x) { return x * 100; }` causes infinite recursion because `x * 100` implicitly invokes the cast. Rebind via an ordinary function: `real conv(int x) { return x * 100; } real operator cast(int x) = conv;`.
18. **Function call named arguments vs local assignments**: `f(x=3)` assigns named parameter `x` to `3`. It DOES NOT assign to a local variable `x`. To assign locally within a call, wrap in parentheses: `f((x=3))`.
19. **Named arguments bind before positionals**: In `f(int x, int y)`, the call `f(4, x=3)` matches `x=3` first, then matches positional `4` to `y`, returning `10(3) + 4 = 34`.
20. **Rest arguments cannot unpack into leading formals**: `f(...arr)` is illegal if `f` expects leading non-rest parameters that remain unmatched.
21. **Function reassignment vs overloading**: Reassigning `f = new void() { ... };` dynamically updates calls in existing callers; declaring a new overload `void f() { ... }` leaves existing callers bound to the earlier version.
22. **Literal hash requires parentheses**: `(3).hash()` and `("str").hash()` are valid; `3.hash()` or `"str".hash()` are syntax errors.
23. **Hash of reals vs ints**: `(3).hash() != (3.0).hash()`. Near-equal reals hash to completely uncorrelated integers.
24. **Map missing key throws error**: `m[k]` throws an error unless `nullValue` was supplied to the map constructor. Setting `m[k] = nullValue` deletes key `k` from the map.
25. **Set operators `+=`/`-=` do NOT mutate in place**: `+`, `-`, `&`, `^` construct a new set using `.newEmpty()`. `s += item` creates a whole new set. Use `.add(item)` and `.delete(item)` for in-place mutation.
26. **Top-level `autounravel` is a hard error**: `autounravel` is only permitted inside structures or functions (implies `static`). Top-level `static` emits a warning. Types cannot be autounraveled.
27. **Qualified name shadowing**: If a non-function variable shares a name with a struct type in scope (`pair A; struct A { static int x; }`), `A.x` resolves to the variable, never the type.
28. **Round / Ceil / Floor return `int`**: Standard `round(x)`, `floor(x)`, `ceil(x)` return integer types. For large values that might overflow 32-bit signed ints, use saturating `Round(x)`, `Floor(x)`, `Ceil(x)`.

## Compile-gated examples

### Example 1: Structures, Reference Semantics, and Containment Inheritance
```asy
// Demonstrates: struct, operator init ctor, reference alias, containment inheritance, operator cast
struct Shape {
  real weight;
  void operator init(real weight=1.0) {
    this.weight = weight;
  }
  void describe() {
    write("Generic shape with weight " + (string)weight);
  }
}

struct Circle {
  Shape shape;
  real radius;
  void operator init(real radius, real weight=1.0) {
    shape.operator init(weight);
    this.radius = radius;
  }
  void describe() {
    write("Circle radius " + (string)radius + ", weight " + (string)shape.weight);
  }
  shape.describe = describe;                       // virtual method dispatch
}
Shape operator cast(Circle c) { return c.shape; }  // implicit upcast to parent

Circle c1 = Circle(5.0, 2.5);
Circle c2 = c1;                                    // reference alias
c2.radius = 7.0;
assert(c1.radius == 7.0);                          // c1 reflects modification to c2
assert(alias(c1, c2));                             // identical instance

Shape s = c1;                                      // implicit upcast via operator cast
assert(s.weight == 2.5);
```

### Example 2: Arrays, Slices, Splicing, and Vectorized Filtering
```asy
// Demonstrates: array literals, splicing, cyclic indexing, slices, vectorized operations
int[] base = {10, 20, 30};
int[] spliced = {1, 2, ...base};                   // space required before ...
assert(spliced.length == 5);
assert(spliced[2] == 10);

int[] sub = spliced[1:4];                          // half-open [1, 4) -> {2, 10, 20}
assert(sub.length == 3 && sub[0] == 2 && sub[2] == 20);

sub[0:2] = new int[] {99};                         // slice replacement modifies length
assert(sub.length == 2 && sub[0] == 99 && sub[1] == 20);

// Cyclic indexing
int[] cyc = {0, 1, 2, 3, 4};
cyc.cyclic = true;
int[] wrapped = cyc[-2:3];                         // wraps indices -> {3, 4, 0, 1, 2}
assert(wrapped.length == 5 && wrapped[0] == 3 && wrapped[4] == 2);

// Vectorized operations
real[] r = {1.0, 4.0, 9.0};
bool[] mask = (r >= 4.0);                          // elementwise comparison -> {false, true, true}
assert(sum(mask) == 2);                            // sum(bool[]) counts true entries
assert(!all(mask));
```

### Example 3: First-Class Closures, Named/Rest Arguments, and Implicit Scaling
```asy
// Demonstrates: using signature alias, anonymous closure, rest packing, named args, implicit scaling
using RealTransform = real(real);

RealTransform makePoly(real a, real b, real c) {
  return new real(real x) {
    return a*x^2 + b*x + c;                        // ^ power operator
  };
}

RealTransform poly = makePoly(2, 3, 1);
assert(poly(2) == 15.0);                           // 2(4) + 3(2) + 1 = 15

// Named arguments and keyword evaluation
real evalLinear(real x, real slope=1.0, real intercept=0.0) {
  return slope*x + intercept;
}
assert(evalLinear(5, intercept=10) == 15.0);

// Rest arguments and implicit scaling (numeric literal before expression)
real sumTripled(...real[] terms) {
  real total = 0;
  for (int i = 0; i < terms.length; ++i) {
    total += terms[i];
  }
  return 3total;                                   // implicit scaling: 3 * total
}
assert(sumTripled(1.0, 2.0, 3.0) == 18.0);
```

### Example 4: Collections, HashMaps, and Range Iteration
```asy
// Demonstrates: templated module access, HashMap, range iterators, key loops
from collections.hashmap(K=string, V=int) access
  HashMap_K_V as HashMap_string_int;

HashMap_string_int scores = HashMap_string_int(nullValue=-1);
scores["Alice"] = 95;
scores["Bob"] = 82;

assert(scores.contains("Alice"));
assert(scores["Alice"] == 95);
assert(scores["Charlie"] == -1);                    // returns nullValue, does not throw

int total = 0;
for (string name : scores) {                       // iterates over keys
  total += scores[name];
}
assert(total == 177);

scores["Bob"] = -1;                                // assigning nullValue deletes key
assert(!scores.contains("Bob"));
assert(scores.size() == 1);

// O(1)-memory integer range loop
int stepSum = 0;
for (int i : range(1, 10, 2)) {                    // 1, 3, 5, 7, 9
  stepSum += i;
}
assert(stepSum == 25);
```
