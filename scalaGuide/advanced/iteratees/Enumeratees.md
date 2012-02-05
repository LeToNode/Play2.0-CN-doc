# Handling data streams reactively

## The realm of Enumeratees

`Enumeratee` is a very important component of the iteratees api. It provides the means for adapting and tranforming streams of data.
An `Enumeratee` that might sound familiar is the `Enumeratee.map`. Let's say we have an Iteratee[A,E] 