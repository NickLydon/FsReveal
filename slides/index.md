- title : F# computation expressions
- description : Introduction to F# computation expressions
- author : Nicholas Lydon
- theme : moon
- transition : default

***

### Computation Expressions
### in F#

***

### Monads

- Idea from Haskell
- Workaround for inability to manipulate state
- Chains computations (hence expression builder)

---

### Concrete definition
- Type wrapper with two operations
- return (or unit) : `'T -> M<'T>`
- bind: `M<'T> * ('T -> M<'U>) -> M<'U>`

---

### C# has IEnumerable<'T>

- generic wrapper type to access sequences of another type
- return implemented by `yield`
- bind implemented by `SelectMany`

---

### And Task<'T>

- return implemented by `Task.FromResult` or `Task.Run`
- bind implemented by `await`

---

### Also Lazy<'T>, Nullable<'T>, etc. etc.

***

### Some examples in F#

---

    type IdentityBuilder() =
        member this.Return(parameter) = 
            parameter
        member this.Bind(parameter, nextExpression) = 
            nextExpression parameter            

    let identity = IdentityBuilder()
    let x = identity {
        let! a = 1
        let! b = 2
        return a + b }

This example does absolutely nothing!

---

    type MaybeBuilder() =
	    member this.Return(parameter) = Some parameter
	    member this.Bind(parameter, nextExpression) = 
	        match parameter with
	        | Some x -> nextExpression x
	        | None -> None

    let maybe = MaybeBuilder()

This example will continue chaining expressions until it finishes successfully, or encounters a None value

---

    let readIntFromConsole() =
        let input = System.Console.ReadLine()
        let canParse,parsed = System.Int32.TryParse input
        if canParse then Some parsed else None

    let x = maybe {
        let! a = readIntFromConsole()
        let! b = readIntFromConsole()
        return a + b }

    match x with
    | Some x -> printfn "Total is %i" x
    | None -> printfn "please enter numbers only"

This computation won't continue if a is not an integer

*** 

    let x =
        let rec loop acc = maybe {
            let! input = readIntFromConsole()
            if input = 0 
            then return acc 
            else return! loop (acc + input) }
        loop 0
    
This shows that computations can be nested recursively

    type MaybeBuilder() =
        member this.ReturnFrom(parameter) = parameter

`ReturnFrom` needs to be specified so that results can be returned without wrapping them in the monad type
