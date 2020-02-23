# Data Driver

## Install

```bash
pip install robotframework-datadriver
```

## Resources

```robottestfile
*** Settings ***
Documentation    Suite description
Library  SeleniumLibrary

*** Variables ***
${browser}  chrome
${url}  https://admin-demo.nopcommerce.com

*** Keywords ***
Open my Browser
    Open Browser  ${url}  ${browser}
    maximize browser window

Close Browsers
    close all browsers

Open Login Page
    go to  ${url}

Input username
    [Arguments]  ${username}
    input text  id:Email  ${username}

Input pwd
    [Arguments]  ${password}
    input text  id:Password  ${password}

click login button
    click element  xpath://input[@class='button-1 login-button']

Error message should be visible
    page should contain  Login was unsuccessful
```

## Robot File with Data

```robottestfile
*** Settings ***
Documentation    Suite description
Library  SeleniumLibrary
Resource  ../Resources/login_resources.robot
Suite Setup  Open my Browser
Suite Teardown  Close Browsers
Test Template  Invalid Login

*** Test Cases ***  username  password
Right user empty pwd  admin@yourstore.com  ${EMPTY}
Right user wrong pass  admin@yourstore.com  xyz
Wrong user right pass  adm@yourstore.com  admin
Wrong user empty pass  adm@yourstore.com  ${EMPTY}
Wrong user wrong pass  adm@yourstore.com  xyz


*** Keywords ***
Invalid Login
    [Arguments]  ${username}  ${password}
    Input username  ${username}
    Input pwd  ${password}
    click login button
    Error message should be visible
```


# From Excel and CSV

```robottestfile
*** Settings ***
Documentation    Suite description
Library  SeleniumLibrary
Resource  ../Resources/login_resources.robot
#Library  DataDriver  ../TestData/LoginData.xlsx  sheet_name=Sheet1
Library  DataDriver  ../TestData/LoginData.csv

Suite Setup  Open my Browser
Suite Teardown  Close Browsers
Test Template  Invalid Login

*** Test Cases ***
LoginTestWithExcel  using  ${username} and ${password}

*** Keywords ***
Invalid Login
    [Arguments]  ${username}  ${password}
    Input username  ${username}
    Input pwd  ${password}
    click login button
    Error message should be visible
```