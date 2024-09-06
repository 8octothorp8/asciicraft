# asciicraft 1.0.0
# has movement, building, breaking, inventory, and trees. nothing else.

import sys, tty, termios, os, random, collections

def getkey():
    fd = sys.stdin.fileno()
    old_settings = termios.tcgetattr(fd)
    try:
        tty.setraw(sys.stdin.fileno())
        key = sys.stdin.read(1)
    finally:
        termios.tcsetattr(fd, termios.TCSADRAIN, old_settings)
    return key

print("Loading map...")

size = 100
setworld = [" "*size]*size
for i in range(size):
    temp = ""
    for j in range(size):
        temp += random.choice([" "]*40+[","])
    setworld[i-1] = temp
world = setworld.copy()
px = round(size/2)
py = round(size/2)
blocks = {}
vision = [px, py-1]
key = ""
look = ""
minepower = 0
point = 0

inventory = []
inval = []

def sortwords(words_list):
    word_count = collections.Counter(words_list)
    result = []
    for word in words_list:
        result.extend([word] * word_count[word])
        word_count[word] = 0 
    return result
def render(x):
    x = x.lower()
    x = x.replace(",", "\033[32m,\033[0m")
    x = x.replace("þ", "\033[43m\033[93m⦀\033[0m")
    x = x.replace("ľ", "\033[43m\033[93m⦀\033[0m")
    x = x.replace("l", "\033[42m\033[92m&\033[0m")
    x = x.replace("ł", "\033[33mł\033[0m")
    x = x.replace("o", "\033[32mo\033[0m")
    return x
def lim(x):
    if x < 0:
        x = 0
    if x >= size:
        x = size - 1
    return x
def top(x, top):
    if x > top:
        x = top
    return x
def bot(x, top):
    if x < top:
        x = top
    return x
def place(char, x, y, collision):
    if world[y][x] in [" ",","]:
        line = world[lim(y)]
        line = list(line)
        if collision == False:
            char = char.lower()
        line[lim(x)] = char
        line = "".join(line)
        world[lim(y)] = line
def megaplace(lst, collision):
    for i in lst:
        for j in lst[i]:
            place(i, j[0], j[1], collision)
def get(x, y):
    return world[y][x]
def placeblock(char, x, y):
    if char in blocks:
        blocks[char].append([x, y])
    else:
        blocks[char] = [[x, y]]
def rand(a, b):
    return random.randint(a, b)
def isghost(a):
    if a == a.lower():
        return True
    else:
        return False
def moveplayer(char, x, y):
    global px
    global py
    global look
    global vision
    if key == char:
        vision = [px+x, py+y]
        look = get(vision[0], vision[1])
        if look in [" ",","] or isghost(look):
            px = vision[0]
            py = vision[1]
        vision = [px+x, py+y]
def renderTrees():
    for y in range(size):
        for x in range(size):
            if world[y][x] == "Þ":
                place("Þ",x,y-1,False)
                place("L",x,y-2,False)
                place("L",x-1,y-2,False)
                place("L",x-2,y-2,False)
                place("L",x+1,y-2,False)
                place("L",x+2,y-2,False)
                place("L",x,y-3,False)
                place("L",x-1,y-3,False)
                place("L",x+1,y-3,False)
def give(x):
    inventory.append(x)
def mine(target, x, y, drops, power):
    for i in blocks:
        for j in blocks[i]:
            if [x, y] in blocks[i] and i == target and power <= minepower:
                del blocks[i][blocks[i].index([x, y])]
                for k in drops:
                    give(k)

os.system("clear")
print("Placing blocks...")
for __ in range(round(size*1)):
    placeblock("Þ", rand(3, size-6), rand(3, size-3))

while True:
    # create world
    world = setworld.copy()
    place("@", px, py, False)
    for i in blocks:
        for j in blocks[i]:
            place(i, j[0], j[1], True)
    renderTrees()
    
    # print
    os.system("clear")
    print(" "*7,"ASCIICRAFT")
    print("╔"+"═"*24+"╗")
    
    min_y = max(0, min(size - 16, py - 8))
    max_y = min(size, max(16, py + 8))
    worldy = world[min_y:max_y]
    min_x = max(0, min(size - 24, px - 12))
    max_x = min(size, max(24, px + 12))
    
    for i in worldy:
        worldx = i[min_x:max_x]
        print("║"+render(worldx)+"║",)
    print("╚"+"═"*24+"╝")
    print("x:",round(size/2)-(size-px),"| y:",round(size/2)-py)
    print()
    
    showinv = ""
    showinval = ""
    inval = []
    for i in inventory:
        if i not in showinv:
            showinv += i + "   │  "
            inval.append(1)
        else:
            idx = showinv.split("   │  ").index(i)
            inval[idx] += 1
    for i in inval:
        showinval += str(i) + "  │   "[len(str(i))-1:]
    
    if inventory != []:
        print("Inventory:")
        print("┌"+"─"*(len(showinv)-1)+"┐")
        print("│ ",render(showinv))
        print("│  ",showinval)
        print("└"+"─"*(len(showinv)-1)+"┘")
        print(" "*(point*7)+"^^^^^^^^")
    else:
        print("Inventory empty!")
    
    # get the key pressed
    key = getkey()
    
    # do stuff
    
    inventory = sortwords(inventory)
    
    fakeinv = []
    for i in inventory:
        if i not in fakeinv:
            fakeinv.append(i)
    main = " "
    if fakeinv != []:
        main = fakeinv[top(point, len(fakeinv)-1)]
    
    moveplayer("w", 0, -1)
    moveplayer("a", -1, 0)
    moveplayer("s", 0, 1)
    moveplayer("d", 1, 0)
    if key == "m":
        mine("Þ", vision[0], vision[1], ["ľ"]*rand(2,3)+["ł"]*rand(0,5)+["o"]*rand(2,10), 0)
        mine("Ľ", vision[0], vision[1], ["ľ"], 0)
    elif key == "p":
        if look in [" ", ","] and main in inventory:
            placeblock(main.upper(), vision[0], vision[1])
            del inventory[inventory.index(main)]
    elif key == "q":
        point = bot(top(point-1, len(fakeinv)-1),0)
    elif key == "e":
        point = bot(top(point+1, len(fakeinv)-1),0)
    
    px = lim(px)
    py = lim(py)
