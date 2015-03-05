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
- Type wrapper with two operations
- return `'T -> M<'T>`
- bind `M<'T> * ('T -> M<'U>) -> M<'U>`

---

### C# has IEnumerable<'T> 
- generic wrapper type to access sequences of another type
- return implemented by `yield`
- bind implemented by `SelectMany`


***

	open System

    type MaybeBuilder() =
 	 
	    member x.Return(parameter) = Some parameter
	    
	    member x.Bind(parameter, nextExpression) = 
	        Console.WriteLine("bind: " + (string)parameter) 
	        |> ignore
	 
	        match parameter with
	        | Some x -> nextExpression x
	        | None -> None


