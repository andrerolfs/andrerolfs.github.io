{% include_relative menu.md %}

# 20180707 HTML Framework In Groovy For Creating Tables

I have spent some time on my [HTML framework](https://github.com/andrerolfs/htmlframework).

Now I can create tables in HTML with `<table/>`and `<div/>` by writing a script in groovy which has to  create a
hierarchy of objects representing the structure and content of the table.

Please look at the Spock unit tests to see how to use it.

The example for `<table/>` is :

```
    HtmlTable table = new HtmlTable()
    table.setColumns(2)
    table.addCell("Hello 1")
    table.addCell("World 1")
    table.addCell(new HtmlA("lo","http://hel"))
    table.addCell("World 2")
    String output = HtmlTooling.htmlText(
                        table,
                        "/Users/amos/github/htmlframework/css/divtable.css")
```
