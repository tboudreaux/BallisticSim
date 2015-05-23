############################################################
##      Balistic Motion Simulator, organic                ##
##            V0.5                                        ##
##              function Cleaned                          ##
##      Copyright 2015, Thomas Boudreaux                  ##
############################################################

#Libraries to Import
from __future__ import division
from visual import *
from visual.controls import *
import time
import sys
import threading

#Global Variables
impacted = False
ended = False
follow = False
endtime = True
paused = False
dt = .01
height = 0
pos = vector(0,0,0)
bpr = 0
gp = vector(0,0,0)
gz = vector(0,0,0)
vix = 0
viy = 0
g = 0
temp = 0
menreturn = 0
BoolReturn = 1
infopasser = False
pressure = 0
R = 287.058
Cd = 0
Aref = 0
mass = 0

#Error Codes
E1 = 'Error, Please enter either Yes or no, program is case sensitive'
E2 = 'Error, Please enter Yes, program is case sensitive'

#Menu Questions
deftemp = 'Would you like to use standard Earth temperature? [Yes/no]: '
enttemp = 'Please enter your desired temerature in the degrees celcius: '
defgrav = 'Would you like to use an Approximation of Earth Normal Gravitational Acceleration at Sea level? [Yes/no]: '
entgrav = 'Please Entered Desired Gravity (enter absolute value of acceletation): '
track = 'Would You like to track the projectile? [Yes/no]: '
defpresh = 'Would you like to use Pressure at Sea Level on Earth? [Yes/no]: '
entpresh = 'Please Enter Desired Pressure for Use in Drag Calculations: '

#Meta Functions
def YesnoMenu(value, question, nextquestion):
    global E1, menreturn, infopasser
    men = True
    while men is True:
        yesno = raw_input(question)
        if yesno == "Yes":
            menreturn = value
            men = False
        elif yesno == "no":
            menreturn = input(nextquestion)
            men = False
            infopasser = True
        else:
            print E1
            men = True
def BoolMenu(question):
    global E1, BoolReturn
    men = True
    while men is True:
        yesno = raw_input(question)
        if yesno == "Yes":
            BoolReturn = 1
            men = False
        elif yesno == "no":
            BoolReturn = 0
            men = False
        else:
            print E1
            men = True

#Main Functions
def UserInput():
    global follow, vix, viy, g, follow, height, deftemp, enttemp, temp, track, infopasser, pressure, Cd, mass
    mass = input('Please Enter Mass of Projectile: ')
    vix = input('Please Enter Initial X Velocity: ')
    viy = input('Please Eneter Initial Y Velocity: ')
    height = input('Please Enter Initial Height: ')
    YesnoMenu(9.81, defgrav, entgrav)
    g = menreturn
    g = -g
    YesnoMenu(16, deftemp, enttemp)
    temp = menreturn
    YesnoMenu(101.325, defpresh, entpresh)
    pressure = menreturn
    print pressure
    Cd = input('Please enter Drag coefficient for object: ')
    BoolMenu(track)
    follow = bool(BoolReturn)

def PadPlacement():
    global vix, viy, g
    taa = viy/g
    happ = ((vix**2)+(viy**2))/(2*abs(g))
    haa = happ + height
    tff = sqrt((2*haa)/abs(g))
    xpp = vix*tff
    base = box(pos=(xpp,-1,0), size=(100,2,100))

def ScreenParam():
    global height, pos
    scene.autoscale = 0
    scene.range = (100,height + 25,100)
    #scene.center = (pos.x,pos.y,pos.z)
    
def ObjectDraw():
    global height, pos, bpr, gp, gz, Aref
    ground = box(pos=(0,-1,0), size=(100,2,100))
    DropStructure = box(pos=(0,(.5 * height),0), size=(5,height,5), color=color.green)
    dsp = DropStructure.pos
    dss = DropStructure.size
    tr = 2
    ballistic = sphere(pos=(dsp.x, dsp.y + (.5*dss.y) + (.5*tr),dsp.z), radius = tr, make_trail=True)
    pos = ballistic.pos
    bpr = ballistic.radius
    gp = ground.pos
    gz = ground.size
    bt = ballistic.trail_object.color
    scene.center = (pos.x,pos.y,pos.z) #This is hear for ease of variable use, it should really be in ScreenParam()
    Aref = 4 * pi * (tr**2)
    print Aref

def keyInput(evt):
    global paused, follow, pos
    x = pos.x
    y = pos.y
    z = pos.z
    print('four')
    s = evt.key
    if (s=='k'):
        print('Pausing Program')
        paused = True
    elif (s=='u'):
        print('Unpausing Program')
        paused = False
        
    #Movment
    elif (s=="right" and follow == False):
        x = x + 1
        scene.center = (x, y, z)
    elif (s=="left" and follow == False):
        x = x - 1
        scene.center = (x - 1, y, z)
    elif (s=="up" and follow == False):
        y = y + 1
        scene.center = (x, y + 1, z)
    elif (s=="down" and follow == False):
        y = y - 1
        scene.center = (x, y - 1, z)

def PositionUpdate():
    global impacted, follow, dt, g, pos, viy, vix, gz, gp, paused, R, pressure, temp, Cd, mass
    while not impacted:
        #Positional Update Per Loop Run
        x = 0
        rate(100)
        D = pressure/(R*temp)
        DragY = (1/2)*D*(viy**2)*Cd*Aref
        Fnety = -(mass * g) + DragY
        NetAy = Fnety / mass
        print DragY
        DragX = (1/2)*D*(vix**2)*Cd*Aref
        Fnetx = DragX
        NetAx = Fnetx / mass
        print DragX
        bp = pos
        dx = vix * dt + ((1/2)*NetAx*(dt**2))
        bp.x = bp.x + dx
        vfy = viy + (NetAy * dt)
        dy = ((viy * dt) + ((NetAy * (dt**2))))/2
        bp.y = bp.y + dy
        viy = vfy
        if follow == True:
            scene.center = (bp.x, bp.y, 0)
        if paused:
            x = x + 1
            time.sleep(x)
        #Impact Check per loop run
        if bp.y - (.5 * bpr) <= gp.y + (.5 * gz.y):
            impacted = True
            break
            
def ProgramTermination():
    global ended, endtime, E1
    while not ended:
        while endtime is True:
            end = raw_input('Would you like to end the program? [Yes]: ')
            if end == 'Yes':
                sys.exit()
            else:
                print E2
                endtime = True

def HoldingPattern():
    if paused is True:
        time.sleep(0.01)
def main():
    global paused
    UserInput()
    ScreenParam()
    ObjectDraw()
    scene.bind('keydown', keyInput)
    PadPlacement()
    PositionUpdate()
    ProgramTermination()

#General Math Section


#Function Calls
main()

