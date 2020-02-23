## Robot File

```robottestfile
*** Settings ***
Documentation    Suite description
Library  SeleniumLibrary
Resource  ../Resources/RegistrationKeywords.robot

Suite Setup  Log  This is open browser Test
Suite Teardown  Log  This is close browser Test

Test Setup  Log  This is Login Test
Test Teardown  Log  This is Logout Test

*** Variables ***
${Browser}  Chrome
${URL}  https://opensource-demo.orangehrmlive.com/
@{Credentials}  Admin  admin123
&{LoginData}  username=Admin  password=admin123

*** Test Cases ***
TC_001
  [Tags]  sanity
  ${PageTitle}=  launchBrowser  ${url}  ${browser}
  log to console  ${PageTitle}
  log  ${PageTitle}
  Input Text  name:userName  mercury
  Input Text  name:password  mercury

TC_002
  [Tags]  sanity
  Log  This is postpaid recharge test

TC_003
  [Tags]  regression
  Log  This is search test

TC_004
  [Tags]  regression
  Log  This is Advanced search test

*** Keywords ***
Do login
  [Arguments]  ${test}
  Input Text  id:txtUsername  @{Credentials}[0]
  Input Text  id:txtPassword  &{LoginData}[password]
  Click Element  name:Submit
```