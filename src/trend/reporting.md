# Reporting
It's time to do something interesting with this trend data that we collected in the preceding section. Synergex has a wrapper library for the Haru PDF library that we can use to generate a PDF report of our sales trends. To start, we're going to need to download the source for the wrapper library. You can find it on GitHub at [PDFKit](https://github.com/Synergex/PDFKit15f/blob/main/pdfdbl.dbl) and download it to your project directory. Next, we'll need to grab the built libraries for our platform [here](https://github.com/Synergex/PDFKit15f/tree/main/Windows64). Download the three DLLs and put them in your project directory too. Now we're ready to start writing our report.

### New imports
At the top of `Program.dbl`, add the following imports:
```dbl
import HPdf
```

### New subroutine signature
Next, we're going to add a new subroutine to generate the report. Add the following code to the bottom of `Program.dbl`:

```dbl
subroutine GenerateSalesTrendsReport
    in trends, @ArrayList
    in inventory, @ArrayList
    endparams
    record
        pdf, @HPdfDoc
        page, @HPdfPage
        font, @HPdfFont
        yPos, float
    proc
    ;; implementation goes here
endsubroutine
```

Our routine needs the trends we've collected, and we're also going to use the inventory list to get the item names.

### Initialization of the PDF 
Inside the procedure division, we're going to start by initializing the PDF document using the following code:

```dbl
pdf = new HPdfDoc()
```

This is going to create a new instance of `HPdfDoc`, which is a class from the Haru PDF library. This instance represents the PDF document we're going to create and modify.

### Adding a new page and setting up
Next we're going to add the first page to the document and set the font using the following code:
```dbl
page = pdf.AddPage()
page.SetSize(HPdfPageSizes.HPDF_PAGE_SIZE_A4, HPdfPageDirection.HPDF_PAGE_PORTRAIT)
```

The size of the page is set to A4, and the orientation is defined as portrait. Although this is the default page size and orientation, it's good practice to set it explicitly.

### Setting up the Font
To add text to the page, we need to tell Haru what font to use. We're going to use the following code to set the font:

```dbl
font = pdf.GetFont("Helvetica", ^null)
page.SetFontAndSize(font, 12)
```
We've chosen the oh-so-trendy Swiss font Helvetica with a size of 12. This font setting will apply to all the text added to the PDF pages.

### Initializing the Y position for text
To support multiple pages, we need to keep track of the vertical position of the text on the page. We'll do this using the following code:

```dbl
yPos = page.GetHeight() - 50
```

The `yPos` variable is initialized to manage the vertical position of text on the page. This line positions the first line of text 50 units from the top of the page.

### Looping through the trends array list
Now we've gotten to the interesting part: we're going to loop through the trends array list and add each trend to the PDF document. I've broken this up a little bit for readability, but we're going to fill in this loop in the next step. For starters, let's get the loop in place using the following code:

```dbl
foreach data trend in trends as @Trend
begin
    data item = (@InventoryItem)inventory[%integer(trend.ItemId) - 1]
    ;;process each trend
end
```
In this loop, each `Trend` object in the `trends` ArrayList is processed. For each trend, the corresponding `InventoryItem` is retrieved from the `inventory` ArrayList. Doing this allows the item name to be included in the report.

### Adding the items in our trends loop
Inside the loop, we're going to actually add the items to the PDF document using the following code:

```dbl
if (yPos < 50)
begin
    page = pdf.AddPage()
    page.SetSize(HPdfPageSizes.HPDF_PAGE_SIZE_A4, 
    &   HPdfPageDirection.HPDF_PAGE_PORTRAIT)
    page.SetFontAndSize(font, 12)
    yPos = page.GetHeight() - 50
end

page.BeginText()
page.MoveTextPos(50, yPos)
page.ShowText("Item: " + %atrim(item.Name) +  
&   ",Item Count: " + %string(trend.ItemCount) +
&   ", Order Count: " + %string(trend.OrderCount) + 
&   ", Historic Count: " + %string(trend.HistoricCount))
page.EndText()

yPos -= 20
```
Here, the subroutine checks if the current page has enough space for more text. If `yPos` is less than 50, which means the page is nearly full, a new page is added with the same settings as before. The routine then adds text to the page, including the item's name and its sales trend data. After each entry, `yPos` is adjusted to move to the next line, ensuring proper spacing between lines of text.

### Saving and closing the PDF document
Now, outside of the trends loop, we're going to save and close the PDF document using the following code:

```dbl
pdf.SaveToFile("SalesTrendsReport.pdf")
pdf.FreeDoc()
```
After all trends are processed and added to the document, the PDF is saved to a file named "SalesTrendsReport.pdf". The `FreeDoc` method is then called to release the resources associated with the PDF document.

### Calling the subroutine
Now that we've written the subroutine, we need to call it. Add the following code to the bottom of `main`:

```dbl
GenerateSalesTrendsReport(trends, inventory)
```

### Building and running the program

```console
dbl Program.dbl StringDictionary.dbl pdfdbl.dbl
dblink Program
dbs Program
```

This time we should see the same output as before, but we should also see a new file in our project directory named `SalesTrendsReport.pdf`.:

```console
dbs Program
restock request for widget with quantity 50
restock request for doodad with quantity 75
restock request for whatchacallit with quantity 833

%DBR-S-STPMSG, STOP
```

Open the newly created pdf, and it should have text that looks like this:

```text
Item: widget, Item Count: 100, Order Count: 20, Historic Count: 180
Item: doodad, Item Count: 100, Order Count: 20, Historic Count: 0
Item: thingy, Item Count: 0, Order Count: 0, Historic Count: 100
Item: whatchacallit, Item Count: 0, Order Count: 0, Historic Count: 1000
```

If you got error text that looks like this:

```console
%DBR-E-DLLOPNERR, DLL could not be opened: libhpdf64.dll
%DBR-I-ERTXT2, System err: (126)  The specified module could not be found.
```

you probably don't have all three of the DLLs in your project directory. Go back and make sure all of the DLLs are in your directory and that they are bit-size/platform matched. For example, I ran dblvars64.bat in my command prompt, so I'm running 64-bit Windows; therefore, when I downloaded the DLLs from GitHub, I grabbed the DLLs in the Windows64 directory. If you ran dblvars32.bat, you would need to grab the DLLs from the Windows32 directory. Hopefully everything is running now, and you can see the PDF report. If you're having trouble, you can check the companion repository on GitHub for the completed code.<!--TODO: Add link?-->