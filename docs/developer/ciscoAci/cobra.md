## Create Session

```python
from credentials import *
import cobra.mit.session
import cobra.mit.access

auth = cobra.mit.session.LoginSession(URL, LOGIN, PASSWORD)
session = cobra.mit.access.MoDirectory(auth)
session.login()
```

## DN Query

```python
import cobra.mit.request
tenant_query = cobra.mit.request.DnQuery("uni/tn-Heroes")
heroes_tenant = session.query(tenant_query)
heroes = heroes_tenant[0]
dir(heroes)
```

## Class Query

* propFilter (query-target-filter) filters the results based on class attributes.
* queryTarget (query-target) specifies what part of the MIT to query:
    * self
    * children
    * subtree
* classFilter (target-subtree-class) can be used when queryTarget is set to either "children" or "subtree" to filter the returned children to only objects of a specific class or classes.
    * For example, to filter for Application Profiles use fvAp.
*subtree (rsp-subtree) specifies how much of the subtree to retrieve:
     * no
     * children
     * full
* subtreeClassFilter (rsp-subtree-class) filters what subtree classes to include in the response.
* subtreeInclude (rsp-subtree-include) specifies what type of information to include in the subtree
    * audit-logs
    * event-logs
    * faults
        * faults,no-scoped
    * health

### Make a Class Query for All App Profiles

 ```python
app_query = cobra.mit.request.ClassQuery('fvAp')
apps = session.query(app_query)
```

### Scope the Query to Applications Named "Save_The_Planet"

```python
# set the property filter to only return the app named "Save_The_Planet"
app_query.propFilter = 'eq(fvAp.name, "Save_The_Planet")'
save_the_planet_app = session.query(app_query)
```
    
## Child Objects

### Have the Query Return Child Objects

```python
# set the scope to subtree full
app_query.subtree = "full"
 
# demonstrate a typo; cobra provides the acceptable options
app_query.queryTarget = "subtre"
Traceback (most recent call last):
...
    (value, str(allowedValues)))
ValueError: "subtre" is invalid, valid values are "set(['self', 'subtree', 'children'])"

# scope the query to subtree
app_query.queryTarget = "subtree"
# look at the applied query scopes using .options
app_query.options
'rsp-subtree=full&query-target-filter=eq(fvAp.name, "Save_The_Planet")&query-target=subtree'
save_the_planet_app_subtree = session.query(app_query)
```

### View the Child Objects for the Query

```python
save_the_planet_app_subtree
[<cobra.modelimpl.fv.ap.Ap object at 0x7f3c3c735150>]
save_the_planet_app_subtree[0].numChildren
3

for epg in save_the_planet_app_subtree[0].children:
     print epg.dn
      
uni/tn-Heroes/ap-Save_The_Planet/epg-web
uni/tn-Heroes/ap-Save_The_Planet/epg-app
uni/tn-Heroes/ap-Save_The_Planet/epg-db
```

## Submitting Configurations

```python
# submit staged configuration to APIC
config_request = cobra.mit.request.ConfigRequest()
config_request.addMo(epg)
session.commit(config_request)

# You will see these two output lines
# SSL Warning
# <Response [200]>
```

## Lookups

### LookupbyDn

```python
# set top level universe directory
uniMo = moDir.lookupByDn('uni')
```

### lookupByClass

```python
# use the "lookupByClass" method to find all endpoints (fvCEp)

endpoints = md.lookupByClass('fvCEp')

print([str(ep.dn) for ep in endpoints]) 
```