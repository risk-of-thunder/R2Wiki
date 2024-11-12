# Thunderkit - Using the DOTS framework in Modding

The Data Oriented Technology Stack (otherwise known as the DOTS framework) is a development philososphy and technology tools designed for creating fast performant code by utilizing Data oriented programming. which allows for highly efficient and performant code.

The DOTS framework is built on three key pillars, of which 2 are accessible and one is unknown if it can be used in modding.

| Framework Part | Description | Availability |
|--|--|--|
| ESC - Entity Component System | The ECS is a framework that works on a purely data driven design, where it has Entities that populate scenes, but lack data and behaviour, components which hold the data in an Entity, and Systems, which transform the data of components in a deterministic way | Unknown |
| Burst Compiler | Burst is a compiler that can be used alongside the Job system to create code that improves performance by translating your IL/.Net bytercode into native CPU code using the LLVM compiler. | Available, known method via Unity and ThunderKit |
| C# Jobs | Allows you to create multithreaded code for performance intensive tasks, such as multiple transform modifications, complex mathematic operations and more. Has built in guard rails for development of safe multithreaded code | Available |

This guide will not cover how to use the Burst Compiler or how to Write C# jobs, it'll only cover how to get access to the framework on a ThunderKit context and how to compile your mod