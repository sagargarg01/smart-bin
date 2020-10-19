# iot-based-smart-bin
This is basically a smart dustbin which notifies its user when its half and full by sending an email on registered user email id and also gives hands free experience by automatic flap opening system.
## contributors
* Harsh Verma 
* Sagar Garg
## Idea for the project
As we all have seen large mettalic dustbins of municipal corporation in different parts of a city, people after collecting their waste from their houses collectively dumped that waste in these dustbin ** BUT ** after some time when these dustbins reached to their full capacity and there is no space left for more trash people started to their their waste region around the dustbins and due to this a huge pile of waste having foul smell is created around that area. So for this we have created this smart dustbin system , after installing this system in those dustbins it can notify the municipal corporation about the particular dustbin which is fulled on timely basis and then they can easily empty the dustbin by taking out the waste.
## modifications for future
* we are thinking to upload all the data to the cloud platforms like thingspeak so that we can monitor the waste productions in different parts of cities and can monitor easily the waste production rate and can increase the frequency of dumping trucks in those regions so that they can clean those areas more frequently
## Challenges
Opting for sensors that can also work in wet waste. Presently we are using infrared sensors to detect whether the dustbin is full or not but they can work good for dry waste only so opting sensors working for both wet and dry waste will be a task.
## sensors and board used for the projects
* Raspberry pi 3B+
* ultrasonic sensor
* infrared sensors
* servo motor
* bin body

## code
### block of code for sending mail
```
def mail():
    global flag1
    i=GPIO.input(10)
    j=GPIO.input(8)
    if i==1 and j==1:
        if flag1==2:
            smtpuser = "your email id"
            smtppass = "password"

            toadd = "email on which file to be sent"
            fromadd = smtpuser
            subject = "Python Test"
            header = "To: "+toadd+'\n'+"From: "+fromadd+'\n'+"Subject: "+subject+'\n'
            body = "full"
            message = header+'\n'+body
    
            print(message)

            s = smtplib.SMTP("smtp.gmail.com",587)

            s.ehlo()
            s.starttls()
            s.ehlo()
            
            s.login(smtpuser, smtppass)
            s.sendmail(fromadd, toadd, message)
            s.quit()
            flag1=3
    elif i==1 and j==0:
        if flag==1:
            smtpuser = "your email id"
            smtppass = "password"

            toadd = "email id on which file to be sent"
            fromadd = smtpuser
            subject = "Python Test"
            header = "To: "+toadd+'\n'+"From: "+fromadd+'\n'+"Subject: "+subject+'\n'
            body = "half"
            message = header+'\n'+body
    
            print(message)

            s = smtplib.SMTP("smtp.gmail.com",587)

            s.ehlo()
            s.starttls()
            s.ehlo()
            
            s.login(smtpuser, smtppass)
            s.sendmail(fromadd, toadd, message)
            s.quit()
            flag1=2
    elif i==0 and j==0:
        flag1=1
```
### calculating distance and opening flap using servo motor
```
def measure():
  *This function measures a distance
  GPIO.output(GPIO_TRIGGER, True)
  time.sleep(0.00001)
  GPIO.output(GPIO_TRIGGER, False)
  start = time.time()

  while GPIO.input(GPIO_ECHO)==0:
    start = time.time()

  while GPIO.input(GPIO_ECHO)==1:
    stop = time.time()

  elapsed = stop-start
  distance = (elapsed * 34300)/2

  return distance

def SetAngle(angle):
    duty = angle / 18 + 2
    GPIO.output(2,True)
    pwm.ChangeDutyCycle(duty)
    time.sleep(1)
    GPIO.output(2,False)
    pwm.ChangeDutyCycle(duty)

def measure_average():
  *This function takes 3 measurements and
  *returns the average.
  distance1=measure()
  time.sleep(0.1)
  distance2=measure()
  time.sleep(0.1)
  distance3=measure()
  distance = distance1 + distance2 + distance3
  distance = distance / 3
  return distance
```
### working loop of all functions
```
try:

  while True:
    distance = measure_average()
    print ("Distance : %.1f" % distance)
    if distance < 30 :
        if flag == False :
            pwm.start(0)
            SetAngle(0)
            print("open")
            flag = True
    elif flag == True:
        pwm.start(1)
        SetAngle(90)
        print("closed")
        flag = False
    else:
        mail()
        
    
    time.sleep(1)

except KeyboardInterrupt:
  # User pressed CTRL-C
  # Reset GPIO settings
  GPIO.cleanup()
```
