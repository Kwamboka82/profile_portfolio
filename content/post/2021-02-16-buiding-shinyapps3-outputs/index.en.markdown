---
title: 'Buiding shinyApps3: Outputs'
author: Victor
date: '2021-02-16'
slug: buiding-shinyapps3-outputs
categories:
  - R
tags:
  - Dashboards
  - Shiny Apps
subtitle: ''
summary: ''
authors: []
lastmod: '2021-02-16T16:45:31+03:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

## OutPuts in ShinyApps

Outputs are build in shinyApps based on inputs. We use render functiond to create a variety of outputs ranging from plots **renderPlot**, tables **renderTable** and images **renderImage**.

### Add a table output
----------------------------------------

In order to add any output to a Shiny app, you need to:

- Create the output (plot, table, text, etc.).
- Render the output object using the appropriate *render___* function.
- Assign the rendered object to *output$x*.
- Add the output to the UI using the appropriate *___Output* function.
- In this example, you will add a table output to the baby names explorer app you created earlier. Don't forget that code inside a render___ function needs to be wrapped inside curly braces (e.g. renderPlot({...})).


```r
ui <- fluidPage(
  titlePanel("What's in a Name?"),
  # Add select input named "sex" to choose between "M" and "F"
  selectInput('sex', 'Select Sex', choices = c("F", "M")),
  # Add slider input named "year" to select year between 1900 and 2010
  sliderInput('year', 'Select Year', min = 1900, max = 2010, value = 1900),
  # CODE BELOW: Add table output named "table_top_10_names"
  tableOutput ("table_top_10_names")
)
server <- function(input, output, session){
  # Function to create a data frame of top 10 names by sex and year 
  top_10_names <- function(){
    top_10_names <- babynames %>% 
      filter(sex == input$sex) %>% 
      filter(year == input$year) %>% 
      top_n(10, prop)
  }
  # CODE BELOW: Render a table output named "table_top_10_names"
  output$table_top_10_names <- renderTable ({
    top_10_names()
  })
  
}
shinyApp(ui = ui, server = server)
```

### Add an interactive table output
-------------------------------------------

There are multiple *htmlwidgets packages* like DT, leaflet, plotly, etc. that provide highly interactive outputs and can be easily integrated into Shiny apps using almost the same pattern. For example, you can turn a static table in a Shiny app into an interactive table using the *DT package*:

- Create an interactive table using DT::datatable().
- Render it using DT::renderDT().
- Display it using DT::DTOutput().
- In this example, you will update the app created previously, replacing the static table with an interactive table.


```r
ui <- fluidPage(
  titlePanel("What's in a Name?"),
  # Add select input named "sex" to choose between "M" and "F"
  selectInput('sex', 'Select Sex', choices = c("M", "F")),
  # Add slider input named "year" to select year between 1900 and 2010
  sliderInput('year', 'Select Year', min = 1900, max = 2010, value = 1900),
  # MODIFY CODE BELOW: Add a DT output named "table_top_10_names"
  DT::DTOutput('table_top_10_names')
)
server <- function(input, output, session){
  top_10_names <- function(){
    babynames %>% 
      filter(sex == input$sex) %>% 
      filter(year == input$year) %>% 
      top_n(10, prop)
  }
  # MODIFY CODE BELOW: Render a DT output named "table_top_10_names"
  output$table_top_10_names <- DT::renderDT({
    top_10_names() %>%
    DT::datatable()
  })
}
shinyApp(ui = ui, server = server)
```

### Add interactive plot output
--------------------------------------------------------

Similar to creating interactive tables, you can easily turn a static plot created using ggplot2 into an interactive plot using the *plotly package*. To render an interactive plot, use plotly::renderPlotly(), and display it using plotly::plotlyOutput().

Remember that just like with other render functions, the code inside plotly::renderPlotly() should be wrapped in curly braces {}!

Check out the next illustrations


```r
ui <- fluidPage(
  selectInput('name', 'Select Name', top_trendy_names$name),
  # CODE BELOW: Add a plotly output named 'plot_trendy_names'
  plotly::plotlyOutput("plot_trendy_names")
)
server <- function(input, output, session){
  # Function to plot trends in a name
  plot_trends <- function(){
     babynames %>% 
      filter(name == input$name) %>% 
      ggplot(aes(x = year, y = n)) +
      geom_col()
  }
  # CODE BELOW: Render a plotly output named 'plot_trendy_names'
output$plot_trendy_names <- plotly::renderPlotly({
  plot_trends()
})
  
  
}
shinyApp(ui = ui, server = server)
```

### Benefits of an interactive plot

Try interacting with the plot. You can zoom in on certain areas, zoom back out, and hover over the bars to see the values. This makes plots in your app far more interesting, and allows users to gain insights without having to see any code or data.
