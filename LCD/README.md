# LCD
# EXERCICE 3 : SYSTÈME DE CONTRÔLE DE TEMPÉRATURE
## Objectif:
Créer un programme MicroPython qui permet gérer un thermostat à plusieurs états.
### Matériel:
• Microcontrôleur compatible MicroPython (Raspberry Pi Pico)\
• Module capteur température/humidité \
• Module LED \
• Module potentiomètre \
• Module écran LCD \
• Module Buzzer \
• Câbles
## Consignes:
1. Développez un programme en MicroPython sur le Raspberry Pico W pour: \
o Lire la valeur de la résistance variable et la convertir en une température de consigne
dans une plage de 15°C à 35 °C. \
o Lire la température mesurée par le capteur toutes les secondes environ.\
o Comparer la température mesurée à la température de consigne. \
o Afficher sur le module LCD: \
   § La température de consigne, préfixée par "Set: ". \
   § La température mesurée préfixée par "Ambient: ". \
o Contrôle: \
   § Si la température mesurée est supérieure à la température de consigne: \
   § La LED bat à une fréquence de 0,5 Hz. \
   § Si la température mesurée est supérieure de 3 degrés à la température de
consigne: \
  § Le buzzer sonne. \
  § La LED clignote plus rapidement.\
  § Le mot "ALARM" apparait sur l'écran LCD.
## Bonus:
• Afficher un battement progressif (dimmer) de la LED.\
• Faire clignoter le mot "ALARM" à l'écran. \
• Faire défiler le mot "ALARM" sur l'écran.

# Réalisation 
## Hardware

![image](https://github.com/HEPL-Baes/smartcities/assets/159534213/6178dd88-5f58-4838-8ab4-97324dae6400)


- Le capteur de température DHT11 est branché sur la PIN D18
- Le module LED est branché sur la PIN D20
- Le module potentiomètre est branché su la PIN A0
- Le module LCD est sur la PIN I2C1
- Le module buzzer est sur la PIN D16

## Software
### Déclaration des librairies et des variables

```
from lcd1602 import LCD1602
import dht
from machine import I2C, Pin, ADC,PWM
from utime import sleep
import utime

i2c = I2C(1, scl=Pin(7), sda=Pin(6), freq=400000) #LCD
d = LCD1602(i2c, 2, 16) #LCD dimension
d.display() # LCD
dht_sensor = dht.DHT11(machine.Pin(18)) # capteur de température
ROTARY_ANGLE_SENSOR = ADC(0) # potentiomètre
buzzer = PWM(Pin(16)) # buzzer
LED = Pin(20, mode=Pin.OUT) # LED
```
### Fonctions
#### consigne
Permet de lire la consigne donné par le potentiomètre et est convertie sur un plage de consigne comprise entre 15 et 35.
```
def consigne():
    val = ROTARY_ANGLE_SENSOR.read_u16()
    t_c = 15 + ((val - 0) * (35 - 15)) // (65535 - 0)
    return t_c
```
#### mesure
Fait une mesure sur le capteur de température et la stock dans une variable.
```
def mesure():
    dht_sensor.measure()
    temp = dht_sensor.temperature()
    return temp
```
#### affiche temp
Permet d'affiche la températur actuelle et la consigne sur le LCD.
```
def affiche_temp(temp,consigne_t):
    d.setCursor(0, 0)
    d.print("Ambient : " + str(temp)+ " C")
    d.setCursor(0, 1)
    d.print("Set: " + str(consigne_t)+" C")
```
#### buzz
Permet de faire sonner un alarme à un certaine fréquence. 
```
def buzz():
    buzzer.freq(100)
    buzzer.duty_u16(1000)
    sleep(1)
    buzzer.duty_u16(0)#close
    sleep(0.1)
```
#### alarme 1
Permet de faire clignater un LED. 
```
def alarm_1():
    global LED
    LED.value(1) 
    utime.sleep(0.1)
    LED.value(0)
    utime.sleep(0.1)
    sleep(1)
```
#### alarme 2
Permet de faire clignater un LED plus rapidement. 
```
def alarm_2():
    global LED
    LED.value(1)
    utime.sleep(1)
    LED.value(0)
    utime.sleep(1)
```
#### alarme msg 
Permet d'afficher un msg d'alarme qui défille et affiche la consigne et la température. 
```
def alarm_msg(temp,consigne_t):
    i = 0
    while i < 16:
        i += 1
        d.clear()
        d.setCursor(i, 0)
        d.print("!! ALARM !!")
        d.setCursor(0, 1)
        d.print("S:" + str(consigne_t)+" A: "+str(temp))
        buzzer.freq(100)
        buzzer.duty_u16(1000)
        utime.sleep(0.5)  
        buzzer.duty_u16(0)
    i = 0
```
### Boucle 
On fonction de la température et de la consigne je fais appel à différrentes fonctions. 
```
while True:
    
    temp = mesure()
    consigne_t = consigne()
    if temp <= consigne_t:
        affiche_temp(temp, consigne_t)
        sleep(1)
        
    elif temp >= consigne_t and temp < consigne_t +3:
        affiche_temp(temp, consigne_t)
        alarm_1()
        sleep(1)
    
    elif temp >= consigne_t + 3:
        affiche_temp(temp, consigne_t)
        alarm_2()
        alarm_msg(temp, consigne_t)
        d.clear()
        buzz()
        sleep(1)
```
### Test
On entend malheureusement pas le buzzer comme c'est un GIF. \
![IMG_3655](https://github.com/HEPL-Baes/smartcities/assets/159534213/3527c65a-3d0a-441f-8066-13a950887a56)


### Code complet 

```
from lcd1602 import LCD1602
import dht
from machine import I2C, Pin, ADC,PWM
from utime import sleep
import utime

i2c = I2C(1, scl=Pin(7), sda=Pin(6), freq=400000) #LCD
d = LCD1602(i2c, 2, 16) #LCD dimension
d.display() # LCD
dht_sensor = dht.DHT11(machine.Pin(18)) # capteur de température
ROTARY_ANGLE_SENSOR = ADC(0) # potentiomètre
buzzer = PWM(Pin(16)) # buzzer
LED = Pin(20, mode=Pin.OUT) # LED


def consigne():
    val = ROTARY_ANGLE_SENSOR.read_u16()
    t_c = 15 + ((val - 0) * (35 - 15)) // (65535 - 0)
    return t_c

def mesure():
    dht_sensor.measure()
    temp = dht_sensor.temperature()
    return temp

def affiche_temp(temp,consigne_t):
    d.setCursor(0, 0)
    d.print("Ambient : " + str(temp)+ " C")
    d.setCursor(0, 1)
    d.print("Set: " + str(consigne_t)+" C")
    
def buzz():
    buzzer.freq(100)
    buzzer.duty_u16(1000)
    sleep(1)
    buzzer.duty_u16(0)#close
    sleep(0.1)
    
def alarm_1():
    global LED
    LED.value(1) 
    utime.sleep(0.1)
    LED.value(0)
    utime.sleep(0.1)
    sleep(1)
    
def alarm_2():
    global LED
    LED.value(1)
    utime.sleep(1)
    LED.value(0)
    utime.sleep(1)
    
def alarm_msg(temp,consigne_t):    
    i = 0
    while i < 16:
        i += 1
        d.clear()
        d.setCursor(i, 0)
        d.print("!! ALARM !!")
        d.setCursor(0, 1)
        d.print("S:" + str(consigne_t)+" A: "+str(temp))
        buzzer.freq(100)
        buzzer.duty_u16(1000)
        utime.sleep(0.5)  
        buzzer.duty_u16(0)
    i = 0

         

    
while True:
    
    temp = mesure()
    consigne_t = consigne()
    if temp <= consigne_t:
        affiche_temp(temp, consigne_t)
        sleep(1)
        
    elif temp >= consigne_t and temp < consigne_t +3:
        affiche_temp(temp, consigne_t)
        alarm_1()
        sleep(1)
    
    elif temp >= consigne_t + 3:
        affiche_temp(temp, consigne_t)
        alarm_2()
        alarm_msg(temp, consigne_t)
        d.clear()
        buzz()
        sleep(1)
```
