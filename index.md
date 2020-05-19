#Úvod

Ahoj, myslím že každý alespoň jednou hrál nebo alepsoň slyšel o této hře.
Proto by jsem se rozhodl že by jsme si ji mohli naprogramovat. Sice na této 
stránce už jeden takový tutoriál je, ale je napsaný v knihovně Pyglet. Já jsem si
za knihovnu vybral Pygame, jelikož se více hodí na tvorbu 2D her. Ještě než začneme,
předpokládám že máte na svém zařízení nainstalovaný python 3 a novější.
Jestli ne, zde je 
[tutoriál.](https://naucse.python.cz/course/pyladies/beginners/install/) 

##Instalace Pygame

První věcí kterou budeme potřebovat pro tvorbu hry je instalace Pygame do systému.
Toho docílíme napsáním jednoduchého příkazu do konzole
```pip install pygame```.
Jakmile máme hotovo, můžeme začít Pygame a jeho funkce používat.

##Vytvoření hracího okna
Pro vytvoření hracího okna, budeme potřebovat metodu `display.set_mode() `.
Také na začátku kódu potřebujeme zavolat metodu `init()` a `quit()` aby jsme
vše inicializovali (načetli) a zrušili na začátku a konci kódu. Další metodou bude metoda 
`update()`. Používá se pro jakoukoliv změnu provedenou v okně.

>[note]
>Jestli jste s Pygame někdy již pracovali, určitě jste slyšeli o metodě
>`flip()`. Tato metoda je velmi podobná metodě `update()`. Jsou však odlišné.
>Metoda `update()` vykoná změny které jsme provedli a které chceme vykreslit,
>zatímco `flip()` nám překreslí celé okno !
 
 
 Prozatimní kód s výsledkem budou vypadat následovně:
```python
# Naimportuje knihovnu
import pygame

# postará se o inicializaci
# všech modulů Pygame
pygame.init()

# vytvoří okno o zadaných rozměrech
dis = pygame.display.set_mode((400,300))

# vykreslí okno
pygame.display.update()

# vše odinicializuje
pygame.quit()
quit()
```
#### Výsledek: 
{{ figure(
    img=static('okno-pygame.png'),
    alt="Vytvořené okno",
) }}

Jakmile spustíme kód, ukáže se nám 
obrazovka, ta se ale okamžitě zavře 
(ukončí). Aby k zavírání nedocházelo,
vytvoříme tzv. `"game loop"`, neboli 
herní cyklus. Dále musíme nastavit, aby
se okno ukončilo stiskem křížku v pravém
rohu okna. Důvodem je, že máme nekonečný
cyklus. Ten by nám zobrazoval okno do nekonečna
a my ho neměli jak vypnout. Toho docílíme jednoduchou
podmínkou v cyklu. 

```python
import pygame

pygame.init()
dis = pygame.display.set_mode((400, 300))
pygame.display.update()

# nastaví oknu titulek
pygame.display.set_caption('Snake')

# proměnná, kterou při ukončení aplikace změníme na True
game_over = False

# herní cyklus
while not game_over:
    for event in pygame.event.get():  # cyklus co hlídá jakoukoliv akci provedenou okně
        if event.type == pygame.QUIT:  # podmínka, která hlídá stisk křížku
            game_over = True  # ukončí herní cyklus

pygame.quit()
quit()

```
Nyní máme vytvořené funkční okno. Dalším
krokem je vykreslení hada do okna. Pro jednoduchost
bude hada představovat barevný čtverec.

#### Výsledek:
{{ figure(
    img=static('okno-zavreni.png'),
    alt="Okno po úpravě",
) }}

##Vytvoření a vykreslení hada

Ještě než vytvoříme hada, nadefinujeme si 
pár proměnných, které budou reprezentovat 
barvy (v RGB). Jak již bylo zmíněno,
hada bude představovat barevný čtverec.
Pro vykreslení zavoláme funkci `draw.rect()`.
Té dodáme argumenty, aby python věděl, do jakého
okna čtverec vykreslit, barvu, souřadnice pro 
umístění v okně a výšku a šířkou čtverce.

```python
import pygame

pygame.init()
dis = pygame.display.set_mode((400, 300))

pygame.display.set_caption('Snake')
game_over = False

# definování barev
white = (255, 255, 255)
yellow = (255, 255, 102)
black = (0, 0, 0)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)
dark_red = (138, 8, 0)

while not game_over:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True

    # vykreslení hada
    pygame.draw.rect(dis, green, [200, 150, 10, 10])

    # metodu jsme museli kvůli funkčnosti přesunout sem
    pygame.display.update()
pygame.quit()
quit()
```
#### Výsledek:

{{ figure(
    img=static('had-vykresleni.png'),
    alt="Vykreslení hada",
) }}

## Pohyb hada

Aby byl had ovladatelný, použijeme knihovnu 
`KEYDOWN`, kterou pygame obsahuje. 
Použijeme `K_UP, K_DOWN, K_LEFT, and K_RIGHT`
pro pohyby do všech stran. Dále budeme potřebovat
proměnné pro souřadnice hada, také si nastavíme 
počet snímků za sekundu. Vše je vysvětlené v komentářích 
za kódem níže.

```python
import pygame

pygame.init()

# pro lepší přehlednost si velikost okna
# nadefinujeme pomocí proměnných
dis_width = 600
dis_height = 400

# ty zde dosadíme
dis = pygame.display.set_mode((dis_width, dis_height))

pygame.display.set_caption('Snake')
game_over = False

white = (255, 255, 255)
yellow = (255, 255, 102)
black = (0, 0, 0)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)
dark_red = (138, 8, 0)

# souřadnice hada
x1 = 300
y1 = 300

# v těchto proměnných budeme uchovávat měnící se
# hodnotu souřadnic x1 a y1
x1_change = 0
y1_change = 0

# zde inicializujeme čas, který nám bude udávat snímky za sekundu
clock = pygame.time.Clock()

while not game_over:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True

        if event.type == pygame.KEYDOWN:  # sleduje, jestli jsme zmáčkli nějakou klávesnici
            if event.key == pygame.K_LEFT:  # po stisknutí půjde had doleva
                x1_change = -10
                y1_change = 0
            elif event.key == pygame.K_RIGHT:  # po stisknutí půjde had doprava
                x1_change = 10
                y1_change = 0
            elif event.key == pygame.K_UP:  # po stisknutí půjde had nahoru
                y1_change = -10
                x1_change = 0
            elif event.key == pygame.K_DOWN:  # po stisknutí půjde had dolů
                y1_change = 10
                x1_change = 0

        # zde ukládáme měnící se hodnoty
        x1 += x1_change
        y1 += y1_change

        # překreslí celé okno, jinak by nám had 'zabarvil'
        # celé okno, vyzkoušejte metodu vymazat a pochopíte :)
        dis.fill(black)

        # dosadíme souřadnice hada
        pygame.draw.rect(dis, green, [x1, y1, 10, 10])

        pygame.display.update()

        # nastavíme počet snímků
        clock.tick()
pygame.quit()
quit()

```

## Konec hry - kolize hada s okrajem
Každá hra musí, nebo by měla být nějakým
způsobem ukončena. Zde to bude při kolizi 
s okrajem obrazovky. Musíme tedy pomoci 
podmínky `if` definovat souřadnice 
 `x` a `y` a aby se souřadnice hada pohybovaly
 v daném rozmezí. Také si nadefinujeme metodu
`message` pro zobrazení proherní hlášky.

```python
import pygame
import time  # naimportujeme knihovnu, budeme ji potřebovat

pygame.init()

# upravíme velikost okna
dis_width = 800
dis_height = 600

dis = pygame.display.set_mode((dis_width, dis_height))

pygame.display.set_caption('Snake')
game_over = False

white = (255, 255, 255)
yellow = (255, 255, 102)
black = (0, 0, 0)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)
dark_red = (138, 8, 0)

# v případě změny rozměrů okna,
# bude had vždy ve středu okna
x1 = dis_width / 2
y1 = dis_height / 2

x1_change = 0
y1_change = 0

clock = pygame.time.Clock()

# určíme hadovi rychlost
snake_speed = 30

# nadefinujeme si font písma
# na funkčnost nemá vliv, je to spíš pro hezčí vzhled :)
font_style = pygame.font.SysFont(None, 50)


# metoda která vypíše konec hry
# jako parametry předáme znění zprávy a barvu textu
def message(msg, color):
    mesg = font_style.render(msg, True, color)
    dis.blit(mesg, [dis_width / 2, dis_height / 2])


while not game_over:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                x1_change = -10
                y1_change = 0
            elif event.key == pygame.K_RIGHT:
                x1_change = 10
                y1_change = 0
            elif event.key == pygame.K_UP:
                y1_change = -10
                x1_change = 0
            elif event.key == pygame.K_DOWN:
                y1_change = 10
                x1_change = 0

        # podmínka, která ukončí hru v případě kolize s okrajem
        if x1 >= dis_width or x1 < 0 or y1 >= dis_height or y1 < 0:
            game_over = True

        x1 += x1_change
        y1 += y1_change

        dis.fill(black)

        pygame.draw.rect(dis, green, [x1, y1, 10, 10])

        pygame.display.update()

        # počet snímků bude roven hadově rychlosti
        clock.tick(snake_speed)

# voláme metodu pro vypsání konce hry
message("Prohrál jsi", red)
pygame.display.update()

# vypíše zprávu na 2 sekundy
time.sleep(2)  # nezapomentě si knihovnu time importovat

pygame.quit()
quit()
``` 
#### Výsledek:
{{ figure(
    img=static('proherni-hlaska.png'),
    alt="Proherní hláška",
) }}

## Přidání jídla
V této sekci si přidáme jídlo pro hada.
Jakmile had překříží jídlo, vypíše se nám
do konzole "Mňam !". Také přidáme možnost zahrát
si hru po prohře znovu a kód si trochu pro 
přehlednost přepíšeme. Pokud se vám had zdá moc rychlý,
změntě hodnotu proměnné `snake_speed` na menší.

```python
import pygame
import time
import random  # naimportujeme knihovnu, budeme ji potřebovat

pygame.init()

dis_width = 800
dis_height = 600

dis = pygame.display.set_mode((dis_width, dis_height))

pygame.display.set_caption('Snake')

white = (255, 255, 255)
yellow = (255, 255, 102)
black = (0, 0, 0)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)
dark_red = (138, 8, 0)

clock = pygame.time.Clock()

snake_speed = 30

font_style = pygame.font.SysFont(None, 30)  # změníme velikost fontu


def message(msg, color):
    mesg = font_style.render(msg, True, color)
    dis.blit(mesg, [dis_width/4, dis_height/2])  # zpráva se bude vykreslovat více uprostřed


def gameLoop():  # hlavní cyklus vytvoříme jako funkci

    # přesuneme pár proměnných do cyklu
    game_over = False
    game_close = False

    x1 = dis_width / 2
    y1 = dis_height / 2

    x1_change = 0
    y1_change = 0

    foodx = round(random.randrange(0, dis_width - 10) / 10.0) * 10.0
    foody = round(random.randrange(0, dis_height - 10) / 10.0) * 10.0

    while not game_over:

        while game_close:  # jakmile prohrajeme zobrazí se hláška, jestli chceme hrát znovu
            dis.fill(black)
            message("Prohrál jsi ! Zmáčnki Q-Konec nebo C-Hraj znovu", dark_red)
            pygame.display.update()

            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:  # jakmile zmáčkneme Q, hra se ukončí
                        game_over = True
                        game_close = False
                    if event.key == pygame.K_c:  # jakmile zmáčkneme C, budeme hrát znovu
                        gameLoop()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    x1_change = -10
                    y1_change = 0
                elif event.key == pygame.K_RIGHT:
                    x1_change = 10
                    y1_change = 0
                elif event.key == pygame.K_UP:
                    y1_change = -10
                    x1_change = 0
                elif event.key == pygame.K_DOWN:
                    y1_change = 10
                    x1_change = 0

        # změníme na game_close, aby se nám zobrazilo menu pro výběr
        if x1 >= dis_width or x1 < 0 or y1 >= dis_height or y1 < 0:
            game_close = True

        x1 += x1_change
        y1 += y1_change

        dis.fill(black)

        # vykreslíme jídlo
        pygame.draw.rect(dis, red, [foodx, foody, 10, 10])

        pygame.draw.rect(dis, green, [x1, y1, 10, 10])
        pygame.display.update()

        # kolize hada s jídlem
        if x1 == foodx and y1 == foody:
            print("Mňam !")

        clock.tick(snake_speed)

    pygame.quit()
    quit()


gameLoop()
```
#### Výsledek:
{{ figure(
    img=static('vypis-konzole.png'),
    alt="Výpis konzole",
) }}

## Zvětšení hada
Následující kód bude přidávat hadovi na délce
poté, co sní jídlo. Také budeme hlídat, aby had
nenarazil sám do sebe. To vyústí též v prohru.
Délka hada bude obsažena v `pythonovském listu`
s počáteční hodnotou jednoho bloku. 
```python
import pygame
import random

pygame.init()

# zmenšíme okno
dis_width = 600
dis_height = 400

dis = pygame.display.set_mode((dis_width, dis_height))

pygame.display.set_caption('Snake')

white = (255, 255, 255)
yellow = (255, 255, 102)
black = (0, 0, 0)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)
dark_red = (138, 8, 0)

clock = pygame.time.Clock()

# zmenšíme rychlost pro lepší hratelnost
snake_speed = 15

font_style = pygame.font.SysFont(None, 30)


def message(msg, color):
    mesg = font_style.render(msg, True, color)
    dis.blit(mesg, [dis_width / 8, dis_height / 2])  # zpráva se bude vykreslovat uprostřed


# metoda vykreslující hada
def snake(snake_list):
    for x in snake_list:
        pygame.draw.rect(dis, green, [x[0], x[1], 10, 10])


def gameLoop():
    game_over = False
    game_close = False

    x1 = dis_width / 2
    y1 = dis_height / 2

    x1_change = 0
    y1_change = 0

    # inicializujeme list a délku hada
    snake_list = []
    length_of_snake = 1

    foodx = round(random.randrange(0, dis_width - 10) / 10.0) * 10.0
    foody = round(random.randrange(0, dis_height - 10) / 10.0) * 10.0

    while not game_over:

        while game_close:  # jakmile prohrajeme zobrazí se hláška, jestli chceme hrát znovu
            dis.fill(black)
            message("Prohrál jsi ! Zmáčnki Q-Konec nebo C-Hraj znovu", dark_red)
            pygame.display.update()

            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:  # jakmile zmáčkneme Q, hra se ukončí
                        game_over = True
                        game_close = False
                    if event.key == pygame.K_c:  # jakmile zmáčkneme C, budeme hrát znovu
                        gameLoop()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    x1_change = -10
                    y1_change = 0
                elif event.key == pygame.K_RIGHT:
                    x1_change = 10
                    y1_change = 0
                elif event.key == pygame.K_UP:
                    y1_change = -10
                    x1_change = 0
                elif event.key == pygame.K_DOWN:
                    y1_change = 10
                    x1_change = 0

        if x1 >= dis_width or x1 < 0 or y1 >= dis_height or y1 < 0:
            game_close = True

        x1 += x1_change
        y1 += y1_change

        dis.fill(black)

        pygame.draw.rect(dis, red, [foodx, foody, 10, 10])

        # inicializace listu pro souřadnice hlavy hada
        snake_head = []
        snake_head.append(x1)  # přidání x souřadnice do listu
        snake_head.append(y1)  # přidání y souřadnice do listu
        snake_list.append(snake_head)  # přidáme souřadnice hlavy do listu pro celého hada

        # zajistí aby nám hra neskončila hned při startu
        if len(snake_list) > length_of_snake:
            del snake_list[0]

        # hlídáme jestli had nenarazí sám do sebe
        for x in snake_list[:-1]:
            if x == snake_head:
                game_close = True

        # zavoláme pro vykreslení hada
        snake(snake_list)

        pygame.display.update()

        # kolize hada s jídlem
        if x1 == foodx and y1 == foody:
            foodx = round(random.randrange(0, dis_width - 10) / 10.0) * 10.0
            foody = round(random.randrange(0, dis_height - 10) / 10.0) * 10.0
            length_of_snake += 1

        clock.tick(snake_speed)

    pygame.quit()
    quit()


gameLoop()
```

## Skóre
V poslední části tutoriálu si ukážeme jak zobrazit
skóre. Vytvoříme si funkci `write_score()` která se
o vše postará. Skóre bude počítáno za každé 
snězené jídlo. 

```python
import pygame
import random

pygame.init()

dis_width = 600
dis_height = 400

dis = pygame.display.set_mode((dis_width, dis_height))

pygame.display.set_caption('Snake')

white = (255, 255, 255)
yellow = (255, 255, 102)
black = (0, 0, 0)
red = (213, 50, 80)
green = (0, 255, 0)
blue = (50, 153, 213)
dark_red = (138, 8, 0)

clock = pygame.time.Clock()

snake_speed = 15

font_style = pygame.font.SysFont(None, 30)

# nastavíme font a velikost písma pro lepší vzhled :)
font_score = pygame.font.SysFont("comicsansms", 35)


def write_score(score):
    value = font_score.render("Your Score: " + str(score), True, yellow)  # nastaví text
    dis.blit(value, [0, 0])  # vypíše


def message(msg, color):
    mesg = font_style.render(msg, True, color)
    dis.blit(mesg, [dis_width / 8, dis_height / 2])


def snake(snake_list):
    for x in snake_list:
        pygame.draw.rect(dis, green, [x[0], x[1], 10, 10])


def gameLoop():
    game_over = False
    game_close = False

    x1 = dis_width / 2
    y1 = dis_height / 2

    x1_change = 0
    y1_change = 0

    snake_list = []
    length_of_snake = 1

    foodx = round(random.randrange(0, dis_width - 10) / 10.0) * 10.0
    foody = round(random.randrange(0, dis_height - 10) / 10.0) * 10.0

    while not game_over:

        while game_close:
            dis.fill(black)
            message("Prohrál jsi ! Zmáčnki Q-Konec nebo C-Hraj znovu", dark_red)
            pygame.display.update()

            for event in pygame.event.get():
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q:
                        game_over = True
                        game_close = False
                    if event.key == pygame.K_c:
                        gameLoop()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    x1_change = -10
                    y1_change = 0
                elif event.key == pygame.K_RIGHT:
                    x1_change = 10
                    y1_change = 0
                elif event.key == pygame.K_UP:
                    y1_change = -10
                    x1_change = 0
                elif event.key == pygame.K_DOWN:
                    y1_change = 10
                    x1_change = 0

        if x1 >= dis_width or x1 < 0 or y1 >= dis_height or y1 < 0:
            game_close = True

        x1 += x1_change
        y1 += y1_change

        dis.fill(black)

        pygame.draw.rect(dis, red, [foodx, foody, 10, 10])

        snake_head = []
        snake_head.append(x1)
        snake_head.append(y1)
        snake_list.append(snake_head)

        if len(snake_list) > length_of_snake:
            del snake_list[0]

        for x in snake_list[:-1]:
            if x == snake_head:
                game_close = True

        snake(snake_list)

        # vypíšeme skóre
        write_score(length_of_snake - 1)  # jako argument podáme délku hada o 1 menší

        pygame.display.update()

        if x1 == foodx and y1 == foody:
            foodx = round(random.randrange(0, dis_width - 10) / 10.0) * 10.0
            foody = round(random.randrange(0, dis_height - 10) / 10.0) * 10.0
            length_of_snake += 1

        clock.tick(snake_speed)

    pygame.quit()
    quit()


gameLoop()
```
#### Výsledek:
{{ figure(
    img=static('proherni-hlaska.png'),
    alt="Proherní hláška",
) }}

A to je pro tutoriál vše. Doufám že jste se alespoň
něco nového dozvěděli. Celý kód naleznete [zde.](https://github.com/MichalHorejs/snake-naucse.python.cz/blob/master/final.py)



