# AD-PWM
## EXERCICE 2 : VOLUME D’UNE MÉLODIE
### Objectif :
Créer un programme MicroPython permet de gérer le volume d’une mélodie jouée sur un buzzer. Le
volume est contrôlé par un potentiomètre.
 ### Matériel :
• Microcontrôleur compatible MicroPython (Raspberry Pi Pico)\
• Module potentiomètre\
• Buzzer\
• Câbles
### Consignes :
1. Branchez le buzzer et le potentiomètre au microcontrôleur
2. Ecrivez un programme MicroPython qui répond aux exigences suivantes :\
o Une mélodie (au choix, soyez créatif) est jouée en boucle.\
o Le faite de changer le potentiomètre modifie directement le volume de la mélodie
3. Testez votre programme et vérifiez qu'il fonctionne correctement.
### Bonus :
• Ajoutez un bouton poussoir qui permet de changer de mélodie.\
• Ajouter une LED qui clignote au rythme de la mélodie.


### Réalisation sans Bonus :
#### Branchement raspberry Pico avec le buzzer et le potentiomètre 

![image](https://github.com/HEPL-Baes/smartcities/assets/159534213/3fe53b4a-242d-48e2-ad51-fb2479edebbc)


• Le buzzer est sur la Pin A1\
• Le potentimètre est sur la Pin A0

### Code :

**- Déclaration des entrées et sorties et des librairies.**

```
from machine import Pin,I2C,ADC,PWM
from utime import sleep
from math import log

buzzer = PWM(Pin(27)) # A1
POT = ADC(0) #A0
```
**- Fonctions des différentes notes possible de jouer (do, re, mi, fa, so, la, si, n).** \
Dans chaque fonction, on spécifie la fréquence, le volume et le moment de la note.\
Le volume subit une opération logarithmique pour une modulation plus progressive.
```
def DO(time,vol):   
    buzzer.freq(1046)#DO
    buzzer.duty_u16(int(log(vol+1)*100))
    sleep(time)
def RE(time,vol):
    buzzer.freq(1175)#RE
    buzzer.duty_u16(int(log(vol+1)*100))
    sleep(time)
def MI(time,vol):
    buzzer.freq(1318)#MI
    buzzer.duty_u16(int(log(vol+1)*100))
    sleep(time)
def FA(time,vol):
    buzzer.freq(1397)#FA
    buzzer.duty_u16(int(log(vol+1)*100))
    sleep(time)
def SO(time,vol):
    buzzer.freq(1568)#SO
    buzzer.duty_u16(int(log(vol+1)*100))
    sleep(time)
def LA(time,vol):
    buzzer.freq(1760)#LA
    buzzer.duty_u16(int(log(vol+1)*100))
    sleep(time)
def SI(time,vol):
    buzzer.freq(1967)#SI
    buzzer.duty_u16(int(log(vol+1)*100))
    sleep(time)
def N(time,vol):
    buzzer.duty_u16(0)# close
    sleep(time)
```
**-Boucle***

Dans la boucle, on enchaîne les appels des différentes fonctions de notes en fonction de la mélodie souhaitée. Pour le volume, on lit la valeur du potentiomètre à chaque note.

```
while True:
        DO(0.25,POT.read_u16())
        RE(0.25,POT.read_u16())
        MI(0.25,POT.read_u16())
        DO(0.25,POT.read_u16())
        N(0.01,POT.read_u16())
    
        DO(0.25,POT.read_u16())
        RE(0.25,POT.read_u16())
        MI(0.25,POT.read_u16())
        DO(0.25,POT.read_u16())
    
        MI(0.25,POT.read_u16())
        FA(0.25,POT.read_u16())
        SO(0.5,POT.read_u16())
    
        MI(0.25,POT.read_u16())
        FA(0.25,POT.read_u16())
        SO(0.25,POT.read_u16())
        N(0.01,POT.read_u16())
    
        SO(0.125,POT.read_u16())
        LA(0.125,POT.read_u16())
        SO(0.125,POT.read_u16())
        FA(0.125,POT.read_u16())
        MI(0.25,POT.read_u16())
        DO(0.25,POT.read_u16())
    
        SO(0.125,POT.read_u16())
        LA(0.125,POT.read_u16())
        SO(0.125,POT.read_u16())
        FA(0.125,POT.read_u16())
        MI(0.25,POT.read_u16())
        DO(0.25,POT.read_u16())
    
        RE(0.25,POT.read_u16())
        SO(0.25,POT.read_u16())
        DO(0.5,POT.read_u16())
        N(0.1,POT.read_u16())
    
        RE(0.25,POT.read_u16())
        SO(0.25,POT.read_u16())
        DO(0.5,POT.read_u16())
```

### Réalisation avec Bonus :
Pour cette partie, mon code représente une évolution significative par rapport au premier.
#### Branchement raspberry Pico avec le bouton, la led, le buzzer et le potentiomète. 

![image](https://github.com/HEPL-Baes/smartcities/assets/159534213/f166fd46-239b-4b79-b979-d5595dc64def)


• La led sur la Pin 16\
• Le bouton sur la Pin 18\
• Le buzzer est sur la Pin A1\
• Le potentimètre est sur la Pin A0

### Code :

**- Déclaration des entrées et sorties et des librairies.**

```
from machine import Pin, I2C, ADC, PWM
from utime import sleep
from math import log

LED_PWM = PWM(Pin(16))
buzzer = PWM(Pin(27))#A1
POT = ADC(0) # A0
pin_button = Pin(18, mode=Pin.IN)  # bouton
```

**- Déclaration des variables.**\
J'ai plusieurs variables internes, telles que "val" qui indique quelle mélodie doit être jouée, "i" qui permet de suivre la position dans la liste d'une mélodie,
et "M" qui indique quelle mélodie est en cours de jeu. Ensuite, on a les fréquences des différentes notes et deux listes de mélodies (M1 & M2).
```
val = 0 # valeur du bouton 
i = 0 #valeur pour les mélodies

DO = 1046
RE = 1175
MI = 1318
FA = 1397
SO = 1568
LA = 1760
SI = 1967

M1 = ((MI, 0.25), (SI, 0.25), (DO, 0.25), (RE, 0.25),
      (MI, 0.25), (RE, 0.25), (DO, 0.25), (SI, 0.25),
      (LA, 0.25), (LA, 0.25), (DO, 0.25), (MI, 0.25),
      (RE, 0.25), (DO, 0.25), (SI, 0.25), (SI, 0.25))
M2 = ((DO, 0.25), (RE, 0.25), (MI, 0.25),
      (DO, 0.25), (DO, 0.25),
      (RE, 0.25), (MI, 0.25), (DO, 0.25),
      (MI, 0.25), (FA, 0.25), (SO, 0.25),
      (MI, 0.25), (FA, 0.25), (SO, 0.25),
       (SO, 0.125), (LA, 0.125),
      (SO, 0.125), (FA, 0.125), (MI, 0.25),
      (DO, 0.25), (SO, 0.125), (LA, 0.125),
      (SO, 0.125), (FA, 0.125), (MI, 0.25),
      (DO, 0.25), (RE, 0.25), (SO, 0.25),
      (DO, 0.5), (RE, 0.25),
      (SO, 0.25), (DO, 0.5))

M = M1
```
**- Déclaration des fontions.**

**-Bouton**
Cette fonction gère l'appui du bouton qui incrémente une valeur et la remets à 0 quand il faut.\
ELle est appelé par une interuption.
```
# Fonction lors d'un appui sur le bouton
def button(pin):
    global val
    val = val + 1
    if val == 3:
        val = 0
    print(val)

pin_button.irq(trigger=Pin.IRQ_FALLING, handler=button)# interruption
```
**-Note**
Cette fonction permet de jouer n'importe quelle note. 
```
def note(F, time):
    buzzer.freq(F)
    buzzer.duty_u16(int(log(POT.read_u16() + 1) * 100))
    sleep(time)
```

**-LED**
Cette fonction allume une LED avec différents niveaux d'intensité en fonction de la note jouée.
```
def led(F):
    duty = {
        DO: 10000,
        RE: 8000,  
        MI: 6000,
        FA: 4000,
        SO: 2000,
        LA: 1000,
        SI: 500,
    }
    LED_PWM.freq(POT.read_u16())
    LED_PWM.duty_u16(duty.get(F, 0))
```

**-Boucle**
Dans cette boucle, on teste la valeur du bouton pour déterminer quelle mélodie doit être jouée. Ensuite, on joue la note, allume la LED, et on incrémente la valeur de "i" pour suivre la position dans la liste de la mélodie. Une fois que la dernière note de la mélodie est atteinte, on remet "i" à 0.
```
while True:
    if val == 1:
        M = M1
    elif val == 2:
        M = M2
    led(M[i][0])
    note(M[i][0], M[i][1])
    i += 1
    if i == len(M):
        i = 0
```
### Code complet avec BONUS :
```
from machine import Pin, I2C, ADC, PWM
from utime import sleep
from math import log

LED_PWM = PWM(Pin(16))
buzzer = PWM(Pin(27))#A1
POT = ADC(0) # A0
pin_button = Pin(18, mode=Pin.IN)  # bouton

val = 0 # valeur du bouton 
i = 0 #valeur pour les mélodies

DO = 1046
RE = 1175
MI = 1318
FA = 1397
SO = 1568
LA = 1760
SI = 1967

M1 = ((MI, 0.25), (SI, 0.25), (DO, 0.25), (RE, 0.25),
      (MI, 0.25), (RE, 0.25), (DO, 0.25), (SI, 0.25),
      (LA, 0.25), (LA, 0.25), (DO, 0.25), (MI, 0.25),
      (RE, 0.25), (DO, 0.25), (SI, 0.25), (SI, 0.25))
M2 = ((DO, 0.25), (RE, 0.25), (MI, 0.25),
      (DO, 0.25), (DO, 0.25),
      (RE, 0.25), (MI, 0.25), (DO, 0.25),
      (MI, 0.25), (FA, 0.25), (SO, 0.25),
      (MI, 0.25), (FA, 0.25), (SO, 0.25),
       (SO, 0.125), (LA, 0.125),
      (SO, 0.125), (FA, 0.125), (MI, 0.25),
      (DO, 0.25), (SO, 0.125), (LA, 0.125),
      (SO, 0.125), (FA, 0.125), (MI, 0.25),
      (DO, 0.25), (RE, 0.25), (SO, 0.25),
      (DO, 0.5), (RE, 0.25),
      (SO, 0.25), (DO, 0.5))

M= M1

# Fonction lors d'un appui sur le bouton
def button(pin):
    global val
    val = val + 1
    if val == 3:
        val = 0
    print(val)

def note(F, time):
    buzzer.freq(F)
    buzzer.duty_u16(int(log(POT.read_u16() + 1) * 100))
    sleep(time)
    
def led(F):
    duty = {
        DO: 10000,
        RE: 8000,  
        MI: 6000,
        FA: 4000,
        SO: 2000,
        LA: 1000,
        SI: 500,
    }
    LED_PWM.freq(POT.read_u16())
    LED_PWM.duty_u16(duty.get(F, 0))

pin_button.irq(trigger=Pin.IRQ_FALLING, handler=button)

while True:
    if val == 1:
        M = M1
    elif val == 2:
        M = M2
    led(M[i][0])
    note(M[i][0], M[i][1])
    i += 1
    if i == len(M):
        i = 0
```
