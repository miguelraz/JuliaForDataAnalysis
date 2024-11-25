# Julia for Data Analysis

## Bogumił Kamiński, Daniel Kaszyński

# Capítulo 2

# Problemas

### Ejercicio 1

Considere el siguiente código:
```
x = [1, 2]
y = x
y[1] = 10
```
¿Cuál es el valor de `x[1]` y por qué?

<details>
<summary>Solución</summary>

`x[1]` will be `10` because `y = x` is not copying data but it binds
the same value both to variable `x` and `y`.
`x[1]` será `10` porque` y = x` no está copiando datos pero se liga
al mismo valor tanto a la variable `x` y` y`.

</details>


### Ejercicio 2

How can you type `⚡ = 1`. Check if this operation succeeds and what is its result.

<details>
<summary>Solution</summary>

En modo de ayuda (activado por `?`) haz copy-paste `⚡` para obtener:
```
help?> ⚡
"⚡" puedes escribir \:zap:<tab>
```
Después de la operación `⚡ = 1` se define una nueva variable ` ⚡` y está asignado
valor `1`.

</details>

### Ejercicio 3

¿Cuál será el valor de la variable `x` después de ejecutar el siguiente código y por qué?
```
x = 0.0
for i in 1:7_000_000
    global x += 1/7
end
x /= 1_000_000
```

<details>
<summary>Solución</summary>

`X` tendrá valor` 0.99999999999242748`. Este valor está por debajo de `1.0` porque
la representación de `1/7` usando el tipo` Float64` es menor que el número racional 1/7,
y el error se acumula cuando hacemos suma varias veces.

*Extra*: puede verificar que de hecho esa representación de `Float64` es un poco menos
que el racional 1/7 aumentando la precisión de los cálculos utilizando la función `big`:
```
julia> big(1/7) # convert Float64 to high-precision float
0.142857142857142849212692681248881854116916656494140625

julia> 1/big(7) # construct high-precision float directly
0.1428571428571428571428571428571428571428571428571428571428571428571428571428568
```
As you can see there is a difference at 17th place after decimal dot where we
have `4` vs `5`.
Como puede ver, hay una diferencia en la 17a cifra después del punto decimal donde nosotros
tenemos `4` vs` 5`.

</details>

### Ejercicio 4

Expresa el tipo `Matrix{Bool}` usando el tipo de `Array`.

<details>
<summary>Solución</summary>

Es `Array {Bool, 2}`. Se puede obtener esta información del REPL

```
julia> Matrix{Bool}
Matrix{Bool} (alias for Array{Bool, 2})
```

</details>

### Ejercicio 5

Let `x` be a vector. Write code that prints an error if `x` is empty
(has zero elements)
Sea `x` un vector. Escribir un código que imprime un error si `x` está vacío 
(tiene cero elementos)

<details>
<summary>Solución</summary>

Puedes hacerlo así:
```
length(x) == 0 && println("x is vacío")
```

*Extra*: Por lo general, en tal caso uno usaría la función 'isempty` y lanzaría
una excepción en lugar de solo imprimir información (aquí supongo que `x` era
usado como un argumento a la función):
```
isempty(x) && throw(ArgumentError("x no puede ser vacío"))
```

</details>

### Exercise 6

Escriba una función llamada `exec` que toma dos valores `x` y `y` y una función
que acepte dos argumentos, llámala `op` y que devuelva `op(x, y)`. Haz que  `+` sea
el valor predeterminado de `OP`.

<details>
<summary>Solución</summary>

Aquí hay dos formas de definir la función `exec`:
```
exec1(x, y, op=+) = op(x, y)
exec2(x, y; op=+) = op(x, y)
```
El primero de ellos usa argumentos posicionales, y el segundo es un argumento de palabra clave (*keyword argument*)
Aquí hay una diferencia en cómo se llaman:
```
julia> exec1(2, 3, *)
6

julia> exec2(2, 3; op=*)
6
```

</details>

### Exercise 7

Escriba una función que calcule una suma de valores absolutos de valores almacenados en una colección pasada a ella.

<details>
<summary>Solución</summary>

Tal función se puede escribir como:
```
sumabs(x) = sum(abs, x)
```

</details>

### Ejercicio 8

Escriba una función que cambie primero y el último elemento en una matriz en su lugar.

<details>
<summary>Solución</summary>

Esto se puede escribir, por ejemplo, como:
```
function intercambia!(x)
    f = x[1]
    x[1] = x[end]
    x[end] = f
    return x
end
```

* Extra* Una forma más avanzada de escribir esta función sería:
```
function intercambia!(x)
    if length(x) > 1
        x[begin], x[end] = x[end], x[begin]
    end
    return x
end
```
Note the differences in the code:
* we use `begin` instead of `1` to get the first element. This is a safer
  practice since some collections in Julia do not use 1-based indexing (in
  practice you are not likely to see them, so this comment is most relevant
  for package developers)
* if there are `0` or `1` element in the collection the function does not do
  anything (depending on the context we might want to throw an error instead)
* in `x[begin], x[end] = x[end], x[begin]` we perform two assignments at the
  same time to avoid having to use a temporary variable `f` (this operation
  is technically called tuple destructuring; we discuss it in later chapters of
  the book)
Tenga en cuenta las diferencias en el código:
* Usamos `begin` en lugar de '1` para obtener el primer elemento. Esto es mejor
 práctica ya que algunas colecciones en Julia no utilizan la indexación basada en 1 (en
 la práctica que no es probable que los vea, por lo que este comentario es más relevante
 para desarrolladores de paquetes)
* Si hay elementos `0` o` 1` en la colección, la función no hace
  nada (dependiendo del contexto, es posible que deseemos lanzar un error)
* en `x[begin], x[end] = x[end], x[begin]` realizamos dos tareas
 al mismo tiempo para evitar tener que usar una variable temporal `f` (esta operación
 se llama técnicamente la desestructuración de tuple o bien *tuple destructuring*; Lo discutiremos en capítulos posteriores de
 del libro)

</details>

### Exercise 9

Write a loop in global scope that calculates the sum of cubes of numbers from
`1` to `10^6`. Next use the `sum` function to perform the same computation.
What is the difference in timing of these operations?

<details>
<summary>Solution</summary>

We used `@time` macro in chapter 1.

Version in global scope:
```
julia> s = 0
0

julia> @time for i in 1:10^6
           global s += i^3
       end
  0.076299 seconds (2.00 M allocations: 30.517 MiB, 10.47% gc time)
```

Version with a function using a `sum` function:
```
julia> sum3(n) = sum(x -> x^3, 1:n)
sum3 (generic function with 1 method)

julia> @time sum3(10^6)
  0.000012 seconds
-8222430735553051648
```

Version with `sum` function in global scope:
```
julia> @time sum(x -> x^3, 1:10^6)
  0.027436 seconds (48.61 k allocations: 2.558 MiB, 99.75% compilation time)
-8222430735553051648

julia> @time sum(x -> x^3, 1:10^6)
  0.025744 seconds (48.61 k allocations: 2.557 MiB, 99.76% compilation time)
-8222430735553051648
```

As you can see using a loop in global scope is inefficient. It leads to
many allocations and slow execution.

Using a `sum3` function leads to fastest execution. You might ask why using
`sum(x -> x^3, 1:10^6)` in global scope is slower. The reason is that an
anonymous function `x -> x^3` is defined anew each time this operation is called
which forces compilation of the `sum` function (but it is still faster than
the loop in global scope).

For a reference check the function with a loop inside it:
```
julia> function sum3loop(n)
           s = 0
           for i in 1:n
               s += i^3
           end
           return s
       end
sum3loop (generic function with 1 method)

julia> @time sum3loop(10^6)
  0.001378 seconds
-8222430735553051648
```
This is also much faster than a loop in global scope.

</details>

### Exercise 10

Explain the value of the result of summation obtained in exercise 9.

<details>
<summary>Solution</summary>

In exercise 9 we note that the result is `-8222430735553051648` which is a
negative value, although we are adding cubes of positive values. The
reason of the problem is that operations on integers overflow. If you
are working with numbers larger that can be stored in `Int` type, which is:
```
julia> typemax(Int)
9223372036854775807
```
use `big` numbers that we discussed in *Exercise 3*:
```
julia> @time sum(x -> big(x)^3, 1:10^6)
  0.833234 seconds (11.05 M allocations: 236.113 MiB, 23.77% gc time, 2.63% compilation time)
250000500000250000000000
```
Now we get a correct result, at the cost of slower computation.

</details>
