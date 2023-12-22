# Following the Trend: An Inventory Project
Time for another project! This time we'll be a routine that collects ordering trends and makes recommendations to restock our inventory. This project is going to make use of a lot of the concepts we've covered so far. You can do all of this using Visual Studio and MSBuild projects, but we're going to do this directly using `dbl`, `dblink` and `rpsutl` to give you a feel for how these tools are working under the hood. If you want to see how things are set up using MSBuild, you can check this books companion repository on GitHub.

## Overview
The GenerateRestockReco subroutine is the core of the recommendation system. It takes in `inventory`, `orders`, and `analysisDate` as inputs and outputs `trends` and `recommendations`. We'll need some fake data and a few helper routines to hold it all together. Let's start by defining the data structures we'll need.

### Required Data Structures

- **InventoryItem**: Represents an item in inventory.
- **Order**: Represents a customer order.
- **Trend**: Used for analyzing sales trends of an item.
- **Restock**: Represents a restock recommendation.

We're going to use the Synergy Data Language to define these structures and build them into a repository. This will give you a chance to work with a brand new repository and really get a feel for how they work. We'll be using the `rpsutl` tool to build the repository and `.include` to bring the structures into our program. Time to dive in!


