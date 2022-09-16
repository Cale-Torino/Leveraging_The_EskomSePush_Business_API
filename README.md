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
- POST

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
