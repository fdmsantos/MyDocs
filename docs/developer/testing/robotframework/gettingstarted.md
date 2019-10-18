# Install

```bash
pip install robotframework
```

## Pycharm

* Plugins

    * IntelliBot
    
   
# Structure
* SCALAR
    * ${VARIABLE NAME}  value
* LIST
    * @{VARIABLE NAME}  value1  value2
* DICTIONARY
    * &{VARIABLE NAME}  k1=value1  k2=value2
* ENVIRONMENT
    * %{USERNAME}
* BUILT-IN

# Robot Commands

```bash
# Run One Test
robot FirstTestSuite.robot

# Run All Tests in folder
robot TestCases/

# Run Tests based in regular expressions
robot TestsCases\Reg*.robot

# With Tags
robot --include=sanity Setup_TearDown.robot
robot -i sanity -i regression Setup_TearDown.robot
robot -e regression Setup_TearDown.robot 
robot -i sanity -e regression Setup_TearDown.robot
```

# Pabot Commands

```bash
pip install -U robotframework-pabot
pabot --processes 2 TestCases/*.robot
pabot --processes 2 --outputdir Results TestCases/*.robot
```