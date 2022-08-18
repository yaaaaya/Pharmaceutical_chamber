# Pharmaceutical_chamber

### **Project objective:**
During the manufacturing of the tablets, the temperature of the tablets has to be maintained between -40 and -30 degrees Celsius. The government declared the following rules for manufacturing these tablets:

1) While the manufacturer is allowed to maintain the temperature of the tablets between, -40 and -30 degrees Celsius, the temperature of the tablets should never remain between -33 and -30 degrees for longer than 20 minutes at a time.

2) The manufacturer should maintain a log of when the cooling chamber for the production of the tablets is opened.
These rules should be implemented within a month of their declaration. Failing to comply would lead to cancellation of the pharmaceutical production permit. The government declared these rules to ensure that the medicine produced at all pharmaceutical companies, was good enough to be used by the general public.

**The objectives of the project are as follows:**

A. Build the circuit for temperature monitoring system, using the Bolt and LM35 sensor. 

B. Create a product on the Bolt Cloud, to monitor the data from the LM35, and link it to Bolt.

C. Write the product code, required to run the polynomial regression algorithm on the data sent by the Bolt.

D. Keep the temperature monitoring circuit inside your fridge with the door of the fridge closed, and let the system record the temperature readings for about 2 hours.

E. Using the reading that you received in the 2 hours, set boundaries for the temperature within the fridge.

F. Write a python code which will fetch the temperature data, every 10 seconds, and send out an sms alert, if the temperature goes beyond the temperature thresholds you decided on in objective "E".

G. Modify the python code, to also do a Z-score analysis and print the line “Someone has opened the fridge door” when an anomaly is detected.

H. Tune the Z-score analysis code, such that, it detects an anomaly when someone opens the door of the fridge.



**Hardware required:**

- 1 x Bolt IoT Module
- 1 x Micro USB Cable
- 1 x LM35 sensor
- 3 x Female to male wires


**Hardware connections:**

Step 1: Hold the sensor in a manner such that you can read LM35 written on it.

Step 2: Identify the pins of the sensor as VCC, Output and Gnd from your left to right.

VCC pin of the LM35 connects to 5v of the Bolt Wifi module.

Output pin of the LM35 connects to A0 (Analog input pin) of the Bolt Wifi module.

Gnd pin of the LM35 connects to the Gnd.

![WhatsApp Image 2022-08-08 at 3 37 45 AM](https://user-images.githubusercontent.com/98824642/185460786-bc0d0b8c-d734-4357-9b67-357f6196e6a9.jpeg)


LINK YOUR PRODUCT TO BOLT CLOUD. 

While creating the product, choose product type as Input Device and interface type as GPIO. After creating the product, select the recently created product and then click on configure icon.

In the hardware tab, select the radio button next to the A0 pin. Give the pin the name 'temp' and save the configuration.

![chamber hardware](https://user-images.githubusercontent.com/98824642/185460556-840eb57c-5fe6-4ed9-ba8c-bbccbe77f951.PNG)

Write the code in js to plot the temperature data and run the polynomial regression algorithm on the data, and save the product configurations.:
```

setChartLibrary('google-chart');

setChartTitle('Polynomial Regression');

setChartType('predictionGraph');

setAxisName('time_stamp','temp');

mul(0.0977);

plotChart('time_stamp','temp');
```

![polynomial regression](https://user-images.githubusercontent.com/98824642/185462366-7b606894-036d-42d4-b887-a2a0188c72ab.png)
![predict](https://user-images.githubusercontent.com/98824642/185462464-ebf4619e-0859-4b3e-8114-c70c92f4b412.PNG)

Python code:
We have to make a another file that will contain our credentials.

![Chamber code](https://user-images.githubusercontent.com/98824642/185462733-67572b58-9749-4d90-87fa-98be19d7b434.PNG)
![chamber code2](https://user-images.githubusercontent.com/98824642/185462764-c499e4b0-65b6-4f36-988e-3a75559b83f8.PNG)

Code for using Integromat:
```
def trigger_integromat_webhook():

    URL = "https://www.integromat.com/" # REPLACE WITH CORRECT URL
    
    response = requests.request("GET", URL)
    
    print response.text
    
    

Code for using telegram:

"""Configurations for telegram_alert.py"""

bolt_api_key = "XXXX"                 # This is your Bolt Cloud API Key

device_id = "XXXX"                    # This is the device ID and will be similar to BOLTXXXX where XXXX is some numbers

telegram_chat_id = "@XXXX"            # This is the channel ID of the created Telegram channel. Paste after @ symbol.

telegram_bot_id = "botXXXX"           # This is the bot ID of the created Telegram Bot. Paste after bot text.

threshold = 250                       # Threshold beyond which the alert should be sent



def send_telegram_message(message):

    """Sends message via Telegram"""
    
    url = "https://api.telegram.org/" + conf.telegram_bot_id + "/sendMessage"
    
    data = {
    
        "chat_id": conf.telegram_chat_id,
        
        "text": message
        
    }
    
    try:
    
        response = requests.request(
        
            "POST",
            
            url,
            
            params=data
            
        )
        
        print("This is the Telegram URL")
        
        print(url)
        
        print("This is the Telegram response")
        
        print(response.text)
        
        telegram_data = json.loads(response.text)
        
        return telegram_data["ok"]
        
    except Exception as e:
    
        print("An error occurred in sending the alert message via Telegram")
        
        print(e)
        
        return False
```
        

Code for using mailgun (email):

```
MAILGUN_API_KEY = 'This is the private API key which you can find on your Mailgun Dashboard' 

SANDBOX_URL= 'You can find this on your Mailgun Dashboard' 

SENDER_EMAIL = 'test@' + SANDBOX_URL  # No need to modify this. The sandbox URL is of the format test@YOUR_SANDBOX_URL

RECIPIENT_EMAIL = 'Enter your Email ID Here'

API_KEY = 'This is your Bolt Cloud account API key'

DEVICE_ID = 'This is the ID of your Bolt device' 
```


```
import email_conf

from boltiot import Email, Bolt

mybolt = Bolt(email_conf.API_KEY, email_conf.DEVICE_ID)

mailer = Email(email_conf.MAILGUN_API_KEY, email_conf.SANDBOX_URL, email_conf.SENDER_EMAIL, email_conf.RECIPIENT_EMAIL)

response = mailer.send_email("Alert", "The Current temperature sensor value is " +str(sensor_value))

```



Code using twilio(sms):

```
SID = 'You can find SID in your Twilio Dashboard' 

AUTH_TOKEN = 'You can find  on your Twilio Dashboard' 

FROM_NUMBER = 'This is the no. generated by Twilio. You can find this on your Twilio Dashboard'

TO_NUMBER = 'This is your number. Make sure you are adding +91 in beginning'

API_KEY = 'This is your Bolt Cloud accout API key'

DEVICE_ID = 'This is the ID of your Bolt device' 
```


Major changes in code:

```
import conf

from boltiot import Sms, Bolt

import json, time

mybolt = Bolt(conf.API_KEY, conf.DEVICE_ID)

sms = Sms(conf.SID, conf.AUTH_TOKEN, conf.TO_NUMBER, conf.FROM_NUMBER)

response = sms.send_sms("The Current temperature sensor value is " +str(sensor_value))
```



For Z-SCORE analysis:

 ```
sudo apt-get update
 
 sudo pip3 install boltiot
 
 sudo pip3 install pyOpenSSL ndg-httpsclient pyasn1
 

import conf, json, time, math, statistics

from boltiot import Sms, Bolt

def compute_bounds(history_data,frame_size,factor):
    if len(history_data)<frame_size :
        return None

    if len(history_data)>frame_size :
        del history_data[0:len(history_data)-frame_size]
    Mn=statistics.mean(history_data)
    Variance=0
    for data in history_data :
        Variance += math.pow((data-Mn),2)
    Zn = factor * math.sqrt(Variance / frame_size)
    High_bound = history_data[frame_size-1]+Zn
    Low_bound = history_data[frame_size-1]-Zn
    return [High_bound,Low_bound]

minimum_limit = 115
maximum_limit = 135

mybolt = Bolt(conf.API_KEY, conf.DEVICE_ID)

sms = Sms(conf.SID, conf.AUTH_TOKEN, conf.TO_NUMBER, conf.FROM_NUMBER)

(optional)

def get_sensor_value_from_pin(pin):
   """Returns the sensor value. Returns -999 if request fails"""
    try:
         response = mybolt.analogRead(pin)
         data = json.loads(response)
         if data["success"] != 1:
            print("Request not successfull")
            print("This is the response->", data)
            return -999
        sensor_value = int(data["value"])
        return sensor_value
    except Exception as e:
         print("Something went wrong when returning the sensor value")
        print(e)
       return -999
 
 def send_telegram_message(message):
     """Sends message via Telegram"""
    url = "https://api.telegram.org/" + conf.telegram_bot_id + "/sendMessage"
     data = {
         "chat_id": conf.telegram_chat_id,
         "text": message
     }
     try:
         response = requests.request(
             "POST",
            url,
             params=data
         )
         print("This is the Telegram URL")
         print(url)
         print("This is the Telegram response")
         print(response.text)
         telegram_data = json.loads(response.text)
         return telegram_data["ok"]
     except Exception as e:
         print("An error occurred in sending the alert message via Telegram")
         print(e)
        return False
history_data=[]

while True:
    response = mybolt.analogRead('A0')
    data = json.loads(response)
    if data['success'] != 1:
        print("There was an error while retriving the data.")
        print("This is the error:"+data['value'])
        time.sleep(10)
        continue

    print ("This is the value "+data['value'])
    sensor_value=0
    try:
        sensor_value = int(data['value'])
    except e:
        print("There was an error while parsing the response: ",e)
        continue

    bound = compute_bounds(history_data,conf.FRAME_SIZE,conf.MUL_FACTOR)
    if not bound:
        required_data_count=conf.FRAME_SIZE-len(history_data)
        print("Not enough data to compute Z-score. Need ",required_data_count," more data points")
        history_data.append(int(data['value']))
        time.sleep(10)
        continue

    try:
        if sensor_value > bound[0] :
            print ("The light level increased suddenly. Sending an SMS.")
            response = sms.send_sms("Someone turned on the lights")
            print("This is the response ",response)
        elif sensor_value < bound[1]:
            print ("The light level decreased suddenly. Sending an SMS.")
            response = sms.send_sms("Someone turned off the lights")
            print("This is the response ",response)
        history_data.append(sensor_value)
    except Exception as e:
        print ("Error",e)
    time.sleep(10)
```
![WhatsApp Image 2022-08-08 at 3 37 49 AM](https://user-images.githubusercontent.com/98824642/185481186-b923542a-0fdf-4c54-9629-9ccaed2ab6ac.jpeg)
![WhatsApp Image 2022-08-19 at 1 14 42 AM](https://user-images.githubusercontent.com/98824642/185481422-4fefad5c-e73d-485c-ba98-ab0747fd642c.jpeg)
