# Recommendations
Now that we have our data structures in order, let's move on to the `GenerateRestockReco` subroutine. `GenerateRestockReco` is supposed to analyze inventory and sales data, ultimately providing recommendations for restocking inventory items. We're going to show off our knowlege of collections, control flow, and comparisons. Let's explore this subroutine in detail, understanding its intricacies and the role of each segment in achieving its objective.

### Subroutine signature
```dbl

.include "InventoryItem" REPOSITORY, structure, end
.include "Order" REPOSITORY, structure, end
.include "Trend" REPOSITORY, structure, end
.include "Restock" REPOSITORY, structure, end

subroutine GenerateRestockReco
    in inventory, @ArrayList
    in orders, @ArrayList
    in analysisDate, d8
    out trends, @ArrayList
    out result, @ArrayList
    endparams
    ;;data division records go here
    record
        oneMonth, int
        oneYear, int
        orderTrends, @StringDictionary
proc
    ;; implementation goes here
endsubroutine
```

### Initialization and preparations

```dbl,ignore,does_not_compile
result = new ArrayList()
trends = new ArrayList()

oneMonth = %jperiod(analysisDate) - 30
oneYear = %jperiod(analysisDate) - 365
orderTrends = new StringDictionary()
```
In this initial section, the subroutine sets up necessary variables. Variables `oneMonth` and `oneYear` are calculated to represent the date thresholds for recent and historical data analysis. The %JPERIOD function converts `analysisDate` into a Julian date format, from which 30 and 365 days are subtracted to get dates one month and one year prior, respectively. The variable `orderTrends` is initialized as a `StringDictionary`, which will be used to map item IDs to their corresponding sales trend data.

### Processing orders
```dbl
foreach data targetOrder in orders as @Order
begin
    data orderDate, int, %jperiod(targetOrder.OrderDate)
    data targetTrend, Trend
    init targetTrend
    targetTrend.ItemId = targetOrder.ItemId

    if (orderTrends.Contains(targetOrder.ItemId))
    begin
        targetTrend = (Trend)orderTrends.Get(targetOrder.ItemId)
    end

    if(orderDate >= oneMonth) then
    begin
        targetTrend.ItemCount += targetOrder.Quantity
        targetTrend.OrderCount += 1
    end
    else if(orderDate >= oneYear) then
    begin
        targetTrend.HistoricCount += targetOrder.Quantity
    end
    else
        nextloop

    orderTrends.Set(targetOrder.ItemId, (@*)targetTrend)
end
```
In this block, the subroutine iterates through each order in the `orders` array list. Because ArrayList only understands an untyped `object`, we tell the compiler what the expected type is using the AS syntax. For each order, the routine calculates the Julian date (`orderDate`) and initializes a `Trend` structure (`targetTrend`). This structure is then populated with data depending on whether the order falls within the one-month or one-year threshold. Orders within these thresholds contribute to the item count and order count or the historic count, reflecting recent and historical sales data. The updated trend data for each item is stored back into the `orderTrends` dictionary.

### Analyzing inventory and creating recommendations
```dbl
foreach data targetItem in inventory as @InventoryItem
begin
    if (orderTrends.Contains(targetItem.ItemId))
    begin
        data targetTrend = (Trend)orderTrends.Get(targetItem.ItemId)
        data historicCount, d28.10, targetTrend.HistoricCount
        data recentQuantity, d28.10, targetTrend.ItemCount
        data historicalAverage, d28.10, historicCount / 12.0

        if (recentQuantity > 1.5 * historicalAverage ||
            &   targetItem.Quantity < historicalAverage) 
        begin
            data restockRequest, Restock
            restockRequest.ItemId = targetItem.ItemId
            restockRequest.Quantity = %integer(recentQuantity > historicalAverage ? 
            &   recentQuantity - targetItem.Quantity: 
            &   historicalAverage - targetItem.Quantity)

            if(restockRequest.Quantity > 0)
                result.Add((@*)restockRequest)
        end

        trends.Add((@*)targetTrend)
    end
end
```
This segment iterates over each item in the inventory. For items that have corresponding trend data in `orderTrends`, the subroutine calculates the historical average sales and compares that value with the recent sales quantity. If the recent sales exceed 1.5 times the historical average or if the current inventory is below the historical average, a restock request is created. This request includes the item ID and the calculated restock quantity, which is determined based on whether the recent sales or the historical average is greater. Restock requests with a positive quantity are added to the `result` array list, which contains all recommendations. Additionally, the trend data for each item is added to the `trends` array list for potential further analysis.

### Test data
We're going to need to build some test data to run this routine against. We'll need a few helper routines to accomplish this. Let's start with the `MakeItem` function. This function will take in an item ID, name, and quantity and return an `InventoryItem` structure, which we'll use to populate our inventory.

```dbl
function MakeItem, InventoryItem
    itemId, n
    name, string
    quantity, int
    endparams
    record
        inv, InventoryItem
proc
    inv.ItemId = %string(itemId)
    inv.Name = name
    inv.Quantity = quantity
    freturn inv
endfunction
```

There's not really anything new here; we're just taking in some parameters and returning a structure. This saves us a few repeated lines later on since we're going to create a few items. Next, we'll need a function to build orders. This function will take in an item ID, quantity, order count, and order date. It will then generate orders for that item for the specified quantity, count, and date. We'll use what's generated to populate a large number of orders.

```dbl
subroutine BuildOrders
    itemId, n
    quantity, int
    orderCount, int
    date, d8
    orders, @ArrayList
    endparams
    record
        ord, Order
        i, int
proc
    for i from 1 thru orderCount by 1
    begin
        ord.ItemId = %string(itemId)
        ord.OrderDate = date
        ord.OrderId = %string(orders.Count)
        ord.Quantity = quantity
        orders.Add((@Order)ord)
    end
    xreturn
endsubroutine
```

The above code is doing just a little bit more than the `MakeItem` function. Because we're going to be generating a lot of orders, we're going to use a FOR loop to do it. We're also going to be adding these orders to an array list` so we can keep track of them. Now that we have our helper routines, let's build some test data.

```dbl

main
    record
        restockRequests, @ArrayList
        trends, @ArrayList
        inventory, @ArrayList
        orders, @ArrayList
        analysisDate, d8
proc
    inventory = new ArrayList()
    orders = new ArrayList()
    ;;this is YYYYMMDD for September 28th, 2023
    ;;this is the format that %jperiod expects
    analysisDate = 20230928

    ;;Let's be explicit and box the returned structures
    inventory.Add((@InventoryItem)MakeItem(1, "widget", 50))
    inventory.Add((@InventoryItem)MakeItem(2, "doodad", 25))
    inventory.Add((@InventoryItem)MakeItem(3, "thingy", 100))
    inventory.Add((@InventoryItem)MakeItem(4, "whatchacallit", 0))

    ;;all of these dates are YYYYMMDD
    BuildOrders(1, 5, 20, 20230928, orders)
    BuildOrders(1, 3, 20, 20230728, orders)
    BuildOrders(1, 3, 20, 20230503, orders)
    BuildOrders(1, 3, 20, 20230328, orders)

    BuildOrders(2, 5, 20, 20230928, orders)
    BuildOrders(3, 5, 20, 20230404, orders)

    BuildOrders(4, 5, 2000, 20230104, orders)

endmain
```

Most of this code is starting to be old hat, but there are a few things to call out. Because ArrayList only understands `Object`, we need to box our structures by casting them to `@InventoryItem` or `@Order`. You may see other developers write this as `(@*)`, which is shorthand for casting to `Object`, but the problem with that is you don't really know if that is going to result in a boxed alpha representation of your structure or an actual boxed `InventoryItem`. The practical effect of this distinction is minimal in Traditional DBL, but if you're running on .NET, the type system is much more strict. We're also going to be using the `BuildOrders` function to generate a lot of orders into our `orders` variable. 

### Generating restock recommendations
Time for the moment of truth. Let's add our `GenerateRestockReco` subroutine to the bottom of our main and see what we get.

```dbl,ignore,does_not_compile
GenerateRestockReco(inventory, orders, analysisDate, trends, restockRequests)
```

This call to `GenerateRestockReco` is going to process the inventory and order data to generate restocking recommendations. The subroutine outputs two ArrayLists: `trends`, which holds trend data for each item, and `restockRequests`, which contains the restocking recommendations. Let's go through the steps to build this project and see what happens.

### Building the project
Make sure you have the DBL environment set up and you're still in the project directory. Additionally, make sure you've still got RPSMFIL and RPSTFIL set from the repository section.
We're making use of the StringDictionary that we built back in [Dictionary](../collections/dictionary.md), and to use that, we're going to need to write that source to a file in our project directory. I've named it `StringDictionary.dbl`, and also I've named the source we were just working on `Program.dbl`. Now run the following command:

```console
dbl Program.dbl StringDictionary.dbl
dblink Program
dbs Program
```

The first two commands should complete without error. If you get an error from the `dbl` compile step, make sure your repository is set up, that you have the most recent version of DBL installed, and that you've written both Program.dbl and StringDictionary.dbl to the project directory. The `dbs` command will run the program and output the results to the console. You should see something like this:

```console
%DBR-S-STPMSG, STOP
```

We didn't get any output! What happened? Well, we didn't tell the program to output anything. Let's add some code to do that.

### Outputting restock recommendations
After the call to `GenerateRestockReco`, add the following code:

```dbl
foreach data restockReq in restockRequests as @Restock
begin
    ;;We can go grab the item from inventory and show the name
    data item = (@InventoryItem)inventory[%integer(restockReq.ItemId) - 1]

    Console.WriteLine("restock request for " + %atrim(item.Name) + 
    &   " with quantity " + %string(restockReq.Quantity))
end
```
This loop iterates over the `restockRequests` array list. For each restock request, it retrieves the corresponding inventory item to display the item's name and the recommended restock quantity. This is a very simple way to output the recommendations, but at least we can see that the subroutine is working. Let's build and run the program again.

```console
dbl Program.dbl StringDictionary.dbl
dblink Program
dbs Program
```

This time we should see some output:

```console
dbs Program
restock request for widget with quantity 50
restock request for doodad with quantity 75
restock request for whatchacallit with quantity 833

%DBR-S-STPMSG, STOP
```

We can see that the subroutine is working as expected. It's recommending restocking for the widget, doodad, and whatchacallit items. The widget and doodad items have been selling well recently, and the whatchacallit item has been selling well historically. The subroutine is also recommending a higher quantity for the whatchacallit item because it has a history of massive sales spikes. Although this is a very simple example, it shows how we can use the tools we've learned to build a useful routine. We can do better though. In the next section, we're going to explore how we can use the `trends` array list to generate a very simple PDF report of the sales trends for each item.