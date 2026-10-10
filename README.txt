This project is now archived.

This version of the project still builds, and I am not unhappy with the final product.

However, the implementations of some of the things I wished to do with it require implementing a painful refactor - that of the new command definitions.
I got 99% of the way there, but no longer wish to continue - certain components would require rewriting, including the nebulous and unfun input.cpp.
Unfortunately, that is not worth my time.

Therefore, I'll be putting this project on hiatus while I explore other systems and ideas.

As a rough documentation for how it works:

    -> Application initialises state
    -> Application calls runFrame()
        -> Within runFrame(), SDL event polling and ImGui event polling occurs.
        -> Commands are constructed according to bindings, against their Command Definition (in Command Repo.cpp)
        -> Commands are piped to the command list, which is then executed.
        -> Underlying state is mutated, and whole thing is rendered to end the loop.
        -> When runFrame returns false, the application cleans itself up and we leave the loop.

Around the code, you may see Command::dfn.
These contain a much more abstract representation of what it means to do things within the application.

The refactor is based around using these in place of the current Command_Definitions to allow for a much more mutable and scriptable application.
Intention was to allow for arbitrary calling of low-level Command::dfn s to allow for application to run simple scripts from files, such that one could construct drawings mathematically, ahead of time, etc.

It's very close to being there, but realistically the implementation is menial and uninteresting. As a todo for it, in case I return:
    -> Check executeCommands for implementation details
    -> Wipe input.h and input.cpp, these require almost a full rewrite.

There are some existing bugs and issues that also should be addressed:
    -> Currently (Action.h) we are storing actions (lists of pixel locations) on the stack.  This is incorrect. 
        -> The problem with this is that y doing it, when the vector resizes (further actions) it forces memory reallocations.  This means that if you have arbitrarily large actions (e.g a screen fill)
            ... it takes a while to process, because it moves the memory around.  This can cause lag in large action banks.
            
    -> Additionally to last point, there is a lack of compression in almost all elements of the code.  This is inefficient.  Reducing actions / canvas's down to their minimum number of mathematical operations to construct them
        ... would save time and memory.  For instance, a filled screen with a single dot in the centre could reasonably be represented by some set of operations that is cheaper to store in memory.

    -> Object handling was very much "hacked in", in the sense that I was largely just messing with it before I decided to stop working on it (to implement this refactor).
        -> This means that there are some weird behaviours - e.g editing a selected object can be very buggy as the means of which I track what becomes the new object does not take into account existing object size.
        -> Again, not necessarily a complicated fix but I really would just rewrite this entire section of logic.

    -> Finally, the CLI is by design inherently unsafe.  Just worth pointing out.

I was proud of what I accomplished with this project, and am grateful for the cool stuff that I learnt along the way.  I think that it has taught me a great deal about how to build an application
    ...in terms of having to deal with derived arbitrary complexity.  It also forced me to relearn C++ in a way in which I am certain I would otherwise not have pursued - having now dealt with the derived state nightmare that is OOP
    ...I cannot see myself building anything using an OOP approach ever again.

Cheers,

Luke
    