---
title: 'Building shinyApp2: Inputs options '
author: Victor
date: '2021-02-16'
slug: building-shinyapp2-inputs-outputs-and-layouts
categories:
  - R
tags:
  - Shiny Apps
  - Dashboards
subtitle: ''
summary: ''
authors: []
lastmod: '2021-02-16T15:51:28+03:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

## INPUTS

Shiny provides a wide variety of inputs that allows users to provide text **(textInput, selectInput)**, numbers **(numericInput, sliderInput)**, booleans **(checkBoxInput, radioInput)**, and dates **(dateInput, dateRangeInput)**.

For example:-

To select a range a name from a predifined range of choice we use - the **selectInput**. 


```r
selectInput("InputID",
            "Label",
            choices = c("A","B","C"))
```


To choose a range of values to filter weight we can use the **sliderInput**.


```r
sliderInput("InputID",
            "Label",
            value = 1925,
            max = 2021,
            min =1900,)
```

To select three phones to compare from a set of 10 options we can use the **checkBoxInput** 

**NoTE** - It's helpful to visualize what you want your input to look like first, then choose the corresponding Shiny input. It is better to draw a **Sketch** first.

### Add a select input
---------------------------------------

Adding an input to a shiny app is a two step process, where you first add an **___Input(“x”)** function to the UI and then access its value in the server using **input$x**.

For example, if you want users to choose an animal from a list, you can use a selectInput, and refer to the chosen value as input$animal:


```r
selectInput(
  'animal', 
  'Select Animal', 
  selected = 'Cat', 
  choices = c('Dog', 'Cat')
)
```

In this example, you will build a Shiny app that lets users visualize the top 10 most popular names by sex by adding an input to let them choose the sex.


```r
ui <- fluidPage(
  titlePanel("What's in a Name?"),
  # CODE BELOW: Add select input named "sex" to choose between "M" and "F"
selectInput("sex", "What is your gender?", "F", choices = c("M", "F")),
  # Add plot output to display top 10 most popular names
  plotOutput('plot_top_10_names')
)

server <- function(input, output, session){
  # Render plot of top 10 most popular names
  output$plot_top_10_names <- renderPlot({
    # Get top 10 names by sex and year
    top_10_names <- babynames %>% 
      # MODIFY CODE BELOW: Filter for the selected sex
      filter(sex == input$sex) %>% 
      filter(year == 1900) %>% 
      top_n(10, prop)
    # Plot top 10 names by sex and year
    ggplot(top_10_names, aes(x = name, y = prop)) +
      geom_col(fill = "#263e63")
  })
}

shinyApp(ui = ui, server = server)
```

### Slider Input
-------------------------------------

We can add a sliderInput for year to select the range of between 1900 to 2010, with a default of 1920 by adding the following code.


```r
ui <- fluidPage(
  titlePanel("What's in a Name?"),
  # Add select input named "sex" to choose between "M" and "F"
  selectInput('sex', 'Select Sex', choices = c("F", "M")),
  # CODE BELOW: Add slider input named 'year' to select years  (1900 - 2010)
  sliderInput("year", "choose year", value = 1900, min = 1900, max = 2010),

  # Add plot output to display top 10 most popular names
  plotOutput('plot_top_10_names')
)

server <- function(input, output, session){
  # Render plot of top 10 most popular names
  output$plot_top_10_names <- renderPlot({
    # Get top 10 names by sex and year
    top_10_names <- babynames %>% 
      filter(sex == input$sex) %>% 
    # MODIFY CODE BELOW: Filter for the selected year
      filter(year == input$year) %>% 
      top_n(10, prop)
    # Plot top 10 names by sex and year
      ggplot(top_10_names, aes(x = name, y = prop)) +
        geom_col(fill = "#263e63")
  })
}

shinyApp(ui = ui, server = server)
```

