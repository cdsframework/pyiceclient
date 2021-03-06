Python ICE Client (pyiceclient)
===============================

A Python 3 module to convert ICE web client data structure to vMR,
send the vMR to ICE, and parse the output vMR. See test_pyiceclient.py
for a usage example.

The example program test_pyiceclient.py takes a test.json file saved
by the ICE web client, and then uses the pyiceclient module to send it
through ICE, adds the evaluations and forecasts, and re-saves it as
test_out.json.

LICENSE
=======

See license in LICENSE file.

ICE Software
============

This module requires ICE server software to be accessible via a URL
specified in the send_request call.

The ICE server software is open source software with an open source
license available at www.cdsframework.org > ICE > Documentation > Open
Source License.

New ICE releases include schedule updates/new vaccines, new features,
and bug fixes; release notes are available at www.cdsframework.org >
ICE > Release Notes; and the software is available for download at
www.cdsframework.org > ICE > Downloads. 

ICE Web Client Data Structure
=============================

The ICE Web Client is a web client for ICE located at:

https://cds.hln.com/iceweb/

This module is based on the ICE web client data structure, which looks
like this:

```
    [
        {
            "id": "Patient ID Alphanumeric",
            "firstName": "First Name",
            "lastName": "Last Name",
            "gender": "M",
            "dob": "YYYYMMDD",
            "evalDate": "YYYYMMDD",
            "izs": [],
            "evaluations": [],
            "forecasts": []
        }
    ]
```

(Note that the ICE web client doesn't actually put evaluations and
forecasts in its data structure - but we use it here as part of this
module)

Where "izs" is a list of immunizations; each immunization is a list of:

* element 0: immunization id
* element 1: date of administration, YYYYMMDD
* element 2: code[: name] (e.g., "03: MMR") (name optional) (CVX for "I", ICD9/ICD10/SCT for "D")
* element 3: "I" (immunization) or "D" (disease) 

Where "evaluations" is a list of evaluations; each evaluation is a list of:

* element 0: immunization id
* element 1: date of administration, YYYYMMDD
* element 2: cvx_code (e.g., "03")
* element 3: vaccine group name (e.g., "Hep B Vaccine Group")
* element 4: validity ("true" or "false" or "unsupported")
* element 5: dose number in series (e.g., "1", "2", "3", etc., or "0" if unsupported)
* element 6  evaluation_code (e.g., "VALID")
* element 7: comma-separated evaluation_interpretation (e.g., "TOO_EARLY_LIVE_VIRUS,BELOW_MINIMUM_INTERVAL")
* element 8: evaluation_group_code (e.g., "100")

Where "forecasts" is a list of forecasts; each forecast is a list of:

* element 0: vaccine group name (e.g., "Hep B Vaccine Group")
* element 1: forecast concept (e.g., "FUTURE_RECOMMENDED")
* element 2: comma-separated forecast interpretation (e.g., "DUE_IN_FUTURE,HIGH_RISK")
* element 3: due date, YYYYMMDD
* element 4: forecast group code (e.g., "100")
* element 5: vaccine code recommended (CVX code, if any)
* element 6: earliest date, YYYYMMDD
* element 7: past due date, YYYYMMDD


The ICE Default Immunization Schedule
=====================================

The ICE Default Immunization schedule was developed by a group of
Subject Matter Experts, based on ACIP recommendations and informed by
CDC's Clinical Decision Support for Immunizations (CDSi) - but its
rules do differ in some ways from CDSi, and its output may not always
match what an individual clinician may expect. Users are advised to be
familiar with the rules and decisions documented at
www.cdsframework.org > ICE > Documentation > Default Immunization
Schedule, and, of course, to use their clinical judgement in
interpreting the recommendations.

Limitations
===========

* Does not perform error checking on input; for example, invalid CVX
  codes or invalid evidence of immunity codes will be passed to ICE
  as-is.

* Earliest date and Past Due date simply returns whatever ICE
  returns. Therefore, if the ICE server's
  **output_earliest_and_overdue_dates** setting in *ice.properties* is
  "N", the earliest date will always be empty and the past due date
  will always be equal to the due date. If the
  **output_earliest_and_overdue_dates** setting is "Y", then the
  earliest date and the past due date will be populated for supported
  vaccine groups (as of March 2018 they are Meningococcal ACWY, Polio,
  Rotavirus and Varicella) in cases where ICE calculates such dates,
  and will be empty otherwise.

* Immunizations that are unsupported by ICE will return a list item in the evaluations list as follows:

  * element 0: immunization id
  * element 1: date of administration, YYYYMMDD
  * element 2: cvx_code (e.g., "03")
  * element 3: vaccine group name ("Unsupported")
  * element 4: validity ("unsupported")
  * element 5: dose number in series ("0")
  * element 6  evaluation_code ("UNSUPPORTED")
  * element 7: comma-separated evaluation_interpretation ("")
  * element 8: evaluation_group_code ("0")


Installation
============

System:
-------

* Install a working Python 3.5+ environment with pip
* Install ICE on the localhost

Python:
-------

* pip install xmltodict
* pip install requests

This project:
-------------

* Download release and unzip to project directory, or git clone <project url>; cd into project directory
* Modify options in test_pyiceclient.py as needed
* Test:

```
   $ python test_pyiceclient.py
```
* Review output files

Install as module:
-----------------

```
python setup.py build
sudo python setup.py install
```
