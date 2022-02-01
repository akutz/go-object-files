# Go object files

C developers will be familiar with compiling C source files (`.c`) into object files (`.o`) before linking them into their final form. Did you know you can do the same thing with the Go compiler as well? This project is a quick demonstration of how to turn `main.go` into `main.o`!

## The files

This project contains the following Go sources:

* **`main.go`**: a simple Go program for summing two integers
* **`sum.go`**: the function `SumInt` used by `main.go`


## The program

Of course it is possible to build this project with `go build`:

```bash
go build -o sum
```

and then use the resulting binary executable to sum two integers:

```bash
$ ./sum 2 3
5
```


## Object files

However, what does `go build` actually do? Well, one way to figure this out is by using `go build -x` to print the commands that `go build` executes. One such step is to produce object files for the Go sources:

```bash
go tool compile sum.go
```

The above command will produce the object file `sum.o`:

```bash
$ ls *.o
sum.o
```

But what happens when we try to create the object file for `main.go`?

```bash
$ go tool compile main.go
main.go:37:14: undefined: SumInt
```

In fact, this is the same error we would receive if we tried to use `go run` to run the program with just `main.go`:

```bash
$ go run main.go 2 3
# command-line-arguments
./main.go:37:14: undefined: SumInt
```

In order to create the object file or compile the program, Go has to create a dependency graph for all the files that depend on one another. The symbol used by `main.go`, `SumInt`, is in the file `sum.go`. If we were to compile `main.o` using _both_ `main.go` and `sum.go`, then it would work.

First let's clean up the previous object file we created:

```bash
rm -f sum.o
```

Now let's compile `main.go` into an object file:

```bash
go tool compile main.go sum.go
```

Verify the result:

```bash
$ ls *.o
main.o
```

And `main.o` is the only object file created.

## Conclusion

This project does not dig into a fraction of what is possible with `go tool compile` or look at `go tool link` at all. However, most developers will never need to manually execute these commands. That's the beauty of `go build`. Still, beauty is not only skin-deep, it's what is on the inside that counts, and understanding how `go build` works is a great goal to have!
