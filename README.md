# venmo-to-simplify-csv
Transform Venmo CSV for import into Simplifi

## Overview
Customers of [Simplifi](https://www.quicken.com/products/simplifi/) have encountered trouble integrating with Venmo. A work-around is to export the transactions from Venmo in CSV format.  The data must be transformed before it can be imported into simplifi.

See Also
* https://community.simplifimoney.com/discussion/5392/ongoing-9-6-23-venmo-returns-a-103-error
* https://community.simplifimoney.com/discussion/5383/fdp-103-error-with-venmo

## Limitations
Venmo supports multi-line comments within transactions.  This search/replace approach cannot handle it.  Potentially, a tool such as [OpenRefine](https://openrefine.org/) can be used.

At the time of writing, Simplifi does not import transactions into an account linked to Venmo.  A workaround is to create a manual account for the transactions.

## Patterns
These regular expresessions will transform the venmo csv output so that Simplifi will import it.
These are search and replacement pairs.

    ^(?!,[0-9]{19,},)(?!^.*ID,Datetime,).*\r?\n?
Leave the replacement blank
#
    ^.*ID,Datetime,Type,Status,Note.*$
    "Date","Payee","Amount","Tags"
#
    ^,[0-9]{19,},([0-9]{4})-([0-9]{2})-([0-9]{2})T[0-9:]+\,Charge,[^,]*,[^,]*,([^,]*),[^,]*,("- \$(([0-9.],?)+)"|- \$(([0-9.]+))).*
    "$2/$3/$1","$4","-$6$8"
#
    ^,[0-9]{19,},([0-9]{4})-([0-9]{2})-([0-9]{2})T[0-9:]+\,Charge,[^,]*,[^,]*,([^,]*),[^,]*,("\+ \$(([0-9.],?)+)"|\+ \$(([0-9.]+))).*
    "$2/$3/$1","$4","$6$8"
#
    ^,[0-9]{19,},([0-9]{4})-([0-9]{2})-([0-9]{2})T[0-9:]+\,Merchant Transaction,[^,]*,[^,]*,[^,]*,([^,]*),("- \$(([0-9.],?)+)"|- \$(([0-9.]+))).*
    "$2/$3/$1","$4","-$6$8"
#
    ^,[0-9]{19,},([0-9]{4})-([0-9]{2})-([0-9]{2})T[0-9:]+\,Payment,[^,]*,[^,]*,([^,]*),[^,]*,("\+ \$(([0-9.],?)+)"|\+ \$(([0-9.]+))).*
    "$2/$3/$1","$4","$6$8"
#
    ^,[0-9]{19,},([0-9]{4})-([0-9]{2})-([0-9]{2})T[0-9:]+\,Payment,[^,]*,[^,]*,[^,]*,([^,]*),("- \$(([0-9.],?)+)"|- \$(([0-9.]+))).*
    "$2/$3/$1","$4","-$6$8"
#
    ^,[0-9]{19,},([0-9]{4})-([0-9]{2})-([0-9]{2})T[0-9:]+\,(Standard Transfer),[^,]*,[^,]*,[^,]*,[^,]*,("- \$(([0-9.],?)+)"|- \$(([0-9.]+))).*
    "$2/$3/$1","$4","-$6$8"


## Uses
### Notepad++
[Notepad++](https://notepad-plus-plus.org/) is a free editor that can perform search and replacement using regular expressions. 

Open the replacement tool and copy/paste the seach and replacement text for each pair.
![Notepad++ screenshot](/assets/images/notepad++.jpg)

#### Encoding Limitation
Venmo exports in UTF-8 encoding and Notepad++ regular expression implementaiton of negative character classes will not match some UTF-8 characters such as emoji.  For this reason, you may need to convert the encoding to ANSI before making the replacements.
![Notepad++ screenshot](/assets/images/notepad++-encoding.jpg)

### PowerGREP
[PowerGrep](https://www.powergrep.com/) is a commercial utility that can run a series of expressions. This can execute the replacement on multiple files with one execution.
"Actions" can be loaded from [venmo-csv-to-simplifi.pga](assets%2Fvenmo-csv-to-simplifi.pga)
