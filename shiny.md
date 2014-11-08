# shiny 包


## 三个例子 ##

###输出随机分布的直方图

#### ui ####

    library(shiny)
    # 定义一个UI来画随机分布
    shinyUI(pageWithSidebar(
    # 应用名称
      headerPanel("Hello Shiny!")
     #有滑块的侧边栏用来输入观察值
     sidebarPanel(
     sliderInput("obs", 
                "Number of observations:", 
                min = 0, 
                max = 1000, 
                value = 500)
     ),

     #展现一个随机分布的图
     mainPanel(
    plotOutput("distPlot")
    )
    ))

#### server ###

      library(shiny)
       
      #定义一个server逻辑去生成画一个随机分布图
      shinyServer(function(input, output) {
      
     
        # 生成随机图的表达式，这个表达式放在renderPlot函数里
        #1）这时一个可交互式的，当改变输入值得时候，它自动变化
        #2）输出类型是一个图
        output$distPlot <- renderPlot({
        #生成一个随机分布，并画出它
          dist <- rnorm(input$obs)
          hist(dist)
        })
      })


#### ui ####

      library(shiny)
      
      #为数据view应用定义一个UI
      shinyUI(pageWithSidebar(
      
        headerPanel("Shiny Text"),
      
  
        #有控制的侧边框来选指定数据的视图
       
        sidebarPanel(
          selectInput("dataset", "Choose a dataset:", 
                      choices = c("rock", "pressure", "cars")),
      
          numericInput("obs", "Number of observations to view:", 10)
        ),
      
      
        # 对需求数据展现关于dataset的summary和一个HTML的表
        mainPanel(
          verbatimTextOutput("summary"),
      
          tableOutput("view")
        )
      ))

#### server ####

      library(shiny)
      library(datasets)
      
      #定义一个server逻辑根据summarize和view这些选择的数据
      shinyServer(function(input, output) {
      
        # 返回这需求的数据
        datasetInput <- reactive({
          switch(input$dataset,
                 "rock" = rock,
                 "pressure" = pressure,
                 "cars" = cars)
        })
      
        
        #生成这些数据的summary
        output$summary <- renderPrint({
          dataset <- datasetInput()
          summary(dataset)
        })
      
        # Show the first "n" observations
        output$view <- renderTable({
          head(datasetInput(), n = input$obs)
        })
      })


#### 反应式设计 ####

> input values => R code => output values
>
> 这种风格以反应值开始，反应值是随时间变化的值，或者由用户输入的值，在反应值之上绑定有反应表达式（reactive expression），反应表达式会接收到反应值并执行其他反应表达式

> 只需要把一个正常的表达式传递给reactive函数就行

#### ui ####

      library(shiny)
      
      # Define UI for dataset viewer application
      shinyUI(pageWithSidebar(
      
        # Application title
        headerPanel("Reactivity"),
      
        # Sidebar with controls to provide a caption, select a dataset, and 
        # specify the number of observations to view. Note that changes made
        # to the caption in the textInput control are updated in the output
        # area immediately as you type
        sidebarPanel(
          textInput("caption", "Caption:", "Data Summary"),
      
          selectInput("dataset", "Choose a dataset:", 
                      choices = c("rock", "pressure", "cars")),
      
          numericInput("obs", "Number of observations to view:", 10)
        ),
      
      
        # Show the caption, a summary of the dataset and an HTML table with
        # the requested number of observations
        mainPanel(
          h3(textOutput("caption")), 
      
          verbatimTextOutput("summary"), 
      
          tableOutput("view")
        )
      ))


#### server ####

       library(shiny)
       library(datasets)
       
       # Define server logic required to summarize and view the selected dataset
       shinyServer(function(input, output) {
       
         # By declaring datasetInput as a reactive expression we ensure that:
         #
         #  1) It is only called when the inputs it depends on changes
         #  2) The computation and result are shared by all the callers (it 
         #     only executes a single time)
         #  3) When the inputs change and the expression is re-executed, the
         #     new result is compared to the previous result; if the two are
         #     identical, then the callers are not notified
         #
         datasetInput <- reactive({
           switch(input$dataset,
                  "rock" = rock,
                  "pressure" = pressure,
                  "cars" = cars)
         })
       
         # The output$caption is computed based on a reactive expression that
         # returns input$caption. When the user changes the "caption" field:
         #
         #  1) This expression is automatically called to recompute the output 
         #  2) The new caption is pushed back to the browser for re-display
         # 
         # Note that because the data-oriented reactive expression below don't 
         # depend on input$caption, those expression are NOT called when 
         # input$caption changes.
         output$caption <- renderText({
           input$caption
         })
       
         # The output$summary depends on the datasetInput reactive expression, 
         # so will be re-executed whenever datasetInput is re-executed 
         # (i.e. whenever the input$dataset changes)
         output$summary <- renderPrint({
           dataset <- datasetInput()
           summary(dataset)
         })
       
         # The output$view depends on both the databaseInput reactive expression
         # and input$obs, so will be re-executed whenever input$dataset or 
         # input$obs is changed. 
         output$view <- renderTable({
           head(datasetInput(), n = input$obs)
         })
       })

## UI & Server ##

       library(shiny)
       
       # Define UI for miles per gallon application
       shinyUI(pageWithSidebar(
       
         # Application title
         headerPanel("Miles Per Gallon"),
       
         sidebarPanel(),
       
         mainPanel()
       ))

headerPanel、sidebarPanel和 mainPanel定义了用户接口的不同区域


     library(shiny)
     
     # Define UI for miles per gallon application
     shinyUI(pageWithSidebar(
     
       # Application title
       headerPanel("Miles Per Gallon"),
     
       # Sidebar with controls to select the variable to plot against mpg
       # and to specify whether outliers should be included
       # 提供一种方式来选择绘制MPG与哪个变量的图形，也提供了个选项
       sidebarPanel(
         selectInput("variable", "Variable:",
                     list("Cylinders" = "cyl", 
                          "Transmission" = "am", 
                          "Gears" = "gear")),
      #checkboxInput，用来控制是否显示异常值
         checkboxInput("outliers", "Show outliers", FALSE)
       ),
       
       mainPanel()
     ))

> 使用input对象的组件来访问输入，并通过向output对象的组件赋值来生成输出。


> 在启动的时候初始化的数据可以在应用程序的整个生命周期中被访问


> 使用反应表达式来计算被多个输出共享的值
      
 server.R
    
     library(shiny)
     library(datasets)
     
     # We tweak the "am" field to have nicer factor labels. Since this doesn't
     # rely on any user inputs we can do this once at startup and then use the
     # value throughout the lifetime of the application
     mpgData <- mtcars
     mpgData$am <- factor(mpgData$am, labels = c("Automatic", "Manual"))
     
     # Define server logic required to plot various variables against mpg
     shinyServer(function(input, output) {
     
       # Compute the forumla text in a reactive expression since it is 
       # shared by the output$caption and output$mpgPlot expressions
       formulaText <- reactive({
         paste("mpg ~", input$variable)
       })
     
       # Return the formula text for printing as a caption
       output$caption <- renderText({
         formulaText()
       })
     
       # Generate a plot of the requested variable against mpg and only 
       # include outliers if requested
       output$mpgPlot <- renderPlot({
         boxplot(as.formula(formulaText()), 
                 data = mpgData,
                 outline = input$outliers)
       })
     })

shiny用renderText和renderPlot生成输出

在主UI面板上添加一些元素


     library(shiny)
     
     # Define UI for miles per gallon application
     shinyUI(pageWithSidebar(
     
       # Application title
       headerPanel("Miles Per Gallon"),
     
       # Sidebar with controls to select the variable to plot against mpg
       # and to specify whether outliers should be included
       sidebarPanel(
         selectInput("variable", "Variable:",
                     list("Cylinders" = "cyl", 
                          "Transmission" = "am", 
                          "Gears" = "gear")),
     
         checkboxInput("outliers", "Show outliers", FALSE)
       ),
     
       # Show the caption and plot of the requested variable against mpg
       # 增加了这些 
       mainPanel(
         h3(textOutput("caption")),
     
         plotOutput("mpgPlot")
       )
     ))



运行和调试

滑动条

####  定制化滑动条####

slider控件功能非常强大，同时也可以定制化。它支持的特性有：



> 既可以输入单个的值，也可以输入一个范围。


> 自定义显示值的格式（比如，货币格式）


> 让滑动条在一定范围内自动滑动

ui.R

     library(shiny)
     
     # Define UI for slider demo application
     shinyUI(pageWithSidebar(
     
       #  Application title
       headerPanel("Sliders"),
     
       # Sidebar with sliders that demonstrate various available options
       sidebarPanel(
         # Simple integer interval
         sliderInput("integer", "Integer:", 
                     min=0, max=1000, value=500),
     
         # Decimal interval with step value
         sliderInput("decimal", "Decimal:", 
                     min = 0, max = 1, value = 0.5, step= 0.1),
     
         # Specification of range within an interval
         sliderInput("range", "Range:",
                     min = 1, max = 1000, value = c(200,500)),
     
         # Provide a custom currency format for value display, with basic animation
         sliderInput("format", "Custom Format:", 
                     min = 0, max = 10000, value = 0, step = 2500,
                     format="$#,##0", locale="us", animate=TRUE),
     
         # Animation with custom interval (in ms) to control speed, plus looping
         sliderInput("animation", "Looping Animation:", 1, 2000, 1, step = 10, 
                     animate=animationOptions(interval=300, loop=T))
       ),
     
       # Show a table summarizing the values entered
       mainPanel(
         tableOutput("values")
       )
     ))

#### 服务端脚本 ####
slider应用程序的服务端是很简单的：它创建了个数据框，用来存放所有输入值，然后把它渲染到HTML表中：

server.R

     library(shiny)
     
     # Define server logic for slider examples
     
     shinyServer(function(input, output) {
     
       # Reactive expression to compose a data frame containing all of the values
       sliderValues <- reactive({
     
         # Compose data frame
         data.frame(
           Name = c("Integer", 
                    "Decimal",
                    "Range",
                    "Custom Format",
                    "Animation"),
           Value = as.character(c(input$integer, 
                                  input$decimal,
                                  paste(input$range, collapse=' '),
                                  input$format,
                                  input$animation)), 
           stringsAsFactors=FALSE)
       }) 
     
       # Show the values using an HTML table
       output$values <- renderTable({
         sliderValues()
       })
     })
选项卡

> 选项卡（tabsets）是由调用tabsetPanel函数创建的，在这函数中，又需要用tabPanel函数创建选项（tab）列表

ui.R
