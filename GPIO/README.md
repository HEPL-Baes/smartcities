# GPIO
## EXERCICE 1 : CLIGNOTEMENT DE LED AVEC BOUTON POUSSOIR
### Objectif:
Créer un programme MicroPython qui permet de faire clignoter une LED à différentes vitesses en
fonction du nombre de pressions sur un bouton poussoir.
#### Matériel:
• Microcontrôleur compatible MicroPython (Raspberry Pi Pico)\
• Module LED\
• Module bouton poussoir\
• Câbles\
### Consignes:
1. Branchez la LED et le bouton poussoir au microcontrôleur
2. Ecrivez un programme MicroPython qui répond aux exigences suivantes :
o La LED doit clignoter à l’infini avec une fréquence de 0,5 Hz lorsque le bouton poussoir
est pressé une fois.\
o La LED doit clignoter plus vite lorsque le bouton poussoir est pressé une second fois.\
o La LED doit s'éteindre lorsque le bouton poussoir est pressé une troisième fois.\
3. Testez votre programme et vérifiez qu'il fonctionne correctement.
#### Bonus:
• Ajoutez un délai ou un effet dans les clignotements de la LED ou dans le passage d’une vitesse
de clignotement à une autre.
• Modifiez le nombre d'appuis nécessaires pour changer la vitesse de clignotement.

### Réalisation :
#### Branchement du Raspberry Pi Pico avec la LED et le Bouton :
![image](https://github.com/HEPL-Baes/smartcities/assets/159534213/3fdbc394-22af-402a-8321-336e3ffedc0d) \
• La LED est sur la pin D18 \
• Le boutton est sur la pin D16 

#### Code : 
**- Déclaration des entrées et sorties et des varibles internes.**

```
import utime
from machine import Pin

# Configuration des entrées/sorties 
pin_button = Pin(18, mode=Pin.IN, pull=Pin.PULL_UP)  #bouton
LED = Pin(16, mode=Pin.OUT)  # LED
val = 0  # Variable interne pour le bouton 
```
**- Fonction boutton** \
Elle permet lorsqu'elle est appelé d'incrémenter la varible 'val' de 1 à chaque appui du boutton, \
et elle remets à 0 la varible 'val' qd elle à atteint 3. 'val' passera donc par 3 états 1,2 et 3.

```
# Fonction lors d'un appui sur le bouton
def button(pin):
    global val
    val = val + 1
    if val == 3:
        val = 0
```
J'ai également ajouté en plus pour le BONUS 3 lignes de codes qui permettent d'allumer la LED une seconde à chaque transitions.

```
    LED.value(1)
    utime.sleep(1)
    LED.value(0)
```
  
La fonction est appellé par une **interruption** à chaque fois que le bonton est sur un front descendant.
```
pin_button.irq(trigger=Pin.IRQ_FALLING, handler=button)
```
**- Boucle principale** \
Ma boucle est constitué de 3 contitions qui dépendent de la valeur de 'val'. \

- Clignotement infini lent. 
```
    if val == 1:
        # Clignotement lent de la LED
        LED.value(1)
        utime.sleep(1)
        LED.value(0)
        utime.sleep(1)
```

![IMG_3488](https://github.com/HEPL-Baes/smartcities/assets/159534213/dceef804-15c6-4ae2-a5e2-0614750dd472) 

- Clignotement infini rapide. 
```
    elif val == 2:
        # Clignotement rapide de la LED
        LED.value(1)
        utime.sleep(0.1)
        LED.value(0)
        utime.sleep(0.1)  
```
![IMG_3489](https://github.com/HEPL-Baes/smartcities/assets/159534213/f8db5e78-5426-42de-be07-0f70061e97c1) 

- Eteint la LED
```
    elif val == 3:
        LED.value(0)  
```
### Code en entier
```
pin_button = Pin(18, mode=Pin.IN, pull=Pin.PULL_UP)  #bouton
LED = Pin(16, mode=Pin.OUT)  # LED
val = 0  # Variable interne pour le bouton 

# Fonction lors d'un appui sur le bouton
def button(pin):
    global val
    val = val + 1
    if val == 3:
        val = 0
#BONUS :  Ajoutez un délai ou un effet dans les clignotements de la LED ou dans le passage d’une vitesse de clignotement à une autre.   
    LED.value(1)
    utime.sleep(1)
    LED.value(0)

# Configuration de l'interruptions pour le bouton
pin_button.irq(trigger=Pin.IRQ_FALLING, handler=button)

# Boucle principale
while True:
    if val == 1:
        # Clignotement lent de la LED
        LED.value(1)
        utime.sleep(1)
        LED.value(0)
        utime.sleep(1)
    elif val == 2:
        # Clignotement rapide de la LED
        LED.value(1)
        utime.sleep(0.1)
        LED.value(0)
        utime.sleep(0.1)             
    elif val == 3:
        LED.value(0)  
```
### Test du programme 

![IMG_3482 (1)](https://github.com/HEPL-Baes/smartcities/assets/159534213/6c0b69f1-b10f-4bfd-9d75-cdcc6c21eded) 

Remarque la partie BONUS est pas présente sur le GIF.


