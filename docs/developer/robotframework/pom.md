## Locators

* File: PageObjects/Locators.py

```python
# Login Page Elements
txt_loginUserName="name:userName"
txt_loginPassword="name:password"
btn_signIn="xpath://input[@name='login']"

# Registration Page Elements
link_Reg="link:REGISTER"
txt_firstName="name:firstName"
txt_lastName="name:lastName"
txt_phone="name:phone"
txt_email="name:userName"
txt_add1="name:address1"
txt_add2="name:address2"
txt_city="name:city"
txt_state="name:state"
txt_postCode="name:postalCode"
drp_country="name:country"
txt_userName="name:email"
txt_Password="name:password"
txt_conformedPassword="name:confirmPassword"
btn_submit="xpath://input[@name='register']"
```

## Resources

* File: Resources/LoginKeywords.robot

```robottestfile
*** Settings ***
Documentation    Suite description
Library  SeleniumLibrary
Variables  ../PageObjects/Locators.py

*** Keywords ***
Open my Browser
    [Arguments]  ${SiteUrl}  ${Browser}
    Open Browser  ${SiteUrl}  ${Browser}
    Maximize browser window

Enter UserName
    [Arguments]  ${username}
    Input Text  ${txt_loginUserName}  ${username}

Enter Password
    [Arguments]  ${password}
    Input Text  ${txt_loginPassword}  ${password}

Click SignIn
    Click Button  ${btn_signIn}

Verify Succesfull Login
    title should be  Find a Flight: Mercury Tours:

Close my browsers
    close all browsers
```

* File: Resources/Registration.robot

```robottestfile
*** Settings ***
Documentation    Suite description
Library  SeleniumLibrary
Variables  ../PageObjects/Locators.py

*** Keywords ***
Open my Browser
    [Arguments]  ${SiteUrl}  ${Browser}
    Open Browser  ${SiteUrl}  ${Browser}
    Maximize browser window

Click Register Link
    click link  ${link_Reg}

Enter FirstName
    [Arguments]  ${firstName}
    Input Text  ${txt_firstName}  ${firstName}

Enter LastName
    [Arguments]  ${lastName}
    Input Text  ${txt_lastName}  ${lastName}

Enter Phone
    [Arguments]  ${phone}
    Input Text  ${txt_phone}  ${phone}

Enter Email
    [Arguments]  ${email}
    Input Text  ${txt_email}  ${email}

Enter Address1
    [Arguments]  ${add1}
    Input Text  ${txt_add1}  ${add1}

Enter Address2
    [Arguments]  ${add2}
    Input Text  ${txt_add2}  ${add2}

Enter City
    [Arguments]  ${city}
    Input Text  ${txt_city}  ${city}

Enter State
    [Arguments]  ${state}
    Input Text  ${txt_state}  ${state}

Enter Postal Code
    [Arguments]  ${postalcode}
    Input Text  ${txt_postCode}  ${postalcode}

Select Country
    [Arguments]  ${country}
    Select from list by label  ${drp_country}  ${country}

Enter User Name
    [Arguments]  ${username}
    Input Text  ${txt_userName}  ${username}

Enter Password
    [Arguments]  ${password}
    Input Text  ${txt_Password}  ${password}

Enter Confirmed Password
    [Arguments]  ${password}
    Input Text  ${txt_conformedPassword}  ${password}

Click Submit
    Click button  ${btn_submit}


Verify Succesfull Registration
    page should contain  Thank you for registeringdsadsa

Close my browsers
    close all browsers
```

## Tests

* File: TestCases/LoginTest.robot

```robotframework
*** Settings ***
Documentation    Suite description
Library  SeleniumLibrary
Resource  ../Resources/LoginKeywords.robot

*** Variables ***
${Browser}  chrome
${SiteUrl}  http://newtours.demoaut.com/
${user}  tutorial
${pwd}  tutorial

*** Test Cases ***
LoginTest
    Open my Browser  ${SiteUrl}  ${Browser}
    Enter UserName  ${user}
    Enter Password  ${pwd}
    Click SignIn
    Sleep  3 seconds
    Verify Succesfull Login
    Close my browsers
```

* File: TestCases/RegistrationTest.robot

```robottestfile
*** Settings ***
Documentation    Suite description
Library  SeleniumLibrary
Resource  ../Resources/RegistrationKeywords.robot

*** Variables ***
${Browser}  headlesschrome
${SiteUrl}  http://newtours.demoaut.com/


*** Test Cases ***
RegistrationTest
    Open my Browser  ${SiteUrl}  ${Browser}
    Click Register Link
    Enter FirstName  David
    Enter LastName  John
    Enter Phone  1234567890
    Enter Email  davidjohn@gmail.com
    Enter Address1  Toronto
    Enter Address2  Canada
    Enter City  Toronto
    Enter State  Brampton
    Enter Postal Code  L3S 1E7
    Select Country  CANADA
    Enter User Name  johnxyz
    Enter Password  johnxyz
    Enter Confirmed Password  johnxyz
    Click Submit
    Verify Succesfull Registration
    Close my browsers
```