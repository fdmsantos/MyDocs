## Install

```bash
pip install -U selenium
pip install robotframework-seleniumlibrary
# Download Browser Drivers
```

## Input Text and Click

```robottestfile
*** Test Cases ***
Login Test
    Open Browser  ${url}  ${browser}
    loginToApplication
    Close Browser

*** Keywords ***
loginToApplication
    Click Link  xpath://a[@class='ico-login']
    Input Text  id:Email  pavanoltraining@gmail.com
    Input Text  id:Password  Test@123
    Click Element  xpath://input[@class='button-1 login-button']
```


## Radio Buttons and Check Boxes

```robottestfile
Testing Radio Buttons and Check Boxes
    Open Browser  ${url}  ${browser}
    Maximize Browser Window
    Select Radio Button  sex  Female
    Select Radio Button  exp  5
    Select Checkbox  BlackTea
    Select Checkbox  RedTea
    Unselect Checkbox  BlackTea
    Close Browser
```

## Dropdown and Lists

```robottestfile
Handling DropDownds and Lists
    Open Browser  ${url}  ${browser}
    Maximize Browser Window
    Select From List By Label  continents  Asia
    Sleep  5
    Select From List By Index  continents  5
    Select From List By Label  selenium_commands  Switch Commands
    Select From List By Label  selenium_commands  WebElement Commands
    Sleep  5
    Unselect From List By Label  selenium_commands  Switch Commands

```

## HTML Table

```robottestfile
TableValidations
    open browser  https://www.testautomationpractice.blogspot.com/  chrome
    maximize browser window
    ${rows}=  get element count  xpath://table[@name='BookTable']/tbody/tr

    ${cols}=  get element count  xpath://table[@name='BookTable']/tbody/tr[1]/th

    log to console  ${rows}
    log to console  ${cols}

    ${data}=  get text  xpath://table[@name='BookTable']/tbody/tr[5]/td[1]
    log to console  ${data}

    table column should contain  xpath://table[@name='BookTable']  2  Author
    table row should contain  xpath://table[@name='BookTable']  4  Learn JS
    table cell should contain  xpath://table[@name='BookTable']  5  2  Mukesh
    table header should contain  xpath://table[@name='BookTable']  BookName

    close browser
```

## Speed

```robottestfile
RegTest
    ${speed}=  Get Selenium Speed
    Log To Console  ${speed}
    Open Browser  ${url}  ${browser}
    Maximize Browser Window
    Set Selenium Speed  3 seconds
    Select Radio Button  Gender  M
    Input Text  name:FirstName  David
    Input Text  name:LastName  John
    Input Text  name:Email  anhc@gmail.com
    Input Text  name:Password  davidjohn
    Input Text  name:ConfirmPassword  davidjohn
    ${speed}=  Get Selenium Speed
    Log To Console  ${speed}
    Close Browser
```

## Timeouts

```robottestfile
Timeouts
    Open Browser  ${url}  ${browser}
    Maximize Browser Window
    Set Selenium Timeout  10 seconds
    Wait Until Page Contains  Registeration
    Select Radio Button  Gender  M
    Input Text  name:FirstName  David
    Input Text  name:LastName  John
    Input Text  name:Email  anhc@gmail.com
    Input Text  name:Password  davidjohn
    Input Text  name:ConfirmPassword  davidjohn
    Close Browser
```

## Implict Wait

```robottestfile
Implict Wait Test
    Open Browser  ${url}  ${browser}
    Maximize Browser Window
    Set Selenium Implicit wait  10 seconds
    Select Radio Button  Gender  M
    Input Text  name:FirstName  David
    Input Text  name:LastName  John
    Input Text  name:Email  anhc@gmail.com
    Input Text  name:Password  davidjohn
    Input Text  name:ConfirmPassword  davidjohn
    Close Browser
```

## Multiple Browsers

```robottestfile
MultipleBrowsers
    Open Browser  https://www.google.com/  chrome
    Maximize Browser Window

    Sleep  3

    Open Browser  https://www.bing.com/  chrome
    Maximize Browser Window

    Switch Browser  1
    ${title1}=  Get Title
    log to console  ${title1}

    Switch Browser  2
    ${title2}=  Get Title
    log to console  ${title2}

    Sleep  3
    Close All Browsers
```

## Tabbed Windows

```robottestfile
TabbedWindowsTest
    Open Browser  http://demo.automationtesting.in/Windows.html  chrome
    Click Element  xpath://*[@id="Tabbed"]/a/button
    Select Window  title=Sakinalium  | Home
    Click Element  xpath://*[@id="container"]/header/div/div/div[2]/ul/Li[4]/a
    Sleep  3
    Close All Browsers
```

## Navigation Keywords

```robottestfile
Navigation Test
    Open Browser  https://www.google.com  chrome
    ${loc}=  Get Location
    log to console  ${loc}
    sleep  3
    go to  https://www.bing.com/
    ${loc}=  Get Location
    log to console  ${loc}
    sleep  3
    go back
    ${loc}=  Get Location
    log to console  ${loc}
    sleep  3
    close browser
```

## Handling Alerts

```robottestfile
Handling Alerts
    Open Browser  https://testautomationpractice.blogspot.com/  chrome
    Click Element  xpath://*[@id="HTML9"]/div[1]/button  # Opens Alert
    Sleep  1
    Alert Should be Present  Press a button!
    Handle Alert  accept
```

## Handling Frames

```robottestfile
Testing Frames
    Open Browser  https://seleniumhq.github.io/selenium/docs/api/java/index  chrome
    Select Frame  packageListFrame
    Click Link  org.openqa.selenium
    Unselect Frame

    Select Frame  packageFrame
    Click Link  WebDriver
    Unselect Frame

    Select Frame  classFrame
    Click Link  Help

    Close Browser
```

## Scroll And Executing Javascript

```robottestfile
ScrollingTest
    Open Browser  https://www.countries-ofthe-world.com/flags-of-the-world.html  chrome
    execute javascript  window.scrollTo(0,2500)
    scroll element into view  xpath://table[1]//tbody[1]//tr[105]//td[1]//img[1]
    execute javascript  window.scrollTo(0,document.body.scrollHeight)  # End of the Page
    sleep  5
    execute javascript  window.scrollTo(0,-document.body.scrollHeight)  # Starting Point
```