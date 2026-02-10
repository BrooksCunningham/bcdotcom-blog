---
layout: post
title: "Customize your monitoring with the XenDesktop Director API and Python"
date: 2018-09-15 00:00:00 -0600
---

On a day-to-day basis, I assist with the operations of a Citrix environment containing over 100 individual XenDesktop sites. The challenge is that Director only displays a single site at a time, requiring manual navigation through each location to identify failure events.

## Solution Overview

I developed a Python script that queries all XML Brokers for current failure counts. When failures are detected (count > 0), Internet Explorer automatically launches to the affected site for further investigation. The script iterates continuously until manually stopped via CTRL+C.

**Key Features:**

- Queries the endpoint: `/Citrix/Monitor/OData/v2/Data/FailureLogSummaries`
- 30-second pause when failures are detected
- 5-second intervals between probes
- Reads DDC list from text file (default: `D:\temp\ddcFile.txt`)
- Uses NTLM authentication

## Python Code

```python
import requests
import time
import xml.etree.ElementTree as ET
import requests.auth
from requests_ntlm import HttpNtlmAuth
import getpass
import webbrowser

#use this for username\password

username = raw_input("Enter Domain\\Username :")
password = getpass.getpass("Enter Password :")

#xml namespaces
ns = {'default': "http://www.w3.org/2005/Atom",
    'base': "http://192.168.0.112/Citrix/Monitor/OData/v2/Data/",
    'd': "http://schemas.microsoft.com/ado/2007/08/dataservices",
    'm': "http://schemas.microsoft.com/ado/2007/08/dataservices/metadata"}

#Used for automatically launching IE when a failure is detected.
ie = webbrowser.get(webbrowser.iexplore)

class DdcMonitor:
    def main(self):
        while True:
            #opens the file with the list of DDCs
            ddcFile = open('D:\\temp\\ddcFile.txt', 'r')
            for ddcFQDN in ddcFile:
                #uses HTTP. HTTPS could be added if needed.
                directorURL = "http://" + ddcFQDN.rstrip("\n") + "/Citrix/Monitor/OData/v2/Data/FailureLogSummaries"
                print("Now probing : " + str(directorURL))
                #Connection information
                #here is an example of a constructed query
                #directorURL = "http://192.168.0.112/Citrix/Monitor/OData/v2/Data/FailureLogSummaries"
                directorSession = requests.session()
                directorSession.auth = HttpNtlmAuth(username,password)
                directorReqData = directorSession.get(directorURL)

                #XML information
                root = ET.fromstring(directorReqData._content)
                entry = root.find('default:entry', ns)
                sub_1 = entry.find("default:content", ns)
                for sub_2 in sub_1.find("m:properties", ns):
                   if "FailureCount" in str(sub_2.tag):
                       if int(sub_2.text) > 0:
                           print("")
                           print("The Failure Count is increasing at " + directorURL)
                           print("The error count is currently :   " + sub_2.text)
                           print("Waiting 30 seconds")
                           ie.open('http://' + ddcFQDN + "/director")
                           print("")
                           time.sleep(30)
                       else:
                           print("The Failure Count is not increasing at " + directorURL)

                print("The probe will run again in 5 seconds")
                print("")
                print("")
                time.sleep(5)

            ddcFile.close()
            time.sleep(1)

try:
    DdcMonitor().main()
    print("the program is no longer running")
except:
    print("Something caused the program to stop. Please restart the program")
```

## Configuration

The DDC file lists brokers line-by-line:

```
ddc1.mycompany.net
ddc2.mycompany.net
ddc3.mycompany.net
```

For more information on the OData wrapper, check out the Citrix eDocs documentation.

**Disclaimer:** This software / sample code is provided to you "AS IS" with no representations, warranties or conditions of any kind.
