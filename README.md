
[![Project Status: WIP ? Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](http://www.repostatus.org/badges/latest/wip.svg)](http://www.repostatus.org/#wip) [![Build Status](https://travis-ci.org/carlganz/rintrojs.svg?branch=master)](https://travis-ci.org/carlganz/rintrojs)[![Coverage Status](https://img.shields.io/codecov/c/github/carlganz/rintrojs/master.svg)](https://codecov.io/github/carlganz/rintrojs?branch=master)

rintrojs
========

The goal of `rintrojs` is to integrate the javascript library [`intro.js`](http://introjs.com/) into `shiny`.

All credit for `intro.js` goes to its author Afshin Mehrabani. I have contributed nothing to `intro.js`. I simply wrote a wrapper for easy integration with `shiny`.

Install
-------

Use `devtools` to install `rintrojs` from github.

``` r
devtools::install_github("carlganz/rintrojs")
```

Usage
-----

To use `rintrojs`, you need to call `introjsUI()` once in the UI, and you need to wrap the elements you want to introduce with `introBox`. You specify the order of the introduction with the data.step parameter of `introBox`, and you specify the content of the introduction with the data.intro parameter of `introBox`. You can initiate the introduction from the server by calling `introjs(session)`.

``` r
library(rintrojs)
library(shiny)

# Define UI for application that draws a histogram
ui <- shinyUI(fluidPage(
  introjsUI(),
  
  # Application title
  introBox(
    titlePanel("Old Faithful Geyser Data"),
    data.step = 1,
    data.intro = "This is the title panel"
  ),
  
  # Sidebar with a slider input for number of bins
  sidebarLayout(sidebarPanel(
    introBox(
      introBox(
        sliderInput(
          "bins",
          "Number of bins:",
          min = 1,
          max = 50,
          value = 30
        ),
        data.step = 3,
        data.intro = "This is a slider",
        data.hint = "You can slide me"
      ),
      introBox(
        actionButton("help", "Press for instructions"),
        data.step = 4,
        data.intro = "This is a button",
        data.hint = "You can press me"
      ),
      data.step = 2,
      data.intro = "This is the sidebar. Look how intro elements can nest"
    )
  ),
  
  # Show a plot of the generated distribution
  mainPanel(
    introBox(
      plotOutput("distPlot"),
      data.step = 5,
      data.intro = "This is the main plot"
    )
  ))
))

# Define server logic required to draw a histogram
server <- shinyServer(function(input, output, session) {
  # initiate hints on startup with custom button and event
  hintjs(session, options = list("hintButtonLabel"="Hope this hint was helpful"),
         events = list("onhintclose"='alert("Wasn\'t that hint helpful")'))
  
  output$distPlot <- renderPlot({
    # generate bins based on input$bins from ui.R
    x    <- faithful[, 2]
    bins <- seq(min(x), max(x), length.out = input$bins + 1)
    
    # draw the histogram with the specified number of bins
    hist(x,
         breaks = bins,
         col = 'darkgray',
         border = 'white')
  })
  
  # start introjs when button is pressed with custom options and events
  observeEvent(input$help,
               introjs(session, options = list("nextLabel"="Onwards and Upwards",
                                               "prevLabel"="Did you forget something?",
                                               "skipLabel"="Don't be a quitter"),
                                events = list("oncomplete"='alert("Glad that is over")'))
  )
})

# Run the application
shinyApp(ui = ui, server = server)
```

Click [here to view example.](https://carlganz.shinyapps.io/rintrojsexample/)

Contributing
------------

This is still pretty preliminary, so any and all feedback is appreciated.
