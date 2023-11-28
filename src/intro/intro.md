# Introduction

Welcome to The DBL Programming Language, your introductory guide to understanding and mastering DBL. DBL, as a progression of DIBOL, brings together a rich history of business-oriented design with modern software development practices. This programming language allows you to create robust, efficient software that retains a high degree of portability and backward compatibility.

DIBOL, which stands for Digital's Business Oriented Language, was a general-purpose, procedural, and imperative programming language initially developed and marketed by Digital Equipment Corporation (DEC) in 1970. Designed primarily for use in Management Information Systems (MIS) software development, DIBOL was deployed across a range of DEC systems, from the PDP-8 running COS-300, to the PDP-11 with COS-350, RSX-11, RT-11, and RSTS/E, and eventually on VMS systems with DIBOL-32. DIBOL development by DEC effectively ceased after 1993.

DBL, developed by DISC, emerged as a successor to DIBOL. After an agreement between DEC and DISC, DBL replaced DIBOL on OpenVMS, Digital UNIX, and SCO Unix. It has since been further developed and supported by Synergex, adding support for Object Oriented and Functional programming styles. 

DBL strikes a balance between the business functionality offered by DIBOL and the incorporation of modern programming paradigms. By integrating procedural syntax, reminiscent of its DIBOL roots, with contemporary features like object-oriented programming, DBL allows programmers to manage their businesses complex business logic. Moreover, DBL's extensive support for various file access methods and data types makes it an excellent choice for data-intensive applications.

With DBL, you gain control over intricate details without the complexity typically associated with such precision, allowing you to focus on building reliable, cross-platform business software. This book will guide you through the versatility of DBL and how it carries forward the legacy of DIBOL into the realm of modern programming.

## Naming
In your journey through DBL, you'll encounter several names for this programming language and its associated tools. This diversity of terminology is an unfortunate side effect of its longevity. Originating from DIBOL, DBL has grown and adapted to accommodate various runtimes and environments.

One crucial aspect of DBL is its standalone runtime, often referred to as Traditional Synergy or Traditional DBL. This standalone variant serves as a comprehensive environment for executing DBL applications independently. The robustness and platform independence offered by Traditional DBL allow for efficient and reliable operation of DBL software written 40+ years ago.

Further enhancing DBL's versatility is its ability to compile natively to .NET. This integration, frequently referred to as Synergy.NET or DBL running on .NET, permits seamless interaction with .NET and its wide ecosystem. This adaptability ensures that DBL applications can leverage the power and scope of .NET's extensive libraries and tools.

Whether you're using DBL in its traditional form or harnessing the power of .NET, the essence of DBL remains consistent - offering robust features and superior reliability for diverse programming tasks. The different names of DBL merely reflect its multifaceted nature and ability to operate in varied environments.

## Who This Book Is For

This book assumes that you’ve written code in another programming language but doesn’t make any assumptions about which one. We’ve tried to make the material broadly accessible to those from a wide variety of programming backgrounds. While we do not delve into the fundamentals of what programming is or the general philosophy behind it, we place significant emphasis on explaining the intricacies of real world DBL. This includes detailed discussions on when and why to use various features of the language, as well as shedding light on common patterns and practices prevalent in the DBL community. However, if you are entirely new to the world of programming, you might find more value in a resource specifically tailored as an introduction to programming concepts. This book aims to bridge your existing programming knowledge to the specificities and nuances of DBL.

## How to Use This Book

In general, this book assumes that you’re reading it in sequence from front to back. Later chapters build on concepts in earlier chapters, and earlier chapters might not delve into details on a particular topic but will revisit the topic in a later chapter. Some chapters can be skipped if you're codebase doesn't use the features covered in that chapter. Examples of this are the ui and io chapters. A counter example would be chapter that covers newer .NET specific features. Those chapters should still be read even if your codebase doesn't currently use those features.

## Special Thanks

This book is inspired by, loosely structured as and sometimes copied from the [Rust Book](https://github.com/rust-lang/book)