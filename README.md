# Leveraging The EskomSePush Business API

[<img src="img/logo.png" width="500"/>](img/logo.png)

---

*Developed by C.A Torino, TECHRAD Radical Technology*
* Links to TECHRAD ZA.
    * [Website](https://www.techrad.co.za)
    * [Tutorial website](https://tutorials.techrad.co.za)
    * [Support website](https://support.techrad.co.za)
    * [API](https://www.techrad.co.za/apisource/public/apps/fusio)

# Summary

Info on API version:`2.0` as of 16th September 2022

Official documentation found [here](https://documenter.getpostman.com/view/1296288/UzQuNk3E)

Below I summarize the most useful info and give my opinion on the pros and cons.

There are a lot of rules that apply to using this API however it is very convenient to use
compared to the Eskom vanilla API.

I suspect we all have to get our basic info from Eskoms API however the API offered from EskomSePush
goes the extra mile for it's users so I would recommend using it within the parameters if it
is viable for your solution

For business users i suggest you take a close look at the Master Service Agreement

# Index
- Summary
- Index
- From the postman doc
- Usage Examples
- Endpoint API Calls
    - Status
    - Area Information
    - Areas Nearby (GPS)
    - Areas Search (Text)
    - Topics Nearby
    - Check allowance


---

# From the postman doc

### Business Users
- You are governed by the terms in the MSA

### Personal Users
- By using this API you agree to all terms and conditions.
- This is invite only, and you can get removed at any time
- This API can change at any moment. We'll try to alert you to big changes
- Use any data at your own risk, we take no responsibility for any inaccuracies or errors.

### Warnings
- Each Token is allocated a Quota (typically Daily usage); once this usage has been exceeded; requests will be blocked until quota is available
- Do not share your Token
- You should only be doing requests from a single IP (not multiple simultaneously)
- Each individual must have their own Token linked to a valid email address
- You cannot build a competing product to EskomSePush/AskMyStreet with this API (Got a cool idea? Let's chat!)

### Where do I get a Token
- Someone at EskomSePush will email your Token to you

### My Token has stopped working
- If we detect malicious behaviour or anything we deem to be untoward we will disable a personal Token without notice or warning
- You can email "personal_support at sepush co za" for best effort & low priority support
- We track usage in real-time and also long term

### Personal Quotas
- Personal API users have a quota, and once this quota is used up in a period (typically daily), requests will be blocked until quota is available again
- Status & Area Information count for a single request
- Area Information has a ?test parameter; which does not count toward your quota. Use it :)
- Areas Search, Areas Nearby & Topics Nearby count as 5 calls toward your request quota
    - These don't need to be called automatically or often
- The Check Allowance can be called to check your quota used & remaining. This doesn't count against your quota (use respectfully).

### Responses
All valid responses will have Header `Content-Type: application/json`

It's important to check the HTTP status code to confirm accuracy of your response before processing it.

| HTTP Code | Description |
| :------------ | :------------ |
|`200`      |OK                                           |
|`400`      |Bad Request (You sent something bad)         |
|`403`      |Not Authenticated (Token Invalid / Disabled) |
|`404`      |Not Found                                    |
|`425`      |Too Many Requests (Token quota exceeded)     |
|`5xx`      |Server side issue - did you let us know?     |

### Authentication
Add the following HTTP request headers, for all requests against the API.

| Header | value |
| :------------ | :------------ |
|`Token`        |               |

### Request Results Example 
```
Accept	application/json, text/plain, */*
Authorization	Basic tokengoeshere
Content-Type	application/json
```

---

# Usage Examples

## PHP cURL
```PHP
<?php
$Token = "YourTokengoeshere";
$curl = curl_init();
curl_setopt_array($curl, array(
  CURLOPT_URL => "https://developer.sepush.co.za/business/2.0/status",
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_ENCODING => "",
  CURLOPT_MAXREDIRS => 10,
  CURLOPT_TIMEOUT => 0,
  CURLOPT_FOLLOWLOCATION => true,
  CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
  CURLOPT_CUSTOMREQUEST => "GET",
  CURLOPT_HTTPHEADER => array(
  "Content-Type: application/json",
  "Token: $Token"
  ),
));
$response = curl_exec($curl);
curl_close($curl);
echo $response;
```

## JavaScript-XHR
```JS
// WARNING: For GET requests, body is set to null by browsers.
var xhr = new XMLHttpRequest();
var Token = new "YourTokengoeshere";
xhr.withCredentials = true;
xhr.addEventListener("readystatechange", function() {
  if(this.readyState === 4) {
    console.log(this.responseText);
  }
});
xhr.open("GET", "https://developer.sepush.co.za/business/2.0/status");
xhr.setRequestHeader('Content-Type', 'application/json;charset=UTF-8');//application/json;charset=UTF-8
xhr.setRequestHeader('Token', Token);//application/json;charset=UTF-8
xhr.send();
```

## C#
```C#
private async void Test_button_Click(object sender, EventArgs e)
{
    string Token = "YourTokengoeshere";
    Memo_richTextBox.Clear();
    using (HttpClient client = new HttpClient())
    {
        //Add Default Request Headers
        client.DefaultRequestHeaders.Add("Token", $"{Token}");
        try
        {
            using (HttpResponseMessage response = await client.GetAsync(new Uri("https://developer.sepush.co.za/business/2.0/status")))
            {
                using (HttpContent content = response.Content)
                {
                    //Read the result and display in Textbox
                    string result = await content.ReadAsStringAsync();//Result string JSON
                    string reasonPhrase = response.ReasonPhrase;//Reason OK, FAIL etc.
                    Memo_richTextBox.AppendText(result + Environment.NewLine);
                    Memo_richTextBox.AppendText(reasonPhrase + Environment.NewLine);
                }
            }
        }
        catch (Exception ex)
        {
            MessageBox.Show(ex.Message, "Could not test API", MessageBoxButtons.OK, MessageBoxIcon.Error);
            LoggerClass.WriteLine(" *** Error:" + ex.Message + " [MainForm] ***");
            return;
        }
    }
}
```

# Endpoint API Calls

## 1. Status

`Get status of loadshedding` [*Check if there is any loadshedding*]

-------------------

### Calling Parameters (Input)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`NULL`      |NULL |NULL      |NULL |

### Interface Address

https://developer.sepush.co.za/business/2.0/status

### Request Method

- HTTP 
- GET

### Response Parameters (Output)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`name`                     |string      |Https        |Cape Town                   |
|`stage`                    |string      |Https        |1                           |
|`stage_start_timestamp`    |string      |Https        |2022-08-08T17:00:00+02:00   |
|`stage_updated`            |string      |Https        |2022-08-08T22:00:00+02:00   |

### Example:

- Returned data: 
   - A nested Json array containing the: status, name, stage, next stages, start time and stage updated time.
- Example: 
   - stage:0 = No load shedding,
   - stage:1 = Stage 1
   - stage:2 = Stage 2
   - stage:3 = Stage 3
   - stage:4 = Stage 4


### Response Result Example
```JSON
{
    "status": {
        "capetown": {
            "name": "Cape Town",
            "next_stages": [
                {
                    "stage": "1",
                    "stage_start_timestamp": "2022-08-08T17:00:00+02:00"
                },
                {
                    "stage": "0",
                    "stage_start_timestamp": "2022-08-08T22:00:00+02:00"
                }
            ],
            "stage": "0",
            "stage_updated": "2022-08-08T00:08:16.837063+02:00"
        },
        "eskom": {
            "name": "National",
            "next_stages": [
                {
                    "stage": "2",
                    "stage_start_timestamp": "2022-08-08T16:00:00+02:00"
                },
                {
                    "stage": "0",
                    "stage_start_timestamp": "2022-08-09T00:00:00+02:00"
                }
            ],
            "stage": "0",
            "stage_updated": "2022-08-08T16:12:53.725852+02:00"
        }
    }
}


```

--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------

## 2. Area Information

`Area Information` Obtain the id from Area Find or Area Search and use with this request. 

This single request has everything you need to monitor upcoming loadshedding events for the chosen suburb.

-------------------

### Calling Parameters (Input)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`id`      |string |Https      |eskde-10-fourwaysext10cityofjohannesburggauteng |
|`test`    |string |Https      |current                                         |

### Interface Address

https://developer.sepush.co.za/business/2.0/area?id=eskde-10-fourwaysext10cityofjohannesburggauteng

### Request Method

- HTTP 
- GET

### Response Parameters (Output)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`end`      |string      |Https        |2022-08-08T22:30:00+02:00                   |
|`note`     |string      |Https        |Stage 2                                     |
|`start`    |string      |Https        |2022-08-08T20:00:00+02:00                   |
|`name`     |string      |Https        |Sandton-WEST (4)                            |
|`region`   |string      |Https        |Eskom Direct, City of Johannesburg, Gauteng |
|`date`     |string      |Https        |2022-08-08                                  |
|`stages`   |array       |Https        |"12:00-14:30",                              |

### Example:

- `name` & `region`
    - Self-explanatory
- `events`
    - A sorted list of events
    - `start` & `end` times listing when it will be impacted by loadshedding
    - Will be an empty list if not impacted
- `schedule`
    - Raw loadshedding schedule, per stage (1-8)
    - Formatted for display purposes (i.e. `20:00-22:30`)
    - Any adjacent events have been merged into a single event (e.g. `12:00-14:30` & `14:00-16:30` become `12:00-16:30`)
    - _Note: An empty list means no events for that stage_
    - _Note: Some Municipalities/Regions don't have Stage 5-8 schedules (and there will be 4 records instead of 8 in this list._
    _Stage 5 upwards you can assume Stage 4 schedule impact._

### Testing

Include the `&test=current` or `&test=future` to get SAMPLE data returned in the `events`. 
`current` will return a loadshedding `event` which is occurring right now, and `future` will return an event starting on the next hour.

_NOTE: The schedule returned with testing data is NOT accurate data; but only for testing purposes._
_The area `name` and `source` is updated to identify that this is testing data. This `test` request will not count towards your quota._


### Response Result Example
```JSON
{
    "events": [
        {
            "end": "2022-08-08T22:30:00+02:00",
            "note": "Stage 2",
            "start": "2022-08-08T20:00:00+02:00"
        }
    ],
    "info": {
        "name": "Sandton-WEST (4)",
        "region": "Eskom Direct, City of Johannesburg, Gauteng"
    },
    "schedule": {
        "days": [
            {
                "date": "2022-08-08",
                "name": "Monday",
                "stages": [
                    [],
                    [
                        "20:00-22:30"
                    ],
                    [
                        "12:00-14:30",
                        "20:00-22:30"
                    ],
                    [
                        "04:00-06:30",
                        "12:00-14:30",
                        "20:00-22:30"
                    ],
                    [
                        "04:00-06:30",
                        "12:00-14:30",
                        "20:00-22:30"
                    ],
                    [
                        "04:00-06:30",
                        "12:00-14:30",
                        "20:00-00:30"
                    ],
                    [
                        "04:00-06:30",
                        "12:00-16:30",
                        "20:00-00:30"
                    ],
                    [
                        "04:00-08:30",
                        "12:00-16:30",
                        "20:00-00:30"
                    ]
                ]
            },
            {
                "date": "2022-08-09",
                "name": "Tuesday",
                "stages": [
                    [
                        "02:00-04:30"
                    ],
                    [
                        "02:00-04:30"
                    ],
                    [
                        "02:00-04:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-12:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-06:30",
                        "10:00-12:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-06:30",
                        "10:00-12:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-06:30",
                        "10:00-12:30",
                        "18:00-22:30"
                    ],
                    [
                        "02:00-06:30",
                        "10:00-14:30",
                        "18:00-22:30"
                    ]
                ]
            },
            {
                "date": "2022-08-10",
                "name": "Wednesday",
                "stages": [
                    [
                        "10:00-12:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-12:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-12:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-12:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-14:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-06:30",
                        "10:00-14:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-06:30",
                        "10:00-14:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-06:30",
                        "10:00-14:30",
                        "18:00-22:30"
                    ]
                ]
            },
            {
                "date": "2022-08-11",
                "name": "Thursday",
                "stages": [
                    [
                        "18:00-20:30"
                    ],
                    [
                        "10:00-12:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-12:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-12:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-12:30",
                        "18:00-22:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-14:30",
                        "18:00-22:30"
                    ],
                    [
                        "02:00-06:30",
                        "10:00-14:30",
                        "18:00-22:30"
                    ],
                    [
                        "02:00-06:30",
                        "10:00-14:30",
                        "18:00-22:30"
                    ]
                ]
            },
            {
                "date": "2022-08-12",
                "name": "Friday",
                "stages": [
                    [],
                    [
                        "18:00-20:30"
                    ],
                    [
                        "10:00-12:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-12:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-12:30",
                        "18:00-20:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-12:30",
                        "18:00-22:30"
                    ],
                    [
                        "02:00-04:30",
                        "10:00-14:30",
                        "18:00-22:30"
                    ],
                    [
                        "02:00-06:30",
                        "10:00-14:30",
                        "18:00-22:30"
                    ]
                ]
            }
        ],
        "source": "https://loadshedding.eskom.co.za/"
    }
}


```

--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------

## 3. Areas Nearby (GPS)

`Areas Nearby (GPS)`Find areas based on GPS coordinates (latitude and longitude). 

These are recommended areas based on EskomSePush users adding locations nearby to those coordinates.

The first area returned is typically the best choice for the coordinates - as it's the most popular used.

-------------------

### Calling Parameters (Input)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`lat`      |long |Https      |-26.0269658 |
|`lon`      |long |Https      |28.0137339 |

### Interface Address

https://developer.sepush.co.za/business/2.0/areas_nearby?lat=-26.0269658&lon=28.0137339

### Request Method

- HTTP 
- GET

### Response Parameters (Output)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`areas` |array      |Https        |[]                      |
|`count` |string     |Https        |253                     |
|`id`    |string     |Https        |capetown-15-observatory |


### Example:

- Returned data: 
   - A nested Json array containing the: count, and id of areas.
- Example: 
   - "id": "capetown-15-observatory" = area name
   - "count": 253 = count number


### Response Result Example
```JSON
{
    "areas": [
        {
            "count": 253,
            "id": "capetown-15-observatory"
        },
        {
            "count": 82,
            "id": "capetown-15-saltriver"
        },
        {
            "count": 80,
            "id": "capetown-15-woodstockeast"
        },
        {
            "count": 67,
            "id": "capetown-15-rondebosch"
        },
        {
            "count": 62,
            "id": "capetown-7-woodstockwest"
        }
    ]
}


```

--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------

## 4. Areas Search (Text)

`Areas Search (Text)`Search area based on a text query. 

-------------------

### Calling Parameters (Input)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`text`      |string |Https      |fourways |

### Interface Address

https://developer.sepush.co.za/business/2.0/areas_search?text=fourways

### Request Method

- HTTP 
- GET

### Response Parameters (Output)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`areas`    |array      |Https        |[]                                       |
|`id`       |string     |Https        |westerncape-2-stellenboschmunicipality   |
|`name`     |string     |Https        |Stellenbosch Municipality (2)            |
|`region`   |string     |Https        |Western Cape                             |


### Example:

- Returned data: 
   - A nested Json array containing the: id, name and region of areas.


### Response Result Example
```JSON
{
    "areas": [
        {
            "id": "westerncape-2-stellenboschmunicipality",
            "name": "Stellenbosch Municipality (2)",
            "region": "Western Cape"
        },
        {
            "id": "westerncape-8-stellenboschfarmers",
            "name": "Stellenbosch farmers (8)",
            "region": "Western Cape"
        },
        {
            "id": "eskomdirect-5215-stellenboschpart1outlyingstellenboschwesterncape",
            "name": "Stellenbosch Part 1 Outlying",
            "region": "Eskom Direct (Web), Stellenbosch, Western Cape"
        },
        {
            "id": "eskde-4-stellenboschnucityofcapetownwesterncape",
            "name": "Stellenbosch NU (4)",
            "region": "Eskom Direct, City of Cape Town, Western Cape"
        },
        {
            "id": "eskde-4-stellenboschnustellenboschwesterncape",
            "name": "Stellenbosch NU (4)",
            "region": "Eskom Direct, Stellenbosch, Western Cape"
        },
        {
            "id": "eskde-8-stellenboschnustellenboschwesterncape",
            "name": "Stellenbosch NU (8)",
            "region": "Eskom Direct, Stellenbosch, Western Cape"
        },
        {
            "id": "eskde-15-stellenboschnucityofcapetownwesterncape",
            "name": "Stellenbosch NU (15)",
            "region": "Eskom Direct, City of Cape Town, Western Cape"
        },
        {
            "id": "eskde-15-stellenboschnustellenboschwesterncape",
            "name": "Stellenbosch NU (15)",
            "region": "Eskom Direct, Stellenbosch, Western Cape"
        },
        {
            "id": "eskde-16-stellenboschmohokarefreestate",
            "name": "Stellenbosch (16)",
            "region": "Eskom Direct, Mohokare, Free State"
        },
        {
            "id": "eskde-16-stellenboschnustellenboschwesterncape",
            "name": "Stellenbosch NU (16)",
            "region": "Eskom Direct, Stellenbosch, Western Cape"
        },
        {
            "id": "eskde-16-stellengiftdrakensteinwesterncape",
            "name": "Stellengift (16)",
            "region": "Eskom Direct, Drakenstein, Western Cape"
        },
        {
            "id": "eskdo-5-stellenboschvleibeaufortwestwesterncape",
            "name": "Stellenboschvlei (5)",
            "region": "Eskom Direct, Beaufort West, Western Cape"
        },
        {
            "id": "eskdo-9-stellenhofsundaysrivervalleyeasterncape",
            "name": "Stellenhof (9)",
            "region": "Eskom Direct, Sundays River Valley, Eastern Cape"
        },
        {
            "id": "westerncape-2-universityofstellenbosch",
            "name": "University of Stellenbosch (2)",
            "region": "Western Cape"
        }
    ]
}


```

--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------

## 5. Topics Nearby

`Topics Nearby` Find topics created by users based on GPS coordinates (latitude and longitude). Can use this to detect if there is a potential outage/problem nearby.

-------------------

### Calling Parameters (Input)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`lat`      |long |Https      |-26.0269658 |
|`lon`      |long |Https      |28.0137339 |

### Interface Address

https://developer.sepush.co.za/business/2.0/topics_nearby?lat=-26.0269658&lon=28.0137339

### Request Method

- HTTP 
- GET

### Response Parameters (Output)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`topics`    |array      |Https        |[]                                |
|`active`    |string     |Https        |2022-08-08T13:02:04.776000+02:00  |
|`body`      |string     |Https        |testing sentance                  |
|`category`  |string     |Https        |electricity                       |
|`distance`  |long       |Https        |4.83                              |
|`followers` |int        |Https        |1                                 |
|`timestamp` |string     |Https        |2022-08-08T13:02:04.776000+02:00  |


### Example:

- Returned data: 
   - A nested Json array containing the: active, body, category, distance, followers, timestamp, of topics.


### Response Result Example
```JSON
{
    "topics": [
        {
            "active": "2022-08-08T13:02:04.776000+02:00",
            "body": "Is anyone else still off in Parkhurst? According to the city power Twitter page electricity has been restored but we're out on 11th",
            "category": "electricity",
            "distance": 4.83,
            "followers": 1,
            "timestamp": "2022-08-08T13:02:04.776000+02:00"
        },
        {
            "active": "2022-08-08T10:08:32.229000+02:00",
            "body": "Anyone selling laptops",
            "category": "information",
            "distance": 3.48,
            "followers": 3,
            "timestamp": "2022-08-07T10:43:25.319000+02:00"
        },
        {
            "active": "2022-08-08T10:02:52.791000+02:00",
            "body": "Hi , if anyone is in need of a dog sitter / baby sitter I am very flexible and able to travel. I live around the  area \nContact me on  x \nMy name is ",
            "category": "information",
            "distance": 1.8,
            "followers": 3,
            "timestamp": "2022-08-08T09:48:02.819000+02:00"
        }
    ]
}



```

--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------


## 6. Check allowance

`Check allowance` Check allowance allocated for token

NOTE: This call doesn't count towards your quota.

-------------------

### Calling Parameters (Input)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`null`      |null |null      |null |

### Interface Address

https://developer.sepush.co.za/business/2.0/api_allowance

### Request Method

- HTTP 
- GET

### Response Parameters (Output)
| Parameter  |  Mode  | Description  | example values  |
| :------------ | :------------ | :------------ | :------------ |
|`count` |int     |Https        |39     |
|`limit` |int     |Https        |50     |
|`type`  |string  |Https        |daily  |


### Example:

- Returned data: 
   - A Json containing the: count, limit, type.


### Response Result Example
```JSON
{
    "allowance": {
        "count": 39,
        "limit": 50,
        "type": "daily"
    }
}



```

--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------------------------------
