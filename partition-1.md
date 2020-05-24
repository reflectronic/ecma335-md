# Partition I: Concepts and Architecture

## Foreword

This sixth edition cancels and replaces the fifth edition which has been technically revised.
This Standard is fully aligned with ISO/IEC 23271:2012.

The following features have been added, extended or clarified in the Standard:

- The presentation of the rules for assignment compatibility (§I.8.7, §III.1.8.1.2.3) has been extensively revised to a more precise and clearer relation-based format.
- The presentation of the verification rules for many IL instructions has been revised to be more precise and clearer by building upon the revisions to the presentation of assignment compatibility.
- The presentation of delegate signature compatibility has been revised along the same lines as assignment compatibility.
- The verification rules for the IL newobj instruction have been extended to cover general delegate creation.
- The dispatch rules for variance (§II.12.2) have been extended to define resolutions for the ambiguities that can arise.
- Type forwarders have been added to support the relocation of types between libraries (§II.6.8)

The following changes of behavior have been made to the Standard:

- The semantics of variance has been redefined making it a core feature of the CLI. In the previous edition of the Standard variance could be ignored by languages not wishing to support it (§I.1.8); as exact type matches always took precedence over matches-by-variance. In this edition the dispatch rules for interfaces (§II.12.2) also allow a match-by-variance to take precedence over an exact match, so all language implementation targeting the CLI must be aware of the behavior even if it is not supported in the language (§I.1.8).
- Additional requirements on ilsasm to metadata conversion. The left-to-right order of interfaces listed in a type header (§II.10.2) must now be preserved as a top-to-bottom order in the InterfaceImpl table (§II.22.23); and the top-to-bottom of method definitions (§II.10.2, §II.25) must now be preserved as a top-to-bottom order in the MethodDef table (§II.22.26). Both these additional requirements are required to support the revised variance semantics.
- System.Math and System.Double have been modified to better conform to IEEE (see §Partition IV and IEC 60559:1989)

The following types have been added to the Standard or have been significantly updated (* represents an update).

| **Type**                                                     | **Library**            |
| ------------------------------------------------------------ | ---------------------- |
| System.Action                                                | BCL                    |
| System.Action\`1<-T>* ... System.Action\`8<-T1..-T8>         | BCL                    |
| System.Comparison`1<-T>*                                     | BCL                    |
| System.Converter`2<-T,+U>*                                   | BCL                    |
| System.IComparable`1<-T>*                                    | BCL                    |
| System.Predicate`1<-T>*                                      | BCL                    |
| System.Collections.Generic.IComparer`1<-T>*                  | BCL                    |
| System.Collections.Generic.IEnumerable`1<+T>*                | BCL                    |
| System.Collections.Generic.IEqualityComparer`1<-T>*          | BCL                    |
| System.Guid                                                  | BCL                    |
| System.MulticastDelegate                                     | BCL                    |
| System.Reflection.CallingConventions                         | Runtime Infrastructure |
| System.Runtime.InteropServices.GuidAttribute                 | Runtime Infrastructure |
| System.Func\`1<+TResult>...System.Func`9<-T1..-T8, +TResult> | BCL                    |
| System.Collections.Generic.Comparer`1<T>                     | BCL                    |
| System.Collections.Generic.EqualityComparer`1<T>             | BCL                    |
| System.Collections.Generic.ISet`1<T>                         | BCL                    |
| System.Collections.Generic.LinkedList`1<T>                   | BCL                    |
| System.Collections.Generic.LinkedList`1<T>.Enumerator        | BCL                    |
| System.Collections.Generic.LinkedListNode`1<T>               | BCL                    |
| System.Collections.Generic.Queue`1<T>                        | BCL                    |
| System.Collections.Generic.Stack`1<T>                        | BCL                    |
| System.Collections.Generic.Stack`1<T>.Enumerator             | BCL                    |
| System.Collections.Stack                                     | BCL                    |
| System.DBNull                                                | BCL                    |
| System.Runtime.InteropServices.Marshal                       | Runtime Infrastructure |
| System.Runtime.InteropServices.SafeBuffer                    | Runtime Infrastructure |
| System.Runtime.InteropServices.SafeHandle                    | Runtime Infrastructure |
| System.Threading.AutoResetEvent                              | BCL                    |
| System.Threading.EventWaitHandle                             | BCL                    |
| System.Threading.ManualResetEvent                            | BCL                    |
| System.WeakReference                                         | BCL                    |
| System.Runtime.CompilerServices.TypeForwardedToAttribute     | BCL                    |
| System.Runtime.CompilerServices.TypeForwardedFromAttribute   | BCL                    |
| System.Threading.EventResetMode                              | BCL                    |
| System.Runtime.InteropServices.DllAttribute*                 | Runtime Infrastructure |
| System.Math*                                                 | BCL                    |

One type, INullableValue, has been removed from the Standard. INullableValue is incompatible with the semantics of boxing as defined in the previous edition of the Standard. The references to it were included in error from an earlier draft and no implementations are known to have ever included it.

Technical Report 89 (TR89), which was submitted during the third edition of this Ecma standard, will no longer be part of the submission. TR89 specified a collection of generic types, to help enhance inter-language interoperability, under consideration for inclusion in a future version of the standard. That consideration has now occurred and TR89 has fulfilled its role. A selection of the types covered in TR89 has been introduced into this edition of the standard. An archive version of TR89 will continue to be available from Ecma.

The following companies and organizations have participated in the development of this standard, and their contributions are gratefully acknowledged: Eiffel Software, Kahu Research, Microsoft Corporation, Novell Corporation, Twin Roots. For previous editions, the following companies and organizations are also acknowledged: Borland, Fujitsu Software Corporation, Hewlett-Packard, Intel Corporation, IBM Corporation, IT University of Copenhagen, Jagger Software Ltd., Monash University, Netscape, Phone.Com, Plum Hall, and Sun Microsystems.

## I.1 Scope

This International Standard defines the Common Language Infrastructure (CLI) in which applications written in multiple high-level languages can be executed in different system environments without the need to rewrite those applications to take into consideration the unique characteristics of those environments. This International Standard consists of the following parts:

- Partition I: Concepts and Architecture – Describes the overall architecture of the CLI, and provides the normative description of the Common Type System (CTS), the Virtual Execution System (VES), and the Common Language Specification (CLS). It also provides an informative description of the metadata.
- Partition II: Metadata Definition and Semantics – Provides the normative description of the metadata: its physical layout (as a file format), its logical contents (as a set of tables and their relationships), and its semantics (as seen from a hypothetical assembler, *ilasm*).
- Partition III: CIL Instruction Set – Describes the Common Intermediate Language (CIL) instruction set.
- Partition IV: Profiles and Libraries – Provides an overview of the CLI Libraries, and a specification of their factoring into Profiles and Libraries. A companion file, CLILibrary.xml, considered to be part of this Partition, but distributed in XML format, provides details of each class, value type, and interface in the CLI Libraries.
- Partition V: Debug Interchange Format – Describes a standard way to interchange debugging information between CLI producers and consumers.
- Partition VI: Annexes – Contains some sample programs written in CIL Assembly Language (ILAsm), information about a particular implementation of an assembler, a machine-readable description of the CIL instruction set which can be used to derive parts of the grammar used by this assembler as well as other tools that manipulate CIL, a set of guidelines used in the design of the libraries of §Partition IV, and portability considerations.

## I.2 Conformance

A system claiming conformance to this International Standard shall implement all the normative requirements of this standard, and shall specify the profile (see §Partition IV Library – Profiles) that it implements. The minimal implementation is the Kernel Profile. A conforming implementation can also include additional functionality provided that functionality does not prevent running code written to rely solely on the profile as specified in this standard. For example, a conforming implementation can provide additional classes, new methods on existing classes, or a new interface on a standardized class, but it shall not add methods or properties to interfaces specified in this standard.

A compiler that generates Common Intermediate Language (CIL, see §Partition III) and claims conformance to this International Standard shall produce output files in the format specified in this standard, and the CIL it generates shall be correct CIL as specified in this standard. Such a compiler can also claim that it generates *verifiable* code, in which case, the CIL it generates shall be verifiable as specified in this standard.

## I.3 Normative references

TODO

## I.4 Conventions

### I.4.1 Organization

The divisions of this International Standard are organized using a hierarchy. At the top level is the *Partition*. The next level is the *clause*, followed by *subclause*. Divisions within a subclause are also referred to as subclauses. Partitions are numbered using Roman numerals. All other divisions are numbered using Arabic digits with their place in the hierarchy indicated by nested numbers. For example, Partition II, 14.4.3.2 refers to subclause 2 in subclause 3 in subclause 4 in clause 14 in Partition II.

### I.4.2 Informative text

This International Standard is intended to be used by implementers, academics, and application programmers. As such, it contains explanatory material that, strictly speaking, is not necessary in a formal specification.

Examples are provided to illustrate possible forms of the constructions described. References are used to refer to related clauses or subclauses. Notes are provided to give advice or guidance to implementers or programmers. Annexes provide additional information.

Except for whole clauses or subclauses that are identified as being informative, informative text that is contained within normative clauses and subclauses is identified as follows:

- The beginning and end of a block of informative text is marked using rectangular boxes.
- As some informative passages span pages, informative text also contains a bold set of vertical black stripes in the right margin.
- By the use of the following pairs of markers: [*Example*: ... *end example*], [*Note*: ... *end note*], and [*Rationale*: ... *end rationale*].

Unless text is identified as being informative, it is normative.

## I.5 Terms and definitions

For the purposes of this International Standard, the following definitions apply. Other terms are defined where they appear in *italic* type.

**ANSI character:** A character from an implementation-defined 8-bit character set whose first 128 code points correspond exactly to those of ISO/IEC 10646.

**ANSI string:** A string of ANSI characters, of which the final character has the value all-bits- zero.

**argument:** The expression supplied for a parameter at the point of the call to a method. 

**assembly:** A configured set of loadable code modules and other resources that together implement a unit of functionality.

**attribute:** A characteristic of a type and/or its members that contains descriptive information. While the most common attributes are predefined, and have a specific encoding in the metadata associated with them, user-defined attributes can also be added to the metadata.

**behavior, implementation-specific:** Unspecified behavior, for which each implementation is required to document the choice it makes.

**behavior, unspecified:** Behavior, for a well-formed program construct and correct data, that depends on the implementation. The implementation is not required to document which behavior occurs.

**behavior, undefined:** Behavior, such as might arise upon use of an erroneous program construct or erroneous data, for which this International Standard imposes no requirements. Undefined behavior can also be expected in cases when this International Standard omits the description of any explicit definition of behavior.

**boxing:** The conversion of a value having some value type, to a newly allocated instance of the reference type `System.Object`.

**Common Intermediate Language (CIL):** The instruction set understood by the VES. 

**Common Language Infrastructure (CLI):** A specification for the format of executable code, and the run-time environment that can execute that code.

**Common Language Specification (CLS):** An agreement between language designers and framework (class library) designers. It specifies a subset of the CTS and a set of usage conventions.

**Common Type System (CTS):** A unified type system that is shared by compilers, tools, and the CLI itself. It is the model that defines the rules the CLI follows when declaring, using, and managing types. The CTS establishes a framework that enables cross-language integration, type safety, and high performance code execution.

**delegate:** A reference type such that an instance of it can encapsulate one or more methods in an invocation list. Given a delegate instance and an appropriate set of arguments, one can invoke all of the methods in a delegate’s invocation list with that set of arguments.

**event:** A member that enables an object or class to provide notifications.

**Execution Engine:** See **Virtual Execution System**.

**field:** A member that designates a typed memory location that stores some data in a program. 

**garbage collection:** The process by which memory for managed data is allocated and released.

**generic argument:** The actual type used to instantiate a particular generic type or generic method. For example, in `List<string>`, `string` is the generic argument corresponding to the generic parameter `T` in the generic type definition `List<T>`.

**generic parameter:** A parameter within the definition of a generic type or generic method that acts as a place holder for a generic argument. For example, in the generic type definition `List<T>`, `T` is a generic parameter.

**generics:** The feature that allows types and methods to be defined such that they are parameterized with one or more generic parameters.

**library:** A repository for a set of types, which are grouped into one or more assemblies. A library can also contain modifications to types defined in other libraries. For example, a library can include additional methods, interfaces, and exceptions for types defined in other libraries.

**managed code:** Code that contains enough information to allow the CLI to provide a set of core services. For example, given an address for a method inside the code, the CLI must be able to locate the metadata describing that method. It must also be able to walk the stack, handle exceptions, and store and retrieve security information.

**managed data:** Data that is allocated and released automatically by the CLI, through a process called garbage collection.

**manifest:** That part of an assembly that specifies the following information about that assembly: its version, name, culture, and security requirements; which other files, if any, belong to that assembly, along with a cryptographic hash of each file; which of the types defined in other files of that assembly are to be exported from that assembly; and, optionally, a digital signature for the manifest itself, and the public key used to compute it.

**member:** Any of the fields, array elements, methods, properties, and events of a type.

**metadata:** Data that describes and references the types defined by the CTS. Metadata is stored in a way that is independent of any particular programming language. Thus, metadata provides a common interchange mechanism for use between tools that manipulate programs (such as compilers and debuggers) as well as between these tools and the VES.

**method:** A member that describes an operation that can be performed on values of an exact type.

**method, generic:** A method (be it static, instance, or virtual), defined within a type, whose signature includes one or more generic parameters, not present in the type definition itself. The enclosing type itself might, or might not, be generic. For example, within the generic type `List<T>`, the generic method `S ConvertTo<S>()` is generic.

**method, non-generic:** A method that is not generic.

**module:** A single file containing content that can be executed by the VES.

**object:** An instance of a reference type. An object has more to it than a value. An object is self-typing; its type is explicitly stored in its representation. It has an identity that distinguishes it from all other objects, and it has slots that store other entities (which can be either objects or values). While the contents of its slots can be changed, the identity of an object never changes.

**parameter:** The name used in the header and body of a method to refer to an argument value supplied at the point of call.

**profile:** A set of libraries, grouped together to form a consistent whole that provides a fixed level of functionality.

**property:** A member that defines a named value and the methods that access that value. A property definition defines the accessing contracts on that value. Hence, the property definition specifies which accessing methods exist and their respective method contracts.

**signature:** The part of a contract that can be checked and automatically enforced. Signatures are formed by adding constraints to types and other signatures. A constraint is a limitation on the use of or allowed operations on a value or location.

**type, generic:** A type whose definition is parameterized by one or more other types; for example, `List<T>`, where `T` is a generic parameter. The CLI supports the creation and use of instances of generic types. For example, `List<int32>` or `List<string>`.

**type, reference:** A type such that an instance of it contains a reference to its data. 

**type, value:** A type such that an instance of it directly contains all its data.

**unboxing:** The conversion of a value having type `System.Object`, whose run-time type is a value type, to a value type instance.

**unmanaged code:** Code that is not managed. 

**unmanaged data:** Data that is not managed.

**value:** A simple bit pattern for something like an integer or a float. Each value has a type that describes both the storage that it occupies and the meanings of the bits in its representation, and also the operations that can be performed on that representation. Values are intended for representing the simple types and non-objects in programming languages.

**verification:** The checking of both CIL and its related metadata to ensure that the CIL code sequences do not permit any access to memory outside the program’s logical address space. In conjunction with the validation tests, verification ensures that the program cannot access memory or other resources to which it is not granted access.

**Virtual Execution System (VES):** This system implements and enforces the CTS model. The VES is responsible for loading and running programs written for the CLI. It provides the services needed to execute managed code and data using the metadata to connect separately generated modules together at runtime. The VES is also known as the **Execution Engine**.

## I.6 Overview of the Common Language Infrastructure

The Common Language Infrastructure (CLI) provides a specification for executable code and the execution environment (the Virtual Execution System) in which it runs. Executable code is presented to the VES as modules. A module is a single file containing executable content in the format specified in §Partition II.

>  **The remainder of this clause and its subclauses contain only informative text**

At the center of the CLI is a unified type system, the Common Type System that is shared by compilers, tools, and the CLI itself. It is the model that defines the rules the CLI follows when declaring, using, and managing types. The CTS establishes a framework that enables cross- language integration, type safety, and high performance code execution. This clause describes the architecture of the CLI by describing the CTS.

The following four areas are covered in this clause:

- **The Common Type System (CTS)** – The CTS provides a rich type system that supports the types and operations found in many programming languages. The CTS is intended to support the complete implementation of a wide range of programming languages. See §I.8.
- **Metadata** – The CLI uses metadata to describe and reference the types defined by the CTS. Metadata is stored (that is, persisted) in a way that is independent of any particular programming language. Thus, metadata provides a common interchange mechanism for use between tools (such as compilers and debuggers) that manipulate programs, as well as between these tools and the VES. See §I.9.
- **The Common Language Specification (CLS)** – The CLS is an agreement between language designers and framework (that is, class library) designers. It specifies a subset of the CTS and a set of usage conventions. Languages provide their users the greatest ability to access frameworks by implementing at least those parts of the CTS that are part of the CLS. Similarly, frameworks will be most widely used if their publicly exported aspects (e.g., classes, interfaces, methods, and fields) use only types that are part of the CLS and that adhere to the CLS conventions. See §I.10.
- **The Virtual Execution System (VES)** – The VES implements and enforces the CTS model. The VES is responsible for loading and running programs written for the CLI. It provides the services needed to execute managed code and data, using the metadata to connect separately generated modules together at runtime (late binding). See §I.12.

Together, these aspects of the CLI form a unifying infrastructure for designing, developing, deploying, and executing distributed components and applications. The appropriate subset of the CTS is available from each programming language that targets the CLI. Language-based tools communicate with each other and with the VES using metadata to define and reference the types used to construct the application. The VES uses the metadata to create instances of the types as needed and to provide data type information to other parts of the infrastructure (such as remoting services, assembly downloading, and security).

### I.6.1 Relationship to type safety

Type safety is usually discussed in terms of what it does (e.g., guaranteeing encapsulation between different objects) or in terms of what it prevents (e.g., memory corruption by writing where one shouldn’t). However, from the point of view of the CTS, type safety guarantees that:

- **References are what they say they are** – Every reference is typed, the object or value referenced also has a type, and these types are assignment compatible (see §I.8.7).
- **Identities are who they say they are** – There is no way to corrupt or spoof an object, and, by implication, a user or security domain. Access to an object is through accessible functions and fields. An object can still be designed in such a way that security is compromised. However, a local analysis of the class, its methods, and the things it uses, as opposed to a global analysis of all uses of a class, is sufficient to assess the vulnerabilities.
- **Only appropriate operations can be invoked** – The reference type defines the accessible functions and fields. This includes limiting visibility based on where the reference is (e.g., protected fields only visible in derived classes).

The CTS promotes type safety (e.g., everything is typed). Type safety can optionally be enforced. The hard problem is determining if an implementation conforms to a type-safe declaration. Since the declarations are carried along as metadata with the compiled form of the program, a compiler from the Common Intermediate Language (CIL) to native code (see §I.8.8) can type-check the implementations.

### I.6.2 Relationship to managed metadata-driven execution

Metadata describes code by describing the types that the code defines and the types that it references externally. The compiler produces the metadata when the code is produced. Enough information is stored in the metadata to:

- **Manage code execution** – not just load and execute, but also memory management and execution state inspection.
- **Administer the code** – Installation, resolution, and other services.
- **Reference types in the code** – Importing into other languages and tools as well as scripting and automation support.

The CTS assumes that the execution environment is metadata-driven. Using metadata allows the CLI to support:

- **Multiple execution models** – The metadata allows the execution environment to deal with a mixture of interpreted, JIT-compiled, native, and legacy code, and still present uniform services to tools like debuggers and profilers, consistent exception handling and unwinding, reliable code access security, and efficient memory management.
- **Auto support for services** – Since the metadata is available at execution time, the execution environment and the base libraries can automatically supply support for reflection, automation, serialization, remote objects, and inter-operability with existing unmanaged native code with little or no effort on the part of the programmer.
- **Better optimization** – Using metadata references instead of physical offsets, layouts, and sizes allows the CLI to optimize the physical layouts of members and dispatch tables. In addition, this allows the generated code to be optimized to match the particular CPU or environment.
- **Reduced binding brittleness** – Using metadata references reduces version-to-version brittleness by replacing compile-time object layout with load-time layout and binding by name.
- **Flexible deployment resolution** – Since we can have metadata for both the reference and the definition of a type, more robust and flexible deployment and resolution mechanisms are possible. Resolution means that by looking in the appropriate set of places it is possible to find the implementation that best satisfies these requirements for use in this context. There are five elements of information in the foregoing: requirements and context are made available via metadata; where to look, how to find an implementation, and how to decide the best match all come from application packaging and deployment.

#### I.6.2.1 Managed code

**Managed code** is code that provides enough information to allow the CLI to provide a set of core services, including

- Given an address inside the code for a method, locate the metadata describing the method
- Walk the stack
- Handle exceptions
- Store and retrieve security information

This standard specifies a particular instruction set, the CIL (see §Partition III), and a file format (see §Partition II) for storing and transmitting managed code.

#### I.6.2.2 Managed data

**Managed data** is data that is allocated and released automatically by the CLI, through a process called **garbage collection**.

#### I.6.2.3 Summary

The CTS is about integration between languages: using another language’s objects as if they were one’s own.

The objective of the CLI is to make it easier to write components and applications in any language. It does this by defining a standard set of types, by making all components fully self- describing, and by providing a high performance common execution environment. This ensures that all CLI-compliant system services and components will be accessible to all CLI-aware languages and tools. In addition, this simplifies deployment of components and applications that use them, all in a way that allows compilers and other tools to leverage the high performance execution environment. The CTS covers, at a high level, the concepts and interactions that make all of this possible.

The discussion is broken down into four areas:

- Type System – What types are and how to define them.
- Metadata – How types are described and how those descriptions are stored.
- Common Language Specification – Restrictions required for language interoperability.
- Virtual Execution System – How code is executed and how types are instantiated, interact, and die.

>  **End informative text**

## I.7 Common Language Specification

### I.7.1 Introduction

The CLS is a set of rules intended to promote language interoperability. These rules shall be followed in order to conform to the CLS. They are described in greater detail in subsequent clauses and are summarized in §I.11. CLS conformance is a characteristic of types that are generated for execution on a CLI implementation. Such types must conform to the CLI standard, in addition to the CLS rules. These additional rules apply only to types that are visible in assemblies other than those in which they are defined, and to the members that are accessible outside the assembly; that is, those that have an accessibility of public, family (but not on sealed types), or family-or-assembly (but not on sealed types).

[*Note*: A library consisting of CLS-compliant code is herein referred to as a *framework*. Compilers that generate code for the CLI can be designed to make use of such libraries, but not to be able to produce or extend such library code. These compilers are referred to as *consumers*. Compilers that are designed to both produce and extend frameworks are referred to as *extenders*. In the description of each CLS rule, additional informative text is provided to assist the reader in understanding the rule’s implication for each of these situations. *end note*]

### I.7.2 Views of CLS compliance

> **This block contains only informative text**

The CLS is a set of rules that apply to generated assemblies. Because the CLS is designed to support interoperability for libraries and the high-level programming languages used to write them, it is often useful to think of the CLS rules from the perspective of the high-level source code and tools, such as compilers, that are used in the process of generating assemblies. For this reason, informative notes are added to the description of CLS rules to assist the reader in understanding the rule’s implications for several different classes of tools and users. The different viewpoints used in the description are called **framework**, **consumer**, and **extender**, and are described here.

#### I.7.2.1 CLS framework

A library consisting of CLS-compliant code is herein referred to as a framework. Frameworks are designed for use by a wide range of programming languages and tools, including both CLS consumer and extender languages. By adhering to the rules of the CLS, authors of libraries ensure that the libraries will be usable by a larger class of tools than if they chose not to adhere to the CLS rules. The following are some additional guidelines that CLS-compliant frameworks should follow:

- Avoid the use of names commonly used as keywords in programming languages.
- Not expect users of the framework to be able to author nested types.
- Assume that implementations of methods of the same name and signature on different interfaces are independent.
- Not rely on initialization of value types to be performed automatically based on specified initializer values.
- Assume users can instantiate and use generic types and methods, but do not require them to define new generic types or methods, or deal with partially constructed generic types.

Frameworks shall not:

- Require users to define new generic types/methods, override existing generic methods, or deal with partially constructed generics in any way.

A CLS Rule applies to this topic; see the normative text at the end of §7.2.

#### I.7.2.2 CLS consumer

A CLS consumer is a language or tool that is designed to allow access to all of the features supplied by CLS-compliant frameworks, but not necessarily be able to produce them. The following is a partial list of things CLS consumer tools are expected to be able to do:

- Support calling any CLS-compliant method or delegate.
- Have a mechanism for calling methods whose names are keywords in the language.
- Support calling distinct methods supported by a type that have the same name and signature, but implement different interfaces.
- Create an instance of any CLS-compliant type.
- Read and modify any CLS-compliant field.
- Access nested types.
- Access any CLS-compliant property. This does not require any special support other than the ability to call the getter and setter methods of the property.
- Access any CLS-compliant event. This does not require any special support other than the ability to call methods defined for the event.
- Have a mechanism to import, instantiate, and use generic types and methods.

[*Note*: Extenders should consider supporting:

- Type inferencing over generic methods with language-defined rules for matching.
- Casting syntax to clarify ambiguous casts to a common supertype.

*end note*]

The following is a list of things CLS consumer tools need not support:

- Creation of new types or interfaces.
- Initialization metadata (see §Partition II) on fields and parameters other than static literal fields. Note that consumers can choose to use initialization metadata, but can also safely ignore such metadata on anything other than static literal fields.

#### I.7.2.3 CLS extender

A CLS extender is a language or tool that is designed to allow programmers to both use and extend CLS-compliant frameworks. CLS extenders support a superset of the behavior supported by a CLS consumer (i.e., everything that applies to a CLS consumer also applies to CLS extenders). In addition to the requirements of a consumer, extenders are expected to be able to:

- Define new CLS-compliant types that extend any (non-sealed) CLS compliant base class.
- Have some mechanism for defining types whose names are keywords in the language.
- Provide independent implementations for all methods of all interfaces supported by a type. That is, it is not sufficient for an extender to require a single code body to implement all interface methods of the same name and signature.
- Implement any CLS-compliant interface.
- Place any CLS-compliant custom attribute on all appropriate elements of metadata.
- Define new CLS-compliant (non-generic) types that extend any (non-sealed) CLS-compliant base type. Valid base types include normal (non-generic) types and also fully constructed generic types.

[*Note*: Extenders should consider supporting:

- Type inferencing over generic methods with language-defined rules for matching.
- Casting syntax to clarify ambiguous casts to a common supertype.

*end note*]

Extenders need not support the following:

- Definition of new CLS-compliant interfaces.
- Definition of nested types.
- Definition of generic types and method.
- Overriding existing virtual generic methods.

The CLS is designed to be large enough that it is properly expressive yet small enough that all languages can reasonably accommodate it.

> **End informative text**

> **CLS Rule 48:** If two or more CLS-compliant methods declared in a type have the same name and, for a specific set of instantiations, they have the same parameter and return types, then all these methods shall be semantically equivelant at those type instantiations.

> [*Note*:
>
> **CLS (consumer):** May select any one of these methods.
>
> **CLS (extender):** Same as consumer.
>
> **CLS (framework)**: Shall not expose methods that violate this rule. 
>
> end note*]

[*Note*: To avoid confusion, the CLS rules follow historical numbering from the previous version of this Standard, despite removal/addition of rules in this version. As such, the first rule shown in this partition is Rule 48. *end note*]

### I.7.3 CLS compliance

As these rules are introduced in detail, they are described in a common format. For an example, see the first rule below. The first paragraph specifies the rule itself. This is then followed by an informative description of the implications of the rule from the three different viewpoints as described above.

The CLS defines language interoperability rules, which apply only to "externally visible" items. The CLS unit of that language interoperability is the assembly — that is, within a single assembly there are no restrictions as to the programming techniques that can be used. Thus, the CLS rules apply only to items that are visible (see §I.8.5.3) outside of their defining assembly and have **public**, **family**, or **family-or-assembly** accessibility (see §I.8.5.3.2).

> **CLS Rule 1:** CLS rules apply only to those parts of a type that are are accessible or visible outside of the defining assembly.
>
> [*Note*:
>
> **CLS (consumer):** no impact.
>
> **CLS (extender):** when checking CLS compliance at compile time, be sure to apply the rules only to information that will be exported outside the assembly.
>
> **CLS (framework)**: CLS rules do not apply to internal implementation within an assembly. A type is CLS-compliant if all its publicly accessible parts (those classes, interfaces, methods, fields, properties, and events that are available to code executing in another assembly) either 
>
> - have signatures composed only of CLS-compliant types, or
>
> -  are specifically marked as not CLS-compliant. 
>
> *end note*]

Any construct that would make it impossible to rapidly verify code is excluded from the CLS. This allows all CLS-compliant language translators to produce verifiable code if they so choose.

#### I.7.3.1 Marking items as CLS-compliant

The CLS specifies how to mark externally visible parts of an assembly to indicate whether or not they comply with the CLS requirements. (Implementers are discouraged from marking extensions to this standard as CLS-compliant.) This is done using the custom attribute mechanism (see §I.9.7 and §Partition II). The class `System.CLSCompliantAttribute` (see §Partition IV) indicates which types and type members are CLS-compliant. It also can be attached to an assembly, to specify the default level of compliance for all top-level types that assembly contains.

The constructor for `System.CLSCompliantAttribute` takes a Boolean argument indicating whether the item with which it is associated is CLS-compliant. This allows any item (assembly, type, or type member) to be explicitly marked as CLS-compliant or not.

The rules for determining CLS compliance are:

- When an assembly does not carry an explicit `System.CLSCompliantAttribute`, it shall be assumed to carry `System.CLSCompliantAttribute(false)`.
- By default, a type inherits the CLS-compliance attribute of its enclosing type (for nested types) or acquires the level of compliance attached to its assembly (for top-level types). A type can be marked as either CLS-compliant or not CLS-compliant by attaching the `System.CLSCompliantAttribute` attribute.
- By default, other members (methods, fields, properties, and events) inherit the CLS- compliance of their type. They can be marked as not CLS-compliant by attaching the attribute `System.CLSCompliantAttribute(false)`.

> **CLS Rule 2:** Members of non-CLS compliant types shall not be marked CLS-compliant.
>
> [*Note*:
>
> **CLS (consumer):** Can ignore any member that is not CLS-compliant using the above rules.
>
> **CLS (extender):** Should encourage correct labeling of newly authored assemblies and publicly exported types and members. Compile-time enforcement of the CLS rules is strongly encouraged.
>
> **CLS (framework)**: Shall correctly label all publicly exported members as to their CLS compliance. The rules specified here can be used to minimize the number of markers required (for example, label the entire assembly if all types and members are compliant or if there are only a few exceptions that need to be marked).
>
> *end note*]

## I.8 Common Type System

Types describe values and specify a contract (see §I.8.6) that all values of that type shall support. Because the CTS supports Object-Oriented Programming (OOP) as well as functional and procedural programming languages, it deals with two kinds of entities: objects and values. **Values** are simple bit patterns for things like integers and floats; each value has a type that describes both the storage that it occupies and the meanings of the bits in its representation, and also the operations that can be performed on that representation. Values are intended for representing the corresponding simple types in programming languages like C, and also for representing non-objects in languages like C++ and Java™.

**Objects** have rather more to them than do values. Each object is self-typing, that is, its type is explicitly stored in its representation. It has an identity that distinguishes it from all other objects, and it has slots that store other entities (which can be either objects or values). While the contents of its slots can be changed, the identity of an object never changes.

There are several kinds of objects and values, as shown in the (informative) diagram below.

The generics feature allows a whole family of types and methods to be defined using a pattern, which includes placeholders called *generic parameters*. These generic parameters are replaced, as required, by specific types, to instantiate whichever member of the family is actually required. The design of generics meets the following goals:

- Orthogonality: Where possible, generic types can occur in any context where existing CLI types can occur.
- Language independence: No assumptions about the source language are made. But CLI-generics attempts to support existing generics-like features of as many languages as possible. Furthermore, the design permits clean extensions of languages currently lacking generics.
- Implementation independence: An implementation of the CLI is allowed to specialize representations and code on a case-by-case basis, or to share all representations and code, perhaps boxing and unboxing values to achieve this.
- Implementation efficiency: Performance of generics is no worse than the use of `Object` to simulate generics; a good implementation can do much better, avoiding casts on reference type instantiations, and producing specialized code for value type instantiations.
- Statically checkable at point of definition: A generic type definition can be validated and verified independently of its instantiations. Thus, a generic type is statically verifiable, and its methods are guaranteed to JIT-compile for all valid instantiations.
- Uniform behavior with respect to generic parameters: In general, the behavior of parameterized types and generic methods is “the same” at all type instantiations.

In addition, CLI supports covariant and contravariant generic parameters, with the following characteristics:

- It is type-safe (based on purely static checking)
- Simplicity: in particular, variance is only permitted on generic interfaces and generic delegates (not classes or value-types)
- Variance affects call instructions that invoke a method from a variant interface. For non- variant interfaces, a method of the exact type specified in the call instruction must exist, and is invoked. For variant interfaces, a method of the exact type specified in the call instruction need not exist; only one that is a variant match for the type. Furthermore, if multiple matches exist, the declaration order and derivation of the methods determine which one is called, and a variant match may be invoked even if an exact match exists (II.12.2). All language systems targeting the CLI must take into account variance whether or not the source language supports the feature.
- Enable implementation of more complex covariance scheme as used in some languages, e.g. Eiffel.

> **This figure is informative**

**Figure 1: Type System**

TODO

[*Note*: A managed pointer might point into the heap. *end note*]

> **End informative figure**

### I.8.1 Relatoinship to object-oriented programming

> **This subclause contains only informative text**

The term **type** is often used in the world of value-oriented programming to mean data representation. In the object-oriented world it usually refers to behavior rather than to representation. In the CTS, type is used to mean both of these things: two entities have compatible types if and only if they have compatible representations and compatible behaviors. Thus, in the CTS, if one type is derived from a base type, then instances of the derived type can be substituted for instances of the base type because both the representation and the behavior are compatible.

Unlike in some OOP languages, in the CTS, two objects that have fundamentally different representations have different types. Some OOP languages use a different notion of type. They consider two objects to have the same type if they respond in the same way to the same set of messages. This notion is captured in the CTS by saying that the objects implement the same interface.

Similarly, some OOP languages (e.g., Smalltalk) consider message passing to be the fundamental model of computation. In the CTS, this corresponds to calling virtual methods (see §I.8.4.4), where the signature of the virtual method plays the role of the message.

The CTS itself does not directly capture the notion of “typeless programming.” That is, there is no way to call a non-static method without knowing the type of the object. Nevertheless, typeless programming can be implemented based on the facilities provided by the reflection package (see §Partition IV - Reflection) if it is implemented.

> **End informative text**

### I.8.2 Values and types

Types describe *values*. Any value described by a type is called an *instance* of that type. Any use of a value—storing it, passing it as an argument, operating on it—requires a type. This applies in particular to all variables, arguments, evaluation stack locations, and method results. The type defines the allowable values and the allowable operations supported by the values of the type. All operators and functions have expected types for each of the values accessed or used.

Every value has an *exact type* that fully describes its type properties.

Every value is an instance of its exact type, and can be an instance of other types as well. In particular, if a value is an instance of a type that inherits from another type, it is also an instance of that other type.

#### I.8.2.1 Value types and reference types

There are two kinds of types: **value types** and **reference types**.

- Value types – The values described by a value type are self-contained (each can be understood without reference to other values).
- Reference types – A value described by a reference type denotes the location of another value. There are four kinds of reference type:
  - An **object type** is a reference type of a self-describing value (see §I.8.2.3). Some object types (e.g., abstract classes) are only a partial description of a value.
  - An **interface type** is always a partial description of a value, potentially supported by many object types.
  - A **pointer type** is a compile-time description of a value whose representation is a machine address of a location. Pointers are divided into managed (§I.8.2.1.1, §I.12.1.1.2) and unmanaged (§I.8.9.2).
  - Built-in reference types.

##### 1.8.2.1.1 Managed pointers and related types

A **managed pointer** (§I.12.1.1.2), or **byref** (§I.8.6.1.3, §I.12.4.1.5.2), can point to a local variable, parameter, field of a compound type, or element of an array. However, when a call crosses a remoting boundary (see §I.12.5) a conforming implementation can use a copy-in/copy- out mechanism instead of a managed pointer. Thus programs shall not rely on the aliasing behavior of true pointers. Managed pointer types are only allowed for local variable (§I.8.6.1.3) and parameter signatures (§I.8.6.1.4); they cannot be used for field signatures (§I.8.6.1.2), as the element type of an array (§I.8.9.1), and boxing a value of managed pointer type is disallowed (§I.8.2.4). Using a managed pointer type for the return type of methods (§I.8.6.1.5) is not verifiable (§I.8.8).

[*Rationale*: For performance reasons items on the GC heap may not contain references to the interior of other GC objects, this motivates the restrictions on fields and boxing. Further returning a managed pointer which references a local or parameter variable may cause the reference to outlive the variable, hence it is not verifiable. *end rationale*]

There are three value types in the Base Class Library (see §Partition IV - BCL): **System.TypedReference**, **System.RuntimeArgumentHandle**, and **System.ArgIterator**; which are treated specially by the CLI.

The value type **System.TypedReference**, or **typed reference** or **typedref**, (§I.8.2.2, §I.8.6.1.3, §I.12.4.1.5.3) contains both a managed pointer to a location and a runtime representation of the type that can be stored at that location. Typed references have the same restrictions as byrefs. Typed references are created by the CIL instruction `mkrefany` (see §Partition III).

The value types **System.RuntimeArgumentHandle** and **System.ArgIterator** (see §Partition IV and CIL instruction `arglist` in §Partition III), contain pointers into the VES stack. They can be used for local variable and parameter signatures. The use of these types for fields, method return types, the element type of an array, or in boxing is not verifiable (§I.8.8). These two types are referred to as **byref**-like types.

#### I.8.2.2 Built-in value and reference types

The following data types are an integral part of the CTS and are supported directly by the VES. They have special encoding in the persisted metadata:

**Table I.1: Special Encoding**

| Name in CIL assembler (see §Parition II) | CLS Type? | Name in class library (see §Parition IV) | Description                   |
| ---------------------------------------- | --------- | ---------------------------------------- | ----------------------------- |
| `bool`<sup>1</sup>                       | Yes       | `System.Boolean`                         | True/false value              |
| `char`<sup>1</sup>                       | Yes       | `System.Char`                            | Unicode 16-bit char.          |
| `object`                                 | Yes       | `System.Object`                          | Object or boxed value type    |
| `string`                                 | Yes       | `System.String`                          | Unicode string                |
| `float32`                                | Yes       | `System.Single`                          | IEC 60559:1989 32-bit float   |
| `float64`                                | Yes       | `System.Double`                          | IEC 60559:1989 64-bit float   |
| `int8`                                   | No        | `System.SByte`                           | Signed 8-bit integer          |
| `int16`                                  | Yes       | `System.Int16`                           | Signed 16-bit integer         |
| `int32`                                  | Yes       | `System.Int32`                           | Signed 32-bit integer         |
| `int64`                                  | Yes       | `System.Int64`                           | Signed 64-bit integer         |
| `native int`                             | yes       | `System.IntPtr`                          | Signed integer, native size   |
| `native unsigned int`                    | No        | `System.UIntPtr`                         | Unsigned integer, native size |
| `typedref`                               | No        | `System.TypedRefernece`                  | Pointer plus exact type       |
| `unsigned int8`                          | Yes       | `System.Byte`                            | Unsigned 8-bit integer        |
| `unsigned int16`                         | No        | `System.UInt16`                          | Unsigned 16-bit integer       |
| `unsigned int32`                         | No        | `System.UInt32`                          | Unsigned 32-bit integer       |
| `unsigned int64`                         | No        | `System.UInt64`                          | Unsigned 64-bit integer       |

<sup>1</sup> `bool` and `char` are integer types in the categorization shown in the figure above.

#### I.8.2.3 Classes, interfaces, and objects

A type fully describes a value if it unambiguously defines the value’s representation and the operations defined on that value.

For a value type, defining the representation entails describing the sequence of bits that make up the value’s representation. For a reference type, defining the representation entails describing the location and the sequence of bits that make up the value’s representation.

A **method** describes an operation that can be performed on values of an exact type. Defining the set of operations allowed on values of an exact type entails specifying named methods for each operation.

Some types are only a partial description; for example, **interface types**. These types describe a subset of the operations and none of the representation, and hence, cannot be an exact type of any value. Hence, while a value has only one exact type, it can also be a value of many other types as well. Furthermore, since the exact type fully describes the value, it also fully specifies all of the other types that a value of the exact type can have.

While it is true that every value has an exact type, it is not always possible to determine the exact type by inspecting the representation of the value. In particular, it is *never* possible to determine the exact type of a value of a value type. Consider two of the built-in value types, 32-bit signed and unsigned integers. While each type is a full specification of their respective values (i.e., an exact type) there is no way to derive that exact type from a value’s particular 32-bit sequence.

For some values, called **objects**, it is always possible to determine the exact type from the value. Exact types of objects are also called **object types**. Objects are values of reference types, but not all reference types describe objects. Consider a value that is a pointer to a 32-bit integer, a kind of reference type. There is no way to discover the type of the value by examining the pointer bits; hence it is not an object. Now consider the built-in CTS reference type `System.String` (see §Partition IV). The exact type of a value of this type is always determinable by examining the value, hence values of type `System.String` are objects, and `System.String` is an object type.

#### I.8.2.4 Boxing and unboxing of values

For every value type, the CTS defines a corresponding reference type called the **boxed type**. The reverse is not true: In general, reference types do not have a corresponding value type. The representation of a value of a boxed type (a **boxed value**) is a location where a value of the value type can be stored. A boxed type is an object type and a boxed value is an object.

A boxed type cannot be directly referred to by name, therefore no field or local variable can be given a boxed type. The closest named base class to a boxed enumerated value type is `System.Enum`; for all other value types it is `System.ValueType`. Fields typed `System.ValueType` can only contain the null value or an instance of a boxed value type. Locals typed `System.Enum` can only contain the null value or an instance of a boxed enumeration type.

All value types have an operation called `box`. Boxing a value of any value type produces its boxed value; i.e., a value of the corresponding boxed type containing a bitwise copy of the original value. If the value type is a nullable type—defined as an instantiation of the value type System.Nullable\<*T*\>—the result is a null reference or bitwise copy of its Value property of type *T*, depending on its `HasValue` property (false and true, respectively). All boxed types have an operation called `unbox`, which results in a managed pointer to the bit representation of the value.

The `box` instruction can be applied to more than just value types; such types are called *boxable* types. A type is boxable if it is one of the following:

- A value type (including instantiations of generic value types) excluding typed references (§I.8.2.1.1). Boxing a byref-like type is not verifiable (§I.8.2.1.1).
  [*Rationale*: Typed references are excluded so that objects in the GC heap cannot contain references to the interior of other GC objects (§I.8.2.1.1). Byref-like types contain embedded pointers to entries in the VES stack. If byref-like types are boxed these embedded pointers could outlive the entries to which they point, so this operation is unverifiable. *end rationale*]
- A reference type (including classes, arrays, delegates, and instantiations of generic classes) excluding managed pointers/byrefs (§I.8.2.1.1)
- A generic parameter (to a generic type definition, or a generic method definition) [*Note*: Boxing and unboxing of generic arguments adds performance overhead to a CLI implementation. The constrained. prefix can improve performance during virtual dispatch to a method defined by a value type, by avoiding boxing the value type. *end note*]

The type `System.Void` is never boxable.

Interfaces and inheritance are defined only on reference types. Thus, while a value type definition (§I.8.9.7) can specify both interfaces that shall be implemented by the value type and the class (`System.ValueType` or `System.Enum`) from which it inherits, these apply only to boxed values.

> **CLS Rule 3:** Boxed value types are not CLS-compliant.
>
> [*Note*:
>
> In lieu of boxed types, use `System.Object`, `System.ValueType`, or `System.Enum`, as appropriate.
>
> **CLS (consumer):** Need not import boxed value types.
>
> **CLS (extender):** Need not provide syntax for defining or using boxed value types.
>
> **CLS (framework)**: Shall not use boxed value types in its publicly exported aspects.
>
> *end note*]

#### I.8.2.5 Identity and equality of values

There are two binary operators defined on all pairs of values: **identity** and **equality**. They return a Boolean result, and are mathematical **equivalence** operators; that is, they are:

- Reflexive – `a op a` is true.
- Symmetric – `a op b` is true if and only if `b op a` is true
- Transitive – if `a op b` is true and `b op c` is true, then `a op c` is true.

In addition, while identity always implies equality, the reverse is not true. To understand the difference between these operations, consider three variables, A, B, and C, whose type is `System.String`, where the arrow is intended to mean "is a reference to":

TODO: figure

The values of the variables are **identical** if the locations of the sequences of characters are the same (i.e., there is, in fact, only one string in memory). The values stored in the variables are **equal** if the sequences of characters are the same. Thus, the values of variables A and B are identical, the values of variables A and C as well as B and C are not identical, and the values of all three of A, B, and C are equal.

##### I.8.2.5.1 Identity

The identity operator is defined by the CTS as follows.

- If the values have different exact types, then they are not identical.
- Otherwise, if their exact type is a value type, then they are identical if and only if the bit sequences of the values are the same, bit by bit.
- Otherwise, if their exact type is a reference type, then they are identical if and only if the locations of the values are the same.

Identity is implemented on `System.Object` via the `ReferenceEquals` method.

##### I.8.2.5.2 Equality

For value types, the equality operator is part of the definition of the exact type. Definitions of equality should obey the following rules:

- Equality should be an equivelance ooperator, as defined above.
- Identity should imply equality, as stated earlier.
- If either (or both) operand is a boxed value, equality should be computed by
  - first unboxing any boxed operand(s), and then
  - applying the usual rules for equality on the resulting values.

Equality is implemented on `System.Object` via the `Equals` method.

[*Note*: Although two floating point NaNs are defined by IEC 60559:1989 to always compare as unequal, the contract for `System.Object.Equals` requires that overrides must satisfy the requirements for an equivalence operator. Therefore, `System.Double.Equals` and `System.Single.Equals` return True when comparing two NaNs, while the equality operator returns False in that case, as required by the IEC standard. *end note*]

### I.8.3 Locations

Values are stored in **locations**. A location can hold only one value at a time. All locations are typed. The type of the location embodies the requirements that shall be met by values that are stored in the location. Examples of locations are local variables and parameters.

More importantly, the type of the location specifies the restrictions on usage of any value that is loaded from that location. For example, a location can hold values of potentially many exact types as long as all of the types are *assignable-to* the type of the location (see below). All values loaded from a location are treated as if they are of the type of the location. Only operations valid for the type of the location can be invoked even if the exact type of the value stored in the location is capable of additional operations.

#### I.8.3.1 Assignment-compatible locations

A value can be stored in a location only if one of the types of the value is **assignment compatible** with the type of the location. A type is always *assignable*-to itself. Assignment compatibility can often be determined at compile time, in which case, there is no need for testing at run time. Assignment compatibility is described in detail in §I.8.7.

#### I.8.3.2 Coercion

Sometimes it is desirable to take a value of a type that is not *assignable-to* a location, and convert the value to a type that is *assignable-to* the type of the location. This is accomplished through **coercion** of the value. Coercion takes a value of a particular type and a desired type and attempts to create a value of the desired type that has equivalent meaning to the original value. Coercion can result in representation change as well as type change; hence coercion does not necessarily preserve object identity.

There are two kinds of coercion: **widening**, which never loses information, and **narrowing**, in which information might be lost. An example of a widening coercion would be coercing a value that is a 32-bit signed integer to a value that is a 64-bit signed integer. An example of a narrowing coercion is the reverse: coercing a 64-bit signed integer to a 32-bit signed integer. Programming languages often implement widening coercions as **implicit conversions**, whereas narrowing coercions usually require an **explicit conversion**.

Some coercion is built directly into the VES operations on the built-in types (see §I.12.1). All other coercion shall be explicitly requested. For the built-in types, the CTS provides operations to perform widening coercions with no runtime checks and narrowing coercions with runtime checks or truncation, according to the operation semantics.

#### I.8.3.3 Casting

Since a value can be of more than one type, a use of the value needs to clearly identify which of its types is being used. Since values are read from locations that are typed, the type of the value which is used is the type of the location from which the value was read. If a different type is to be used, the value is **cast** to one of its other types. Casting is usually a compile time operation, but if the compiler cannot statically know that the value is of the target type, a runtime cast check is done. Unlike coercion, a cast never changes the actual type of an object nor does it change the representation. Casting preserves the identity of objects.

For example, a runtime check might be needed when casting a value read from a location that is typed as holding a value of a particular interface. Since an interface is an incomplete description of the value, casting that value to be of a different interface type will usually result in a runtime cast check.

### I.8.4 Type members

As stated above, the type defines the allowable values and the allowable operations supported by the values of the type. If the allowable values of the type have a substructure, that substructure is described via fields or array elements of the type. If there are operations that are part of the type, those operations are described via methods on the type. Fields, array elements, and methods are called **members** of the type. Properties and events are also members of the type.

#### I.8.4.1 Fields, array elements, and values

The representation of a value (except for those of built-in types) can be subdivided into sub- values. These sub-values are either named, in which case, they are called **fields**, or they are accessed by an indexing expression, in which case, they are called **array elements**. Types that describe values composed of array elements are **array types**. Types that describe values composed of fields are **compound types**. A value cannot contain both fields and array elements, although a field of a compound type can be an array type and an array element can be a compound type.

Array elements and fields are typed, and these types never change. All of the elements in an array shall have the same type. Each field of a compound type can have a different type.

#### I.8.4.2 Methods

A type can associate operations with that type or with each instance of that type. Such operations are called methods. A method is named, and has a signature (see §I.8.6.1) that specifies the allowable types for all of its arguments and for its return value, if any.

A method that is associated only with the type itself (as opposed to a particular instance of the type) is called a static method (see §I.8.4.3).

A method that is associated with an instance of the type is either an instance method or a virtual method (see §I.8.4.4). When they are invoked, instance and virtual methods are passed the instance on which this invocation is to operate (known as **this** or a **this pointer**).

The fundamental difference between an instance method and a virtual method is in how the implementation is located. An instance method is invoked by specifying a class and the instance method within that class. Except in the case of instance methods of generic types, the object passed as **this** can be **null** (a special value indicating that no instance is being specified) or an instance of any type that inherits (see §I.8.9.8) from the class that defines the method. A virtual method can also be called in this manner. This occurs, for example, when an implementation of a virtual method wishes to call the implementation supplied by its base class. The CTS allows **this** to be null **inside** the body of a virtual method.

[*Rationale*: Allowing a virtual method to be called with a non-virtual call eliminates the need for a “call super” instruction and allows version changes between virtual and non-virtual methods. It requires CIL generators to insert explicit tests for a null pointer if they don’t want the null this pointer to propagate to called methods. *end rationale*]

A virtual or instance method can also be called by a different mechanism, a **virtual call**. Any type that inherits from a type that defines a virtual method can provide its own implementation of that method (this is known as **overriding**, see §I.8.10.4). It is the exact type of the object (determined at runtime) that is used to decide which of the implementations to invoke.

#### I.8.4.3 Static fields and static methods

Types can declare locations that are associated with the type rather than any particular value of the type. Such locations are **static fields** of the type. As such, static fields declare a location that is shared by all values of the type. Just like non-static (instance) fields, a static field is typed and that type never changes. Static fields are always restricted to a single application domain basis (see §I.12.5), but they can also be allocated on a per-thread basis.

Similarly, types can also declare methods that are associated with the type rather than with values of the type. Such methods are **static methods** of the type. Since an invocation of a static method does not have an associated value on which the static method operates, there is no **this** pointer available within a static method.

#### I.8.4.4 Virtual methods

An object type can declare any of its methods as **virtual**. Unlike other methods, each exact type that implements the type can provide its own implementation of a virtual method. A virtual method can be invoked through the ordinary method call mechanism that uses the static type, method name, and types of parameters to choose an implementation, in which case, the **this** pointer can be **null**. In addition, however, a virtual method can be invoked by a special mechanism (a **virtual call**) that chooses the implementation based on the dynamically detected type of the instance used to make the virtual call rather than the type statically known at compile time. Virtual methods can be marked **final** (see §I.8.10.2).

### I.8.5 Naming

Names are given to entities of the type system so that they can be referred to by other parts of the type system or by the implementations of the types. Types, fields, methods, properties, and events have names. With respect to the type system, values, locals, and parameters do not have names. An entity of the type system is given a single name (e.g., there is only one name for a type).

#### I.8.5.1 Valid names

All name comparisons are done on a byte-by-byte (i.e., case sensitive, locale-independent, also known as code-point comparison) basis. Where names are used to access built-in VES-supplied functionality (e.g., the class initialization method) there is always an accompanying indication on the definition so as not to build in any set of reserved names.

> **CLS Rule 4:** Assemblies shall follow Annex 7 of Technical Report 15 of the Unicode Standard 3.0 governing the set of characters permitted to start and be included in identifiers, available on- line at http://www.unicode.org/unicode/reports/tr15/tr15-18.html. Identifiers shall be in the canonical format defined by Unicode Normalization Form C. For CLS purposes, two identifiers are the same if their lowercase mappings (as specified by the Unicode locale-insensitive, one-to- one lowercase mappings) are the same. That is, for two identifiers to be considered different under the CLS they shall differ in more than simply their case. However, in order to override an inherited definition the CLI requires the precise encoding of the original declaration be used.
>
> [*Note*:
>
> **CLS (consumer):** Need not consume types that violate CLS Rule 4, but shall have a mechanism to allow access to named items that use one of its own keywords as the name.
>
> **CLS (extender):** Need not create types that violate CLS Rule 4. Shall provide a mechanism for defining new names that obey these rules, but are the same as a keyword in the language.
>
> **CLS (framework)**: Shall not export types that violate CLS Rule 4. Should avoid the use of names that are commonly used as keywords in programming languages (see §Partition VI - Annex D)
>
> *end note*]

#### I.8.5.2 Assemblies and scoping

Generally, names are not unique. Names are collected into groupings called **scopes**. Within a scope, a name can refer to multiple entities as long as they are of different **kinds** (methods, fields, nested types, properties, and events) or have different signatures.

> **CLS Rule 5:** All names introduced in a CLS-compliant scope shall be distinct independent of kind, except where the names are identical and resolved via overloading. That is, while the CTS allows a single type to use the same name for a method and a field, the CLS does not.
>
> **CLS Rule 6:** Fields and nested types shall be distinct by identifier comparison alone, even though the CTS allows distinct signatures to be distinguished. Methods, properties, and events that have the same name (by identifier comparison) shall differ by more than just the return type, except as specified in CLS Rule 39.
>
> [*Note*:
>
> **CLS (consumer):** Need not consume types that violate these rules after ignoring any members that are marked as not CLS-compliant.
>
> **CLS (extender):** Need not provide syntax for defining types that violate these rules.
>
> **CLS (framework)**: Shall not mark types as CLS-compliant if they violate these rules unless they mark sufficient offending items within the type as not CLS-compliant so that the remaining members do not conflict with one another.
>
> *end note*]

A named entity has its name in exactly one scope. Hence, to identify a named entity, both a scope and a name need to be supplied. The scope is said to **qualify** the name. Types provide a scope for the names in the type; hence types qualify the names in the type. For example, consider a compound type `Point` that has a field named `x`. The name “field `x`” by itself does not uniquely identify the named field, but the **qualified name** “field `x` in type `Point`” does.

Since types are named, the names of types are also grouped into scopes. To fully identify a type, the type name shall be qualified by the scope that includes the type name. A type name is scoped by the **assembly** that contains the implementation of the type. An assembly is a configured set of loadable code modules and other resources that together implement a unit of functionality. The type name is said to be in the **assembly scope** of the assembly that implements the type. Assemblies themselves have names that form the basis of the §CTS naming hierarchy.

The **type definition**:

- Defines a name for the type being defined (i.e., the **type name**) and specifies a scope in which that name will be found.
- Defines a **member scope** in which the names of the different kinds of members (fields, methods, events, and properties) are bound. The tuple of (member name, member kind, and member signature) is unique within a member scope of a type.
- Implicitly assigns the type to the assembly scope of the assembly that contains the type definition.

The CTS supports an **enum** (also known as an **enumeration type**), an alternate name for an existing type. For the purposes of matching signatures, an enum shall not be the same as the underlying type. Instances of an enum, however, shall be *assignable-to* the underlying type, and vice versa. That is, no cast (see §I.8.3.3) or coercion (see §I.8.3.2) is required to convert from the enum to the underlying type, nor are they required from the underlying type to the enum. An enum is considerably more restricted than a true type, as follows:

- It shall have exactly one instance field, and the type of that field defines the underlying type of the enumeration.
- It shall not have any methods of its own.
- It shall derive from System.Enum (see §Partition IV Library – Kernel Profile).
- It shall not implement any interfaces of its own.
- It shall not have any properties or events of its own.
- It shall not have any static fields unless they are literal. (see §I.8.6.1.2)

The underlying type shall be a built-in integer type. Enums shall derive from System.Enum,

hence they are value types. Like all value types, they shall be sealed (see §I.8.9.9).

> **CLS Rule 7:** The underlying type of an enum shall be a built-in CLS integer type, the name of the field shall be "value__", and that field shall be marked `RTSpecialName`.
>
> **CLS Rule 8:** There are two distinct kinds of enums, indicated by the presence or absence of the `System.FlagsAttribute` (see §Partition IV Library) custom attribute. One represents named integer values; the other represents named bit flags that can be combined to generate an unnamed value. The value of an enum is not limited to the specified values.
>
> **CLS Rule 9:** Literal static fields (see §I.8.6.1) of an enum shall have the type of the enum itself.
>
> [*Note*:
>
> **CLS (consumer):** Shall accept the definition of enums that follow these rules, but need not distinguish flags from named values.
>
> **CLS (extender):** Same as consumer. Extender languages are encouraged to allow the authoring of enums, but need not do so.
>
> **CLS (framework)**: Shall not expose enums that violate these rules, and shall not assume that enums have only the specified values (even for enums that are named values).
>
> *end note*]

#### I.8.5.3 Visibility, accessibility, and security

To refer to a named entity in a scope, both the scope and the name in the scope shall be **visible** (see §I.8.5.3.1). Visibility is determined by the relationship between the entity that contains the reference (the **referent**) and the entity that contains the name being referenced. Consider the following pseudo-code:

```
class A
{
    int32 IntInsideA;
}
class B inherits from A
{
    method X(int32, int32)
    {
        IntInsideA := 15;
    }
}
```

If we consider the reference to the field `IntInsideA` in class A:

- We call class B the **referent** because it has a method that refers to that field,
- We call `IntInsideA` in class `A` the **referenced entity**.

There are two fundamental questions that need to be answered in order to decide whether the referent is allowed to access the referenced entity. The first is whether the name of the referenced entity is **visible** to the referent. If it is visible, then there is a separate question of whether the referent is **accessible** (see §I.8.5.3.2).

Access to a member of a type is permitted only if all three of the following conditions are met:

1. The type is visible and, in the case of a nested type, accessible.
2. The member is accessible.
3. All relevant security demands (see §I.8.5.3.3) have been granted.

An instantiated generic type is visible from some assembly if and only if the generic type itself and each of its component parts (generic type definition and generic arguments) are visible. For example, if *List* is exported from assembly A (i.e., declared "public") and *MyClass* is defined in assembly B but not exported, then *List*\<*MyClass*\> is visible only from within assembly B.

Accessibility of members of instantiated generic types is independent of instantiation.

Access to a member *C*<*T1, ... Tn*>.*m* is therefore permitted if the following conditions are met:

- *C*<*T1, ... Tn*> is visible.
- Member *m* within generic type *C* (i.e., *C.m*) is accessible.
- Security permissions have been granted.

##### I.8.5.3.1 Visibility of types

Only type names, not member names, have controlled visibility. Type names fall into one of the following three categories

- **Exported** from the assembly in which they are defined. While a type can be marked to *allow* it to be exported from the assembly, it is the configuration of the assembly that decides whether the type name *is* made available.
- **Not exported** outside the assembly in which they are defined.
- Nested within another type. In this case, the type itself has the visibility of the type inside of which it is nested (its **enclosing type**). See §I.8.5.3.4.

A top-level named type is *exported* if and only if it has public visibility. A type generated by a type definer is exported if and only if it is made from exported types.

A type generated by a type definer is visible if all types from which it was generated are visible.

##### I.8.5.3.2 Accessibility of members and nested types

A type scopes all of its members, and it also specifies the accessibility rules for its members. Except where noted, accessibility is decided based only on the statically visible type of the member being referenced and the type and assembly that is making the reference. The CTS supports seven different rules for accessibility:

- **compiler-controlled** – accessible only through the use of a definition, not a reference, hence only accessible from within a single compilation unit and under the control of the compiler.
- **private** – accessible only to referents in the implementation of the exact type that defines the member.
- **family** – accessible to referents that support the same type (i.e., an exact type and all of the types that inherit from it). For verifiable code (see §I.8.8), there is an additional requirement that can require a runtime check: the reference shall be made through an item whose exact type supports the exact type of the referent. That is, the item whose member is being accessed shall inherit from the type performing the access.
- **assembly** – accessible only to referents in the same assembly that contains the implementation of the type.
- **family-and-assembly** – accessible only to referents that qualify for both family and assembly access.
- **family-or-assembly** – accessible only to referents that qualify for either family or assembly access.
- **public** – accessible to all referents.

A member or nested type is exported if and only if it has public, family-or-assembly, or family accessibility, and its defining type (in the case of members) or its enclosing type (in the case of nested types) is exported.

The accessibility of a type definer is the same as that for the type from which it was generated.

In general, a member of a type can have any one of the accessibility rules assigned to it. There are three exceptions, however:

1. Members (other than nested types) defined by an interface shall be public.
2. When a type defines a virtual method that overrides an inherited definition, the accessibility shall either be identical in the two definitions or the overriding definition shall permit more access than the original definition. For example, it is possible to override an **assembly virtual** method with a new implementation that is **public virtual**, but not with one that is **family virtual**. In the case of overriding a definition derived from another assembly, it is not considered restricting access if the base definition has **family**-or-assembly access and the override has only **family** access.
3. A member defined by a nested type, or a nested type enclosed by a nested type, shall not have greater accessibility than the nested type that defines it (in the case of a member) or the nested type that encloses it (in the case of a nested type).

[*Rationale*: Languages including C++ allow this “widening” of access. Restricting access would provide an incorrect illusion of security since simply casting an object to the base class (which occurs implicitly on method call) would allow the method to be called despite the restricted accessibility. To prevent overriding a virtual method use **final** (see §I.8.10.2) rather than relying on limited accessibility. *end rationale*]

> **CLS Rule 10:** Accessibility shall not be changed when overriding inherited methods, except when overriding a method inherited from a different assembly with accessibility **family-or-assembly**. In this case, the override shall have accessibility **family**.
>
> [*Note*:
>
> **CLS (consumer):** Need not accept types that widen access to inherited virtual methods.
>
> **CLS (extender):** Need not provide syntax to widen access to inherited virtual methods.
>
> **CLS (framework)**: Shall not rely on the ability to widen access to a virtual method, either in the exported portion of the framework or by users of the framework.
>
> *end note*]

##### I.8.5.3.3 Security permissions

Access to members is also controlled by security demands that can be attached to an assembly, type, method, property, or event. Security demands are not part of a type contract (see §I.8.6), and hence are not inherited. There are two kinds of demands:

- An **inheritance demand**. When attached to a type, it requires that any type that wishes to inherit from this type shall have the specified security permission. When attached to a non-final virtual method, it requires that any type that wishes to override this method shall have the specified permission. It shall not be attached to any other member.
- A **reference demand**. Any attempt to resolve a reference to the marked item shall have specified security permission.

Only one demand of each kind can be attached to any item. Attaching a security demand to an assembly implies that it is attached to all types in the assembly unless another demand of the same kind is attached to the type. Similarly, a demand attached to a type implies the same demand for all members of the type unless another demand of the same kind is attached to the member. For additional information, see Declarative Security in §Partition II, and the classes in the `System.Security` namespace in §Partition IV.

##### I.8.5.3.4 Nested types

A type can be a member of an enclosing type, in which case, it is a nested type. A nested type has the same visibility as the enclosing type and has an accessibility as would any other member of the enclosing type. This accessibility determines which other types can make references to the nested type. That is, for a class to define a field or array element of a nested type, have a method that takes a nested type as a parameter or returns one as value, etc., the nested type shall be both visible and accessible to the referencing type. A nested type is part of the enclosing type so its methods have access to all members of its enclosing type, as well as family access to members of the type from which it inherits (see §I.8.9.8). The names of nested types are scoped by their enclosing type, not their assembly (only top-level types are scoped by their assembly). There is no requirement that the names of nested types be unique within an assembly.

### I.8.6 Contracts

**Contracts** are named. They are the shared assumptions on a set of **signatures** (see §I.8.6.1) between all implementers and all users of the contract. The signatures are the part of the contract that can be checked and enforced.

Contracts are not types; rather they specify requirements on the implementation of types. Types state which contracts they abide by (i.e., which contracts all implementations of the type shall support). An implementation of a type can be verified to check that the enforceable parts of a contract—the named signatures—have been implemented. The kinds of contracts are:

- **Class contract** – A class contract is specified with a class definition. Hence, a class definition defines both the class contract and the **class type**. The name of the class contract and the name of the class type are the same. A class contract specifies the representation of the values of the class type. Additionally, a class contract specifies the other contracts that the class type supports (e.g., which interfaces, methods, properties, and events shall be implemented). A class contract, and hence the class type, can be supported by other class types as well. A class type that supports the class contract of another class type is said to **inherit** from that class type.
- **Interface contract** – An interface contract is specified with an interface definition. Hence, an interface definition defines both the interface contract and the **interface type**. The name of the interface contract and the name of the interface type are the same. Many types can support the same interface contract. Like class contracts, interface contracts specify which other contracts the interface supports (e.g., which interfaces, methods, properties, and events shall be implemented). [*Note*: An interface type can never fully describe the representation of a value. Therefore an interface type can never support a class contract, and hence can never be a class type or an exact type. *end note*]
- **Method contract** – A method contract is specified with a method definition. A method contract is a named operation that specifies the contract between the implementation(s) of the method and the callers of the method. A method contract is always part of a type contract (class, value type, or interface), and describes how a particular named operation is implemented. The method contract specifies the contracts that each parameter to the method shall support and the contracts that the return value shall support, if there is a return value.
- **Property contract** – A property contract is specified with a property definition. There is an extensible set of operations for handling a named value, which includes a standard pair for reading the value and changing the value. A property contract specifies method contracts for the subset of these operations that shall be implemented by any type that supports the property contract. A type can support many property contracts, but any given property contract can be supported by exactly one type. Hence, property definitions are a part of the type definition of the type that supports the property.
- **Event contract** – An event contract is specified with an event definition. There is an extensible set of operations for managing a named event, which includes three standard methods (register interest in an event, revoke interest in an event, fire the event). An event contract specifies method contracts for all of the operations that shall be implemented by any type that supports the event contract. A type can support many event contracts, but any given event contract can be supported by exactly one type. Hence, event definitions are a part of the type definition of the type that supports the event.

#### I.8.6.1 Signatures

**Signatures** are the part of a contract that can be checked and automatically enforced. Signatures are formed by adding constraints to types and other signatures. A constraint is a limitation on the use of or allowed operations on a value or location. Example constraints would be whether a location can be overwritten with a different value or whether a value can ever be changed.

All locations have signatures, as do all values. Assignment compatibility requires that the signature of the value, including constraints, be compatible with the signature of the location, including constraints. There are four fundamental kinds of signatures: type signatures (see §I.8.6.1.1), location signatures (see §I.8.6.1.2), parameter signatures (see §I.8.6.1.4), and method signatures (see §I.8.6.1.5). (A fifth kind, a local signature (see §I.8.6.1.3) is really a version of a location signature.)

> **CLS Rule 11:** All types appearing in a signature shall be CLS-compliant. All types composing an instantiated generic type shall be CLS-compliant.
>
> **CLS Rule 12:** The visibility and accessibility of types and members shall be such that types in the signature of any member shall be visible and accessible whenever the member itself is visible and accessible. For example, a public method that is visible outside its assembly shall not have an argument whose type is visible only within the assembly. The visibility and accessibility of types composing an instantiated generic type used in the signature of any member shall be visible and accessible whenever the member itself is visible and accessible. For example, an instantiated generic type present in the signature of a member that is visible outside its assembly shall not have a generic argument whose type is visible only within the assembly.
>
> [*Note*:
>
> **CLS (consumer):** Need not accept types whose members violate these rules.
>
> **CLS (extender):** Need not provide syntax to violate these rules.
>
> **CLS (framework)**: Shall not violate this rule in its exported types and their members.
>
> *end note*]

The following subclauses describe the various kinds of signatures. These descriptions are cumulative: the simplest signature is a type signature; a location signature is a type signature plus (optionally) some additional attributes; and so forth.

##### I.8.6.1.1 Type signatures

Type signatures define the constraints on a value and its usage. A type, by itself, is a valid type signature. The type signature of a value cannot be determined by examining the value or even by knowing the class type of the value. The type signature of a value is derived from the location signature (see below) of the location from which the value is loaded or from the operation that computes it. Normally the type signature of a value is the type in the location signature from which the value is loaded.

[*Rationale*: The distinction between a Type Signature and a Location Signature (below) is made because certain constraints, such as “constant,” are constraints on values not locations. Future versions of this standard, or non-standard extensions, can introduce type constraints, thus making the distinction meaningful. *end rationale*]

##### Location signatures

All locations are typed. This means that all locations have a **location signature**, which defines constraints on the location, its usage, and on the usage of the values stored in the location. Any valid type signature is a valid location signature. Hence, a location signature contains a type and can additionally contain the constant constraint. The location signature can also contain **location constraints** that give further restrictions on the uses of the location. The location constraints are:

- The **init-only constraint** promises (hence, requires) that once the location has been initialized, its contents never change. Namely, the contents are initialized before any access, and after initialization, no value can be stored in the location. The contents are always identical to the initialized value (see §I.8.2.3). This constraint, while logically applicable to any location, shall only be placed on fields (static or instance) of compound types.
- The **literal constraint** promises that the value of the location is actually a fixed value of a built-in type. The value is specified as part of the constraint. Compilers are required to replace all references to the location with its value, and the VES therefore need not allocate space for the location. This constraint, while logically applicable to any location, shall only be placed on static fields of compound types. Fields that are so marked are not permitted to be referenced from CIL (they shall be in-lined to their constant value at compile time), but are available using reflection and tools that directly deal with the metadata.

> **CLS Rule 13:** The value of a literal static is specified through the use of field initialization metadata (see §Partition II Metadata). A CLS-compliant literal must have a value specified in field initialization metadata that is of exactly the same type as the literal (or of the underlying type, if that literal is an **enum**).
>
> [*Note*:
>
> **CLS (consumer):** Must be able to read field initialization metadata for static literal fields and inline the value specified when referenced. Consumers can assume that the type of the field initialization metadata is exactly the same as the type of the literal field (i.e., a consumer tool need not implement conversions of the values).
>
> **CLS (extender):** Must avoid producing field initialization metadata for static literal fields in which the type of the field initialization metadata does not exactly match the type of the field.
>
> **CLS (framework)**: Should avoid the use of syntax specifying a value of a literal that requires conversion of the value. Note that compilers can do the conversion themselves before persisting the field initialization metadata resulting in a CLS-compliant framework, but frameworks are encouraged not to rely on such implicit conversions.
>
> *end note*]

[*Note*: It might seem reasonable to provide a volatile constraint on a location that would require that the value stored in the location not be cached between accesses. Instead, CIL includes a **volatile.** prefix to certain instructions to specify that the value neither be cached nor computed using an existing cache. Such a constraint can be encoded using a custom attribute (see §I.9.7), although this standard does not specify such an attribute. *end note*]

##### I.8.6.1.3 Local signatures

A **local signature** specifies the contract on a local variable allocated during the running of a method. A local signature contains a full location signature, plus it can specify one additional constraint:

The **byref** constraint states that the content of the corresponding location is a **managed pointer**. A managed pointer can point to a local variable, parameter, field of a compound type, or element of an array. However, when a call crosses a remoting boundary (see §I.12.5) a conforming implementation can use a copy-in/copy-out mechanism instead of a managed pointer. Thus programs shall not rely on the aliasing behavior of true pointers.

In addition, there is one special local signature. The **typed reference** local variable signature states that the local will contain both a managed pointer to a location and a runtime representation of the type that can be stored at that location. A typed reference signature is similar to a byref constraint, but while the byref specifies the type as part of the byref constraint (and hence statically as part of the type description), a typed reference provides the type information dynamically. A typed reference is a full signature in itself and cannot be combined with other constraints. In particular, it is not possible to specify a **byref** whose type is **typed reference**.

The typed reference signature is actually represented as a built-in value type, like the integer and floating-point types. In the Base Class Library (see §Partition IV Library) the type is known as **System.TypedReference** and in the assembly language used in §Partition II it is designated by the keyword **typedref**. This type shall only be used for parameters and local variables. It shall not be boxed, nor shall it be used as the type of a field, element of an array, or return value.

> **CLS Rule 14:** Typed references are not CLS-compliant.
>
> [*Note*:
>
> **CLS (consumer):** There is no need to accept this type.
>
> **CLS (extender):** There is no need to provide syntax to define this type or to extend interfaces or classes that use this type.
>
> **CLS (framework)**: This type shall not appear in exported members.
>
> *end note*]

##### I.8.6.1.4 Parameter signatures

A **parameter signature**, defines constraints on how an individual value is passed as part of a method invocation. Parameter signatures are declared by method definitions. Any valid local signature is a valid parameter signature.

##### I.8.6.1.5 Method signatures

A **method signature** is composed of

- a calling convention,
- the number of generic parameters, if the method is generic,
- if the calling convention specifies this is an instance method and the owning method definition belongs to a type T then the type of the `this` pointer is:
  - given by the first parameter signature, if the calling convention is `instance explicit` (§II.15.3),
  - inferred as &T, if T is a value type and the method definition is non-virtual (§I.8.9.7),
  - inferred as "boxed" T, if T is a value type and the method definition is virtual (this includes method definitions from an interface implemented by T) (§I.8.9.7),
  - inferred as T, otherwise
- a list of zero or more parameter signatures—one for each parameter of the method — and,
- a type signature for the result value, if one is produced.

Method signatures are declared by method definitions. Only one constraint can be added to a method signature in addition to those of parameter signatures:

- The **vararg** constraint can be included to indicate that all arguments past this point are optional. When it appears, the calling convention shall be one that supports variable argument lists.

Method signatures are used in two different ways: as part of a method definition and as a description of a calling site when calling through a function pointer. In the latter case, the method signature indicates

- the calling convention (which can include platform-specific calling conventions),
- the types of all the argument values that are being passed, and
- if needed, a vararg marker indicating where the fixed parameter list ends and the variable parameter list begins.

When used as part of a method definition, the vararg constraint is represented by the choice of calling convention.

[*Note*: a single *method implementation* may be used both to satisfy a *method definition* of a type and to satisfy a *method definition* of an interface the type implements. If the type is a value type, T, then the `this` pointer in the method signature for the type’s own *method definition* is a managed pointer &T, while it is "boxed" T in the method signature associated with the interface’s *method definition*. *end note*]

[*Note*: the presence of a `this` pointer affects parameter signature/argument number pairing in CIL. If the parameter signature for the `this` pointer is inferred then the first parameter signature in the metadata is for argument number one. If there is no `this` pointer, as with static methods, or this is an `instance explicit` method, then the first parameter signature is for argument number zero. See the descriptions of the call and load function instructions in Partition III. *end note*]

> **CLS Rule 15:** The vararg constraint is not part of the CLS, and the only calling convention supported by the CLS is the standard managed calling convention.
>
> [*Note*:
>
> **CLS (consumer):** There is no need to accept methods with variable argument lists or unmanaged calling convention.
>
> **CLS (extender):** There is no need to provide syntax to declare vararg methods or unmanaged calling conventions.
>
> **CLS (framework)**: Neither vararg methods nor methods with unmanaged calling conventions shall be exported externally.
>
> *end note*]

##### I.8.6.1.6 Signature Matching

For signatures other than method signatures two signatures are said to *match* if and only if every component type of the signature is identical in the two signatures.

Method signature matching is used when determining *hiding* and *overriding* (§I.8.10.2, §I.8.10.4). Two method signatures are said to *match* if and only if:

- the calling conventions are identical;
- both signatures are either static or instance;
- the number of generic parameters is identical, if the method is generic;
- for instance signatures the type of the `this` pointer of the overriding/hiding signature is *assignable-to* (§I.8.7) the type of the `this` pointer of the overridden/hidden signature;
- the number and type signatures of the parameters are identical; and
- the type signatures for the result are identical. [*Note*: This includes void (§II.23.2.11) if no value is returned. *end note*]

[*Note*: when overriding/hiding the accessibility of items need not be identical (§I.8.10.2, §I.8.10.4). *end note*]

### I.8.7 Assignment compatibility
TODO

### I.8.8 Type safety and verification
TODO

### I.8.9 Type definers
TODO

## I.9 Metadata
> **This clause and its subclauses contain only informative text, with the exception of the CLS rules introduced here and repeated in §I.11. The metadata format is specified in §Partition II Metdata – File Format**

New types—value types and reference types—are introduced into the CTS via type declarations expressed in **metadata**. In addition, metadata is a structured way to represent all information that the CLI uses to locate and load classes, lay out instances in memory, resolve method invocations, translate CIL to native code, enforce security, and set up runtime context boundaries. Every CLI PE/COFF module (see §Partition II Metadata – File Format) carries a compact metadata binary that is emitted into the module by the CLI-enabled development tool or compiler.

Each CLI-enabled language will expose a language-appropriate syntax for declaring types and members and for annotating them with attributes that express which services they require of the infrastructure. Type imports are also handled in a language-appropriate way, and it is the development tool or compiler that consumes the metadata to expose the types that the developer sees.

Note that the typical component or application developer will not need to be aware of the rules for emitting and consuming CLI metadata. While it can help a developer to understand the structure of metadata, the rules outlined in this clause are primarily of interest to tool builders and compiler writers.

### I.9.1 Components and assemblies
Each CLI component carries the metadata for declarations, implementations, and references specific to that component. Therefore, the component-specific metadata is referred to as **component metadata**, and the resulting component is said to be **self-describing**. In object models such as COM or CORBA, this information is represented by a combination of typelibs, IDL files, DLLRegisterServer, and a myriad of custom files in disparate formats and separate from the actual executable file. In contrast, the metadata is a fundamental part of a CLI component.

Collections of CLI components and other files are packaged together for deployment into **assemblies**, discussed in more detail in a later subclause. An assembly is a logical unit of functionality that serves as the primary unit of reuse in the CLI. Assemblies establish a name scope for types.

Types declared and implemented in individual components are exported for use by other implementations via the assembly in which the component participates. All references to a type are scoped by the identity of the assembly in whose context the type is being used. The CLI provides services to locate a referenced assembly and request resolution of the type reference. It is this mechanism that provides an isolation scope for applications: the assembly alone controls its composition.

### I.9.2 Accessing metadata
Metadata is emitted into and read from a CLI module using either direct access to the file format as described in §Partition II Metadata – File Format or through the Reflection library. It is possible to create a tool that verifies a CLI module, including the metadata, during development, based on the specifications supplied in §Partition II and §Partition III.

When a class is loaded at runtime, the CLI loader imports the metadata into its own in-memory data structures, which can be browsed via the CLI Reflection services. The Reflection services should be considered as similar to a compiler; they automatically walk the inheritance hierarchy to obtain information about inherited methods and fields, they have rules about hiding by name or name-and-signature, rules about inheritance of methods and properties, and so forth.

#### I.9.2.1 Metadata tokens
A metadata token is an implementation-dependent encoding mechanism. §Partition II describes the manner in which metadata tokens are embedded in various sections of a CLI PE/COFF module. Metadata tokens are embedded in CIL and native code to encode method invocations and field accesses at call sites; the token is used by various infrastructure services to retrieve information from metadata about the reference and the type on which it was scoped in order to resolve the reference.

A metadata token is a typed identifier of a metadata object (such as type declaration and member declaration). Given a token, its type can be determined and it is possible to retrieve the specific metadata attributes for that metadata object. However, a metadata token is not a persistent identifier. Rather it is scoped to a specific metadata binary. A metadata token is represented as an index into a metadata data structure, so access is fast and direct.

#### I.9.2.2 Member signatures in metadata
Every location—including fields, parameters, method return values, and properties—has a type, and a specification for its type is carried in metadata.

A value type describes values that are represented as a sequence of bits. A reference type describes values that are represented as the location of a sequence of bits. The CLI provides an explicit set of built-in types, each of which has a default runtime form as either a value type or a reference type. The metadata APIs can be used to declare additional types, and part of the type specification of a variable encodes the identity of the type as well as which form (value or reference) the type is to take at runtime.

Metadata tokens representing encoded types are passed to CIL instructions that accept a type (`newobj, newarray, ldtoken`). (See the CIL instruction set specification in §Partition III.)

These encoded type metadata tokens are also embedded in member signatures. To optimize runtime binding of field accesses and method invocations, the type and location signatures associated with fields and methods are encoded into member signatures in metadata. A member signature embodies all of the contract information that is used to decide whether a reference to a member succeeds or fails.

### I.9.3 Unmanaged code
It is possible to pass data from CLI managed code to unmanaged code. This always involves a transition from managed to unmanaged code, which has some runtime cost, but data can often be transferred without copying. When data must be reformatted the VES provides a reasonable specification of default behavior, but it is possible to use metadata to explicitly require other forms of **marshalling** (i.e., reformatted copying). The metadata also allows access to unmanaged methods through implementation-specific pre-existing mechanisms.

### I.9.4 Method implementation metadata
For each method for which an implementation is supplied in the current CLI module, the tool or compiler will emit information used by the CIL-to-native code compilers, the CLI loader, and other infrastructure services. This information includes:
- Whether the code is managed or unmanaged.
- Whether the implementation is in native code or CIL (note that all CIL code is managed).
- The location of the method body in the current module, as an address relative to the start of the module file in which it is located (a **Relative Virtual Address**, or **RVA**). Or, alternatively, the RVA is encoded as 0 and other metadata is used to tell the infrastructure where the method implementation will be found, including:
  - A method implementation to be located by implementation-specific means described outside this Standard.
  - Forwarding calls through an imported global static method.

### 1.9.5 Class Layout
In the general case, the CLI loader is free to lay out the instances of a class in any way it chooses, consistent with the rules of the CTS. However, there are times when a tool or compiler needs more control over the layout. In the metadata, a class is marked with an attribute indicating whether its layout rule is:
- **autolayout:** A class marked `autolayout` indicates that the loader is free to lay out the class in any way it sees fit; any layout information that might have been specified is ignored. This is the default.
- **sequentiallayout:** A class marked `sequentiallayout` guides the loader to preserve field order as emitted, but otherwise the specific offsets are calculated based on the CLI type of the field; these can be shifted by explicit offset, padding, and/or alignment information.
- **explicitlayout:** A class marked `explicitlayout` causes the loader to ignore field sequence and to use the explicit layout rules provided, in the form of field offsets and/or overall class size or alignment. There are restrictions on valid layouts, specified in §Partition II.

It is also possible to specify an overall size for a class. This enables a tool or compiler to emit a value type specification where only the size of the type is supplied. This is useful in declaring CLI built-in types (such as 32-bit integer). It is also useful in situations where the data type of a member of a structured value type does not have a representation in CLI metadata (e.g., C++ bit fields). In the latter case, as long as the tool or compiler controls the layout, and CLI doesn’t need to know the details or play a role in the layout, this is sufficient. Note that this means that the VES can move bits around but can’t marshal across machines – the emitting tool or compiler will need to handle the marshaling.

Optionally, a developer can specify a packing size for a class. This is layout information that is not often used, but it allows a developer to control the alignment of the fields. It is not an alignment specification, per se, but rather serves as a modifier that places a ceiling on all alignments. Typical values are 1, 2, 4, 8, or 16. Generic types shall not be marked `explicitlayout`.

For the full specification of class layout attributes, see the classes in `System.Runtime.InteropServices` in §Partition IV.

### I.9.6 Assemblies: name scopes for types
An assembly is a collection of resources that are built to work together to deliver a cohesive set of functionality. An assembly carries all of the rules necessary to ensure that cohesion. It is the unit of access to resources in the CLI.

Externally, an assembly is a collection of exported resources, including types. Resources are exported by name. Internally, an assembly is a collection of public (exported) and private (internal to the assembly) resources. It is the assembly that determines which resources are to be exported outside of the assembly and which resources are accessible only within the current assembly scope. It is the assembly that controls how a reference to a resource, public or private, is mapped onto the bits that implement the resource. For types in particular, the assembly can also supply runtime configuration information. A CLI module can be thought of as a packaging of type declarations and implementations, where the packaging decisions can change under the covers without affecting clients of the assembly.

The identity of a type is its assembly scope and its declared name. A type defined identically in two different assemblies is considered two different types.

**Assembly Dependencies:** An assembly can depend on other assemblies. This happens when implementations in the scope of one assembly reference resources that are scoped in or owned by another assembly.

- All references to other assemblies are resolved under the control of the current assembly scope. This gives an assembly an opportunity to control how a reference to another assembly is mapped onto a particular version (or other characteristic) of that referenced assembly (although that target assembly has sole control over how the referenced resource is resolved to an implementation).
- It is always possible to determine which assembly scope a particular implementation is running in. All requests originating from that assembly scope are resolved relative to that scope.

From a deployment perspective, an assembly can be deployed by itself, with the assumption that any other referenced assemblies will be available in the deployed environment. Or, it can be deployed with its dependent assemblies.

**Manifests:** Every assembly has a manifest that declares which files make up the assembly, what types are exported, and what other assemblies are required to resolve type references within the assembly. Just as CLI components are self-describing via metadata in the CLI component, so are assemblies self-describing via their manifests. When a single file makes up an assembly it contains both the metadata describing the types defined in the assembly and the metadata describing the assembly itself. When an assembly contains more than one file with metadata, each of the files describes the types defined in the file, if any, and one of these files also contains the metadata describing the assembly (including the names of the other files, their cryptographic hashes, and the types they export outside of the assembly).

**Applications:** Assemblies introduce isolation semantics for applications. An application is simply an assembly that has an external entry point that triggers (or causes a hosting environment such as a browser to trigger) the creation of a new **application domain**. This entry point is effectively the root of a tree of request invocations and resolutions. Some applications are a single, self-contained assembly. Others require the availability of other assemblies to provide needed resources. In either case, when a request is resolved to a module to load, the module is loaded into the same application domain from which the request originated. It is possible to monitor or stop an application via the application domain.

**References:** A reference to a type always qualifies a type name with the assembly scope within which the reference is to be resolved; that is, an assembly establishes the name scope of available resources. However, rather than establishing relationships between individual modules and referenced assemblies, every reference is resolved through the current assembly. This allows each assembly to have absolute control over how references are resolved. See §Partition II.

### I.9.7 Metadata extensibility
CLI metadata is extensible. There are three reasons this is important:
- The CLS is a specification for conventions that languages and tools agree to support in a uniform way for better language integration. The CLS constrains parts of the CTS model, and the CLS introduces higher-level abstractions that are layered over the CTS. It is important that the metadata be able to capture these sorts of development - time abstractions that are used by tools even though they are not recognized or supported explicitly by the CLI.
- It should be possible to represent language-specific abstractions in metadata that are neither CLI nor CLS language abstractions. For example, it should be possible, over time, to enable languages like C++ to not require separate headers or IDL files in order to use types, methods, and data members exported by compiled modules.
- It should be possible, in member signatures, to encode types and type modifiers that are used in language-specific overloading. For example, to allow C++ to distinguish **int** from **long** even on 32-bit machines where both map to the underlying type **int32**.

This extensibility comes in the following forms:
- Every metadata object can carry custom attributes, and the metadata APIs provide a way to declare, enumerate, and retrieve custom attributes. Custom attributes can be identified by a simple name, where the value encoding is opaque and known only to the specific tool, language, or service that defined it. Or, custom attributes can be identified by a type reference, where the structure of the attribute is self-describing (via data members declared on the type) and any tool including the CLI reflection services can browse the value encoding.

> **CLS Rule 34:** The CLS only allows a subset of the encodings of custom attributes. The only types that shall appear in these encodings are (see §Partition IV): `System.Type`, `System.String`, `System.Char`, `System.Boolean`, `System.Byte`, `System.Int16`, `System.Int32`, `System.Int64`, `System.Single`, `System.Double`, and any enumeration type based on a CLS-compliant base integer type.
>
> [*Note*:
>
> **CLS (consumer):**: Shall be able to read attributes encoded using the restricted scheme.
>
> **CLS (extender):** Must meet all requirements for CLS consumer and be able to author new classes and new attributes. Shall be able to attach attributes based on existing attribute classes to any metadata that is emitted. Shall implement the rules for the `System.AttributeUsageAttribute` (see §Partition IV).
>
> **CLS (framework):** Shall externally expose only attributes that are encoded within the CLS rules and following the conventions specified for `System.AttributeUsageAttribute`. 
>
> *end note*]

- In addition to CTS type extensibility, it is possible to emit custom modifiers into member signatures (see Types in §Partition II). The CLI will honor these modifiers for purposes of method overloading and hiding, as well as for binding, but will not enforce any of the language-specific semantics. These modifiers can reference the return type or any parameter of a method, or the type of a field. They come in two kinds: **required modifiers** that anyone using the member must understand in order to correctly use it, and **optional modifiers** that can be ignored if the modifier is not understood.

> **CLS Rule 35:** The CLS does not allow publicly visible required modifiers (**modreq**, see §Partition II), but does allow optional modifiers (**modopt**, see §Partition II) it does not understand.
>
> [*Note*:
>
> **CLS (consumer):** Shall be able to read metadata containing optional modifiers and correctly copy signatures that include them. Can ignore these modifiers in type matching and overload resolution. Can ignore types that become ambiguous when the optional modifiers are ignored, or that use required modifiers.
>
> **CLS (extender):** Shall be able to author overrides for inherited methods with signatures that include optional modifiers. Consequently, an extender must be able to copy such modifiers from metadata that it imports. There is no requirement to support required modifiers, nor to author new methods that have any kind of modifier in their signature.
>
> **CLS (framework):** Shall not use required modifiers in externally visible signatures unless they are marked as not CLS-compliant. Shall not expose two members on a class that differ only by the use of optional modifiers in their signature, unless only one is marked CLS-compliant.
>
> *end note*]

### I.9.8 Globals, imports and exports
The CTS does not have the notion of **global statics**: all statics are associated with a particular class. Nonetheless, the metadata is designed to support languages that rely on static data that is stored directly in a PE/COFF file and accessed by its relative virtual address. In addition, while access to managed data and managed functions is mediated entirely through the metadata itself, the metadata provides a mechanism for accessing unmanaged data and unmanaged code.

> **CLS Rule 36:** Global static fields and methods are not CLS-compliant.
>
> [*Note*:
>
> **CLS (consumer):** Need not support global static fields or methods.
>
> **CLS (extender):** Need not author global static fields or methods.
>
> **CLS (framework):** Shall not define global static fields or methods. 
>
> *end note*]
 
### I.9.9 Scoped statics
The CTS does not include a model for file- or function-scoped static functions or data members. However, there are times when a compiler needs a metadata token to emit into CIL for a scoped function or data member. The metadata allows members to be marked so that they are never visible or accessible outside of the PE/COFF file in which they are declared and for which the compiler guarantees to enforce all access rules.

> **End informative text**

## I.10 Name and type rules for the Common Language Specification
### I.10.1 Identifiers
Languages that are either case-sensitive or case-insensitive can support the CLS. Since its rules apply only to items exported to other languages, **private** members or types that aren’t exported from an assembly can use any names they choose. For interoperation, however, there are some restrictions.

In order to make tools work well with a case-sensitive language it is important that the exact case of identifiers be maintained. At the same time, when dealing with non-English languages encoded in Unicode, there might be more than one way to represent precisely the same identifier that includes combining characters. The CLS requires that identifiers obey the restrictions of the appropriate Unicode standard and they are persisted in Canonical form C, which preserves case but forces combining characters into a standard representation. See CLS Rule 4, in §I.8.5.1.

At the same time, it is important that externally visible names not conflict with one another when used from a case-insensitive programming language. As a result, all identifier comparisons shall be done internally to CLS-compliant tools using the Canonical form KC, which first transforms characters to their case-canonical representation. See CLS Rule 4, in §I.8.5.1.

### I.1.0.2 Overloading

[*Note*: Although the CTS describes inheritance, object layout, name hiding, and overriding of virtual methods, it does not discuss overloading at all. While this is surprising, it arises from the fact that overloading is entirely handled by compilers that target the CTS and not the type system itself. In the metadata, all references to types and type members are fully resolved and include the precise signature that is intended. This choice was made since every programming language has its own set of rules for coercing types and the VES does not provide a means for expressing those rules. *end note*]

Following the rules of the CTS, it is possible for duplicate names to be defined in the same scope as long as they differ in either kind (field, method, etc.) or signature. The CLS imposes a stronger restriction for overloading methods. Within a single scope, a given name can refer to any number of methods provided they differ in any of the following:
 - Number of parameters
 - Type of any parameter

Notice that the signature includes more information, but CLS-compliant languages need not produce or consume classes that differ only by that additional information (see §Partition II for the complete list of information carried in a signature):
- Calling convention
- Custom modifiers
- Return type
- Whether a parameter is passed by value or by reference

There is one exception to this rule. For the special names `op_Implicit` and `op_Explicit`, described in §I.10.3.3, methods can be provided that differ only by their return type. These are marked specially and can be ignored by compilers that don’t support operator overloading.

Properties shall not be overloaded by type (that is, by the return type of their getter method), but they can be overloaded with different number or types of indices (that is, by the number and types of the parameters of their **getter** methods). The overloading rules for properties are identical to the method overloading rules.

> **CLS Rule 37:** Only properties and methods can be overloaded.
> **CLS Rule 38:** Properties and methods can be overloaded based only on the number and types of their parameters, except the conversion operators named `op_Implicit` and `op_Explicit`, which can also be overloaded based on their return type.
>
> [*Note*:
>
> **CLS (consumer):** Can assume that only properties and methods are overloaded, and need not support overloading based on return type unless providing special syntax for operator overloading. If return type overloading isn’t supported, then the `op_Implicit` and `op_Explicit` can be ignored since the functionality shall be provided in some other way by a CLS-compliant framework. Consumers must first apply the hide-by-name and hide-by-signature-and-name rules (§I.8.10.4) to avoid any ambiguity.
>
> **CLS (extender):** Should not permit the authoring of overloads other than those specified here. It is not necessary to support operator overloading at all, hence it is possible to entirely avoid support for overloading on return type.
>
> **CLS (framework)**: Shall not publicly expose overloading except as specified here. Frameworks authors should bear in mind that many programming languages, including object-oriented languages, do not support overloading and will expose overloaded methods or properties through mangled names. Most languages support neither operator overloading nor overloading based on return type, so `op_Implicit` and `op_Explicit` shall always be augmented with some alternative way to gain the same functionality.
> 
> end note*]

[*Note*: The names visible on any class `C`, are the names visible in that class and its base classes. As a consequence, the names of methods on interfaces implemented by `C` that are only implemented via MethodImpls (see §Partition II) are not visible on class `C`. The names visible on an interface `I`, consist only of the names directly defined on this interface. As a consequence, the names of methods from other interfaces (which `I` requires be implemented) are not visible on `I` itself *end note*]

### I.10.3 Operator overloading
CLS-compliant consumer and extender tools are under no obligation to allow defining of operator overloading. CLS-compliant consumer and extender tools do not have to provide a special mechanism to call these methods.

[*Note*: This topic is addressed by the CLS so that
 - languages that do provide operator overloading can describe their rules in a way that other languages can understand, and
 - languages that do not provide operator overloading can still access the underlying functionality without the addition of special syntax.
 
 *end note*]

Operator overloading is described by using the names specified below, and by setting a special bit in the metadata (**SpecialName**) so that they do not collide with the user’s name space. A CLS-compliant producer tool shall provide some means for setting this bit. If these names are used, they shall have precisely the semantics described here.

##### I.10.3.1 Unary operators
Unary operators take one operand, perform some operation on it, and return the result. They are represented as static methods on the class that defines the type of their one operand. §Table I.4: Unary Operator Names shows the names that are defined.

##### Table I.4: Unary Operator Names
| **Name** | **ISO/IEC 14882:2003 C++ Operator Symbol** (This column is informative) |
| ---------- | ---------- |
| `op_Decrement` | Similar to `--` <sup>1</sup> |
| `op_Increment` | Similar to `++` <sup>1</sup> |
| `op_UnaryNegation` | Similar to `-` (unary) |
| `op_UnaryPlus` | Similar to `+` (unary) |
| `op_LogicalNot` | `!` |
| `op_True`<sup>2</sup> | Not defined |
| `op_False`<sup>2</sup> | Not defined |
| `op_AddressOf` | `&` (unary) |
| `op_OnesComplement` | `~` |
| `op_PointerDereference` | `*` (unary) |

<sup>1</sup> From a pure C++ point of view, the way one must write these functions for the CLI differs in one very important aspect. In C++, these methods must increment or decrement their operand directly, whereas, in CLI, they must not; instead, they simply return the value of their operand +/- 1, as appropriate, without modifying their operand. The operand must be incremented or decremented by the compiler that generates the code for the ++/-- operator, separate from the call to these methods.

<sup>2</sup> The `op_True` and `op_False` operators do not exist in C++. They are provided to support tristate Boolean types, such as those used in database languages.

##### I.10.3.2 Binary operators
Binary operators take two operands, perform some operation on them, and return a value. They are represented as static methods on the class that defines the type of one of their two operands. §Table I.5: Binary Operator Names shows the names that are defined.
##### Table I.5: Unary Operator Names
| **Name** | **ISO/IEC 14882:2003 C++ Operator Symbol** (This column is informative) |
| ---------- | ---------- |
| `op_Addition` | `+` (binary) |
| `op_Substraction` | `-` (binary) |
| `op_Multiply` | `*` (binary) |
| `op_Division` | `/` |
| `op_Modulus` | `%` |
| `op_ExclusiveOr` | `^` |
| `op_BitwiseAnd` | `&` (binary) |
| `op_BitwiseOr` | `|` |
| `op_LogicalAnd` | `&&` |
| `op_LogicalOr` | `||` |
| `op_Assign` | Not defined (= is not the same) |
| `op_LeftShift` | `<<` |
| `op_RightShift` | `>>` |
| `op_SignedRightShift` | Not defined |
| `op_UnsignedRightShift` | Not defined |
| `op_Equality` | `==` |
| `op_GreaterThan` | `>` |
| `op_LessThan` | `<` |
| `op_Inequality` | `!=` |
| `op_GreaterThanOrEqual` | `>=` |
| `op_LessThanOrEqual` | `<=` |
| `op_UnsignedRightShiftAssignment` | Not defined |
| `op_MemberSelection` | `->` |
| `op_RightShiftAssignment` | `>>=` |
| `op_MultiplicationAssignment` | `*=` |
| `op_PointerToMemberSelection` | `->*` |
| `op_SubtractionAssignment` | `-=` |
| `op_ExclusiveOrAssignment` | `^=` |
| `op_LeftShiftAssigment` | `<<=` |
| `op_ModulusAssignment` | `%=` |
| `op_AdditionAssignment` | `+=` |
| `op_BitwiseAndAssignment` | `&=` |
| `op_BitwiseOrAssignment` | `|=` |
| `op_Comma` | `,` |
| `op_DivisionAssignment` | `/=` |

#### I.10.3.3 Conversion operators
Conversion operators are unary operations that allow conversion from one type to another. The operator method shall be defined as a static method on either the operand or return type. There are two types of conversions:
- An implicit (**widening**) coercion shall not lose any magnitude or precision. These should be provided using a method named `op_Implicit`.
- An explicit (**narrowing**) coercion can lose magnitude or precision. These should be provided using a method named `çop_Explicit`.

[*Note*:  Conversions provide functionality that can’t be generated in other ways, and many languages do not support the use of the conversion operators through special syntax. Therefore, CLS rules require that the same functionality be made available through an alternate mechanism. It is recommended that the more common ToXxx (where Xxx is the target type) and FromYyy (where Yyy is the name of the source type) naming pattern be used.
 *end note*]

Because these operations can exist on the class of their operand type (so-called “from” conversions) and would therefore differ on their return type only, the CLS specifically allows that these two operators be overloaded based on their return type. The CLS, however, also requires that if this form of overloading is used then the language shall provide an alternate means for providing the same functionality since not all CLS languages will implement operators with special syntax.

> **CLS Rule 39:** If either `op_Implicit` or `op_Explicit` is provided, an alternate means of providing the coercion shall be provided.
>
> [*Note*:
>
> **CLS (consumer):** Where appropriate to the language design, use the existence of `op_Implicit` and/or `op_Explicit` in choosing method overloads and generating automatic coercions.
>
> **CLS (extender):** Where appropriate to the language design, implement user-defined implicit or explicit coercion operators using the corresponding `op_Implicit`, `op_Explicit`, `ToXxx`, and/or `FromXxx` methods.
>
> **CLS (framework)**: If coercion operations are supported, they shall be provided as `FromXxx` and `ToXxx`, and optionally `op_Implicit` and `op_Explicit` as well. CLS frameworks are encouraged to provide such coercion operations.
> 
> end note*]

### I.10.4 Naming patterns

See also §Partition VI. While the CTS does not dictate the naming of properties or events, the CLS does specify a pattern to be observed. For Events:
An individual event is created by choosing or defining a delegate type that is used to indicate the event. Then, three methods are created with names based on the name of the event and with a fixed signature. For the examples below we define an event named `Click` that uses a delegate type named `EventHandler`.

```
EventAdd, used to add a handler for an event
	Pattern: void add_<EventName> (<DelegateType> handler)
	Example: viod add_Click (EventHandler handler);
EventRemove, used to remove a handler for an event
	Pattern: void remove_<EventName> (<DelegateType> handler)
	Example: void remove_Click (EventHandler handler)
EventRaise, used to indicate than an event has occurred
	Patter: void family raise_<EventName> (Event e)
```

For Properties: 
An individual property is created by deciding on the type returned by its getter method and the types of the getter’s parameters (if any). Then, two methods are created with names based on the name of the property and these types. For the examples below we define two properties: Name takes no parameters and returns a `System.String`, while `Item` takes a `System.Object` parameter and returns a `System.Object`. Item is referred to as an indexed property, meaning that it takes parameters and thus can appear to the user as through it were an array with indices.

```
PropertyGet, used to read the value of the property
	Pattern: <PropType> get_<PropName> (<Indices>)
	Example: System.String getName ();
	Example: System.Object getItem (System.Object key);
PropertySet, used to modify the value of the property
	Pattern: void set_<PropName> (<Indices>, <PropType>)
	Example: void set_Name (System.String name)
	Example: void set_Item (System.Object key, System.Object value);
```

### I.10.5 Exceptions
The CLI supports an exception handling model, which is introduced in §I.12.4.2. CLScompliant frameworks can define and throw externally visible exceptions, but there are restrictions on the type of objects thrown:

> **CLS Rule 40:** Objects that are thrown shall be of type `System.Exception` or a type inheriting from it. Nonetheless, CLS-compliant methods are not required to block the propagation of other types of exceptions.
>
> [*Note*:
>
> **CLS (consumer):** Need not support throwing or catching of objects that are not of the specified type.
>
> **CLS (extender):** Must support throwing of objects of type `System.Exception` or a type inheriting from it. Need not support the throwing of objects having other types.
>
> **CLS (framework)**: Shall not publicly expose thrown objects that are not of type `System.Exception` or a type inheriting from it.
> 
> end note*]

### I.10.6 Custom attributes
In order to allow languages to provide a consistent view of custom attributes across language boundaries, the Base Class Library provides support for the following rule defined by the CLS:

> **CLS Rule 41:** Attributes shall be of type System.Attribute, or a type inheriting from it.
>
> [*Note*:
>
> **CLS (consumer):** Need not support attributes that are not of the specified type.
>
> **CLS (extender):** Must support the authoring of custom attributes.
>
> **CLS (framework)**: Shall not publicly expose attributes that are not of type `System.Attribute` or a type inheriting from it. 
> 
> end note*]

The use of a particular attribute class can be restricted in various ways by placing an attribute on the attribute class. The `System.AttributeUsageAttribute` is used to specify these restrictions. The restrictions supported by the `System.AttributeUsageAttribute` are:

- What kinds of constructs (types, methods, assemblies, etc.) can have the attribute applied to them. By default, instances of an attribute class can be applied to any construct. This is specified by setting the value of the `ValidOn` property of `System.AttributeUsageAttribute`. Several constructs can be combined.
- Multiple instances of the attribute class can be applied to a given piece of metadata. By default, only one instance of any given attribute class can be applied to a single metadata item. The `AllowMultiple` property of the attribute is used to specify the desired value.
- Do not inherit the attribute when applied to a type. By default, any attribute attached to a type should be inherited to types that derive from it. If multiple instances of the attribute class are allowed, the inheritance performs a union of the attributes inherited from the base class and those explicitly applied to the derived class type. If multiple instances are not allowed, then an attribute of that type applied directly to the derived class overrides the attribute supplied by the base class. This is specified by setting the `Inherited` property of `System.AttributeUsageAttribute` to the desired value.

[*Note*:  Since these are CLS rules and not part of the CTS itself, tools are required to specify explicitly the custom attributes they intend to apply to any given metadata item. That is, compilers or other tools that generate metadata must implement the AllowMultiple and Inherit rules. The CLI does not supply attributes automatically. The usage of attributes in the CLI is further described in §Partition II.
 *end note*]

### I.10.7 Generic types and methods
The following subclauses describe the CLS rules for generic types and methods.

#### I.10.7.1 Nested type parameter re-declaration
Any type exported by a CLS-compliant framework, that is nested in a generic type, itself declares, by position, all the generic parameters of that enclosing type. (The nested type can also introduce new generic parameters.) As such, any CLS-compliant type nested inside a generic type is itself generic. Such redeclared generic parameters shall precede any newly introduced generic parameters. [*Example:* Consider the following C# source code:

````cs
public class A<T> {
	public class B {}
	public class C<U, V> {
		public class D<W> {}
	}
}
public class X {
	public class Y<T> {}
}
````

The relevant corresponding ILAsm code is:
```
.class ... A`1<T> ... {							  // T is introduced
	.class ... nested ... B<T> ... { }			  // T is redeclared
	.class ... nested ... C'2<T,U,V> ... {		  // T is redeclared; U and V are introduced
		...class nested ... D`1<T,U,V,W> ... { }  // T, U, and V are redeclared; W is introduced
	}
}
.class ... X ... {
	.class ... nested Y`1<T> ... { }			  // Nothing is redeclared; T is introduced
}
```
As generic parameter re-declaration is based on parameter position matching, not on parameter name matching, the name of a redeclared generic parameter need not be the same as the one it redeclares. For example:
```
.class ... A`<T> ... {							  // T is introduced
	.class ... nested ... B<Q> { }				  // T is redeclared (as Q)
	.class ... nested ... C`2<T1,U,V> ... {		  // T is redeclared (as T1); U and V are introduced
	.class ... nested ... D`1<R1,R2,R3,W> ... { } // T1, U, and V are redeclared (as R1, R2, and R3); W is introduced
	}
}
```
A CLS-compliant Framework should therefore expose the following types:
| **Lexical Name**| **Total Generic Parameters** | **Redeclared Generic Parameters** | **Introduced Generic Parameters** |
| ---------- | ---------- | ---------- | ---------- |
| `A<T>` | `1 (T)` | `0` | `1 T` |
| `A<T>.B` | `1 (T)` | `1 T` | `0` |
| `A<T>.C<U,V>` | `3 (T,U,V)` | `1 T` | `2 U,V` |
| `A<T>.C<U,V>.D<W>` | `4 (T,U,V,W)` | `3 T,U,V` | `1 W` |
| `X` | `0` | `0` | `0` |
| `X.Y<T>` | `1 (T)` | `0` | `1 T` |

*end example*]

> **CLS Rule 42:** Nested types shall have at least as many generic parameters as the enclosing type. Generic parameters in a nested type correspond by position to the generic parameters in its enclosing type.
>
> [*Note*:
>
> **CLS (consumer):** Need not consume types that violate this rule.
>
> **CLS (extender):** Same as consumers. Extenders choosing to support definition of types nested in generic types shall follow this rule for externally visible types.
>
> **CLS (framework)**: Shall not expose types that violate this rule.
> 
> end note*]

#### I.10.7.2 Type names and arity encoding
CLS-compliant generic type names are encoded using the format “name[\`arity]” , where […] indicates that the grave accent character “\`” and arity together are optional. The encoded name shall follow these rules:

 1. *name* shall be an *ID* (see §Partition II) that does not contain the “\`” character.
 2. *arity* is specified as an unsigned decimal number without leading zeros or spaces.
 3. For a normal generic type, *arity* is the number of type parameters declared on the type.
 4. For a nested generic type, *airty* is the number of newly introduced type parameters.
 
 [*Example:* Consider the following C# source code:
 ```cs
 public class A<T> {
	 public class B {}
	 public class C<,U,V> {
	 public class D<W> {}
	}
}
public class X {
	public class Y<T> {}
}
```
The relevant corresponding ILAsm code is:
```cs
.class ... A`1<T> ... {						    	// T is introduced
	.class ... nested ... B<T> ... { }				// T is redeclared
	.class ... nested ... C`2<T,U,V> ... {			// T is redeclared; U and V are introduced
		.class ... nested ... D`1<T,U,V,W> ... { }	// T, U, and V are redeclared; W is introduced
	}
}
.class ... X ... {
	.class ... nested Y`1<T> ... { }				// Nothing is redeclared; T is introduced
}
```

A CLS-compliant Framework should expose the following types:
| **Lexical Name**| **Total Generic Parameters** | **Redeclared Generic Parameters** | **Introduced Generic Parameters** | **Metadata Encoding**
| ---------- | ---------- | ---------- | ---------- | ---------- |
| `A<T>` | `1 (T)` | `0` | `1 T` | ```A`1``` |
| `A<T>.B` | `1 (T)` | `1 T` | `0` | `B` |
| `A<T>.C<U,V>` | `3 (T,U,V)` | `1 T` | `2 U,V` | ```C`2``` |
| `A<T>.C<U,V>.D<W>` | `4 (T,U,V,W)` | `3 T,U,V` | `1 W` | ```D`1``` |
| `X` | `0` | `0` | `0` | `X` |
| `X.Y<T>` | `1 (T)` | `0` | `1 T` | ```Y`1``` |

While a type name encoded in metadata does not explicitly mention its enclosing type, the CIL and Reflection type name grammars do include this detail:
| **Lexical Name**| **Metadata Encoding** | **CIL** | **Reflection** |
| ---------- | ---------- | ---------- | ----------  |
| `A<T>` | ```A`1``` | ```A`1``` | ```A`1[T]``` |
| `A<T>.B` | `B` | ```A`1/B``` | ```A`1+B[T]``` |
| `A<T>.C<U,V>` | ```C`2``` | ```A`1/C`2``` | ```A`1+C`2[T,U,V]``` |
| `A<T>.C<U,V>.D<W>` | ```D`1``` | ```A`1/C`2/D`1``` | ```A`1+C`2+D`1[T,U,V,W]```  |
| `X` | `X` | `X` | `X` |
| `X.Y<T>` | ```Y`1``` | ```X/Y`1``` | ```X+Y`1[T]``` |

*end example*]

> **CLS Rule 43:** The name of a generic type shall encode the number of type parameters declared on the non-nested type, or newly introduced to the type if nested, according to the rules defined above.
>
> [*Note*:
>
> **CLS (consumer):** Need not consume types that violate this rule.
>
> **CLS (extender):** Same as consumers. Extenders choosing to support definition of generic types shall follow this rule for externally visible types.
>
> **CLS (framework)**: Shall not expose types that violate this rule.
> 
> end note*]

##### I.10.7.3 Type constrain re-declaration
CLS Frameworks shall ensure that a generic type explicitly re-declares any constraints present on generic parameters in its base class and all implemented interfaces. Put another way, CLS Extenders and Consumers should be able to examine just the specific type in question, to determine the set of constraints that need to be satisfied.

> **CLS Rule 44:** A generic type shall redeclare sufficient constraints to guarantee that any constraints on the base type, or interfaces would be satisfied by the generic type constraints.
>
> [*Note*:
>
> **CLS (consumer):** Need not consume types that violate this rule. Consumers who check constraints need only look at the type being instantiated to determine the applicable constraints.
>
> **CLS (extender):** Same as consumers. Extenders choosing to support definition of generic types shall follow this rule.
>
> **CLS (framework)**: Shall not expose types that violate this rule.
> 
> end note*]

#### I.10.7.4 Contraint type restrictions
> **CLS Rule 45:** Types used as constraints on generic parameters shall themselves be CLScompliant.
>
> [*Note*:
>
> **CLS (consumer):** Need not consume types that violate this rule.
>
> **CLS (extender):** Same as consumers. Extenders choosing to support definition of generic types shall follow this rule when checking for CLS compliance, and need not provide syntax to violate this rule.
>
> **CLS (framework)**: Shall not expose types that violate this rule.
> 
> end note*]

##### I.10.7.5 Frameworks and accessibility of nested types
CLI generics treat the generic type declaration and all instantiations of that generic type as having the same accessibility scope. However, language accessibility rules may differ in this regard, with some choosing to follow the CLI accessibility model, while others use a more restrictive, per-instantiation model. To enable consumption by all CLS languages, CLS frameworks shall be designed with a conservative per-instantiation model of accessibility in mind, and not expose nested types or require access to protected members based on specific, alternate instantiations of a generic type.

This has implications for signatures containing nested types with **family** accessibility. Open generic types shall not expose fields or members with signatures containing a specific instantiation of a nested generic type with family accessibility. Non-generic types extending a specific instantiation of a generic base class or interface, shall not expose fields or members with signatures containing a different instantiation of a nested generic type with family accessibility. [*Example*: Consider the following C# source code:
```cs
public class C<T> {
	protected class n {...}
	protected void M1(C<int>.N n) {...}	 // Not CLS-compliant - C<int>.N not accessible from within C<T> in all languages
	protected void M2(C<T>.n n) {...}	 // CLS-compliant - C<T>.N accessible inside C<T>
}

public class D : C<long> {
	protected void M3(C<int>.N n) {...}	 // Not CLS-compliant - C<int>.N is not accessible in D (extends C<long>)
	protected void M4(C<long>.N n) {...} // CLS-compliant, C<long>.N is accessible in D (extends C<long>)
}
```
The relevant corresponding ILASM code is:
```
.class public ... C`1<T> ... {
	.class ... nested N<T> ... {}
	.method family hidebysig instance void M1(class C`1/n<int32> n) {} // Not CLS-compliant - C<int>.N is not accessible from withing C<T> in all languages
	.method family hidebysig instance void M2(class C`1/N<!0> n) ... {} // CLS-compliant - C<T>.N is accessible inside C<T>
}
.class public ... D extends class C`1<int64> {
	.method family hidebysig instance void M3(class C`1/N<int32> n) .. {} // Not CLS-compliant - C<int>.N is not accessible in D (extends C<long>)
	.method family hidebysig instance void M4(class C`1/N<int64> n) ... {} // CLS-compliant, C<long>.N is accessible in D (extends C<long>)
}
```
*end example*]
> **CLS Rule 46:** The visibility and accessibility of members (including nested types) in an instantiated generic type shall be considered to be scoped to the specific instantiation rather than the generic type declaration as a whole. Assuming this, the visibility and accessibility rules of CLS rule 12 still apply.
>
> [*Note*:
>
> **CLS (consumer):** Need not consume types that violate this rule.
>
> **CLS (extender):** Shall use this more restrictive notion of accessibility when determining CLS compliance.
>
> **CLS (framework)**: Shall not expose members that violate this rule.
> 
> end note*]

#### I.10.7.6 Frameworks and abstract or virtual methods
CLS Frameworks shall not expose libraries that require CLS Extenders to override or implement generic methods to use the framework. This does not imply that virtual or abstract generic methods are non-compliant; rather, the framework shall also provide concrete implementations with appropriate default behavior.
> **CLS Rule 47:** For each abstract or virtual generic method, there shall be a default concrete (nonabstract) implementation.
>
> [*Note*:
>
> **CLS (consumer):** No impact.
>
> **CLS (extender):** Need not provide syntax for overriding generic methods.
>
> **CLS (framework)**: Shall not expose generic methods that violate this rule without also providing appropriate concrete implementations.
> 
> end note*]
