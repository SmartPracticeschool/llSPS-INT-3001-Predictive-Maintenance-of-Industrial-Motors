# llSPS-INT-3001-Predictive-Maintenance-of-Industrial-Motors
Predictive Maintenance of Industrial Motors
Source code
import time
import sys
import ibmiotf.application
import ibmiotf.device
import random
import requests
#Provide your IBM Watson Device Credentials
organization = "2u4wmg"
deviceType = "rasp"
deviceId = "12345"
authMethod = "token"
authToken = "123456789"


def myCommandCallback(cmd):
        print("Command received: %s" % cmd.data['command'])

        if cmd.data['command']=='motoron':
                print("Motor ON IS RECEIVED")


        elif cmd.data['command']=='motoroff':
                print("MOTOR OFF IS RECEIVED")


try:
	deviceOptions = {"org": organization, "type": deviceType, "id": deviceId, "auth-method": authMethod, "auth-token": authToken}
	deviceCli = ibmiotf.device.Client(deviceOptions)
	#..............................................
	
except Exception as e:
	print("Caught exception connecting device: %s" % str(e))
	sys.exit()

# Connect and send a datapoint "hello" with value "world" into the cloud as an event of type "greeting" 10 times
deviceCli.connect()

while True:

        hum=random.randint(0, 100)
        #print(hum)
        temp =random.randint(0, 100)
        vibration =random.randint(0, 100)
        current =random.randint(0, 100)
        #Send Temperature & Humidity to IBM Watson
        data = { 'Temperature' : temp, 'Humidity': hum, 'vibration':vibration, 'current':current }
        #print (data)
        def myOnPublishCallback():
            print ("Published Temperature = %s C" % temp, "Humidity = %s %%" % hum, "vibration = %s %%" % vibration, "current = %s %%" % current, "to IBM Watson")

        success = deviceCli.publishEvent("Weather", "json", data, qos=0, on_publish=myOnPublishCallback)
        if(temp<50):
                r = requests.get('https://www.fast2sms.com/dev/bulk?authorization=dqF1h94caTEzStImNWUD0gx3vQjrwsKLRpOoeilJ85PMVkYXGnMOEUrL8KiwAPa0QxhoctVgkmXlCdWs&sender_id=FSTSMS&message=Temperature is low...please switch on the motor.&language=english&route=p&numbers=7999697504')
                print(r.status_code)
        if(hum<50):
                r = requests.get('https://www.fast2sms.com/dev/bulk?authorization=dqF1h94caTEzStImNWUD0gx3vQjrwsKLRpOoeilJ85PMVkYXGnMOEUrL8KiwAPa0QxhoctVgkmXlCdWs&sender_id=FSTSMS&message=Humidity is low ....please switch on the motor.&language=english&route=p&numbers=7999697504')
                print(r.status_code)
        if(vibration<50):
                r = requests.get('https://www.fast2sms.com/dev/bulk?authorization=dqF1h94caTEzStImNWUD0gx3vQjrwsKLRpOoeilJ85PMVkYXGnMOEUrL8KiwAPa0QxhoctVgkmXlCdWs&sender_id=FSTSMS&message=The motor vibration is too high...&language=english&route=p&numbers=7999697504')
                print(r.status_code)
        if(current<50):
                r = requests.get('https://www.fast2sms.com/dev/bulk?authorization=dqF1h94caTEzStImNWUD0gx3vQjrwsKLRpOoeilJ85PMVkYXGnMOEUrL8KiwAPa0QxhoctVgkmXlCdWs&sender_id=FSTSMS&message=Current consumption is high.&language=english&route=p&numbers=7999697504')
                print(r.status_code)
        if not success:
            print("Not connected to IoTF")
        time.sleep(2)

        deviceCli.commandCallback = myCommandCallback

# Disconnect the device and application from the cloud
deviceCli.disconnect()
















                       











