# Dirty Builds
TODO: Expand dirty vs clean builds and explain additive style builds in more detail
The ability to additively link or replace routines over time using DBOs, ELBs, and OLBs has historically facilitated quick debugging and streamlined deployment. In more recent times with higher-speed internet connections and more powerful systems, these benefits have ceased to outweigh the considerable downsides.

## Additive library builds

1.  **State accumulation in ELBs**: As ELBs and OLBs evolve with routines being linked in or replaced, they can accumulate states, including outdated configurations or remnants of previous routines. This leftover state can introduce unexpected behaviors or bugs that might not manifest in a cleanly built library.

2.  **Version control difficulties**: While traditional version control systems track changes in source files, the additive model of ELBs might complicate versioning. It can become challenging to discern specific changes between ELB versions or to merge alterations from different developers.

3.  **Dependency challenges**: Over time, as more routines are added or replaced in an ELB, it can be difficult to discern which parts are actively used and which have become obsolete or redundant. While not directly causing memory overhead, these lingering routines can introduce potential conflicts, bugs, or increased complexity.

4.  **Onboarding new developers**: For developers unfamiliar with the DBL environment and the specific history of an additively built library, there can be a learning curve. Comprehending the intricacies and evolution of a library, developed over an extended period, can be challenging.

5.  **Deployment variability**: While some users argue that the DBL system simplifies deployment by allowing for tiny patches, this approach can lead to environments with slightly different library states, especially if patches are applied inconsistently. This variability can make debugging and troubleshooting harder since not all environments are guaranteed to be identical.

Today you can use MSBuild-based projects to get highly controlled, well-structured builds that produce the same artifact for a clean build or an incremental one. Depending on your project structure, this process can actually be even faster than manual additive builds using custom scripts.