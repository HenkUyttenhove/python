#!/usr/bin/python
# #####################################################
# Python Library for 3x4 matrix keypad using a
# MCP23017 chip on I2C bus
#
# Written by Henk Uyttenhove
# October 2017
#
# #####################################################

import smbus
import time

class KeyPad():
    def __init__(self):
        self.bus = smbus.SMBus(1)
        self.DEVICE = 0x20   # adress of I2C chip on the bus
        self.IODIRB = 0x01   # setup of the B bus
        self.OLATB = 0x15    # set the initial output signals
        self.GPIOB = 0x13    # read the input signals
        self.KeypadColumn = [16,32,64] # define the vertical columns and the related pins
        self.KeypadRow ={1:0,2:1,4:2,8:3}  #translation table of horizontal row into position
        self.Keypad = ([1,2,3],[4,5,6],[7,8,9],["*",0,"#"]) # layout of the keypad
        self.PushedKey = 0x00                                 # default code read when no buttons are pushed
        self.PushedButton = ""   #Actual button pushed
        self.ButtonPressed = False                            # status "no button pressed"


        # The Velleman keypad is a button matrix 3x horizontal, 4x vertical. 
        # Connector has 7 wires from left to right in order 1,2,3,4,5,6,7
        # Wire 1,2,3,4 are the horizontal rows from top to bottom
        # Wire 5,6,7 are the vertical columns from left to right
        #
        # Keypad Layout
        #     5|6|7
        #   1|1|2|3|
        #   2|4|5|6|
        #   3|7|8|9|
        #   4|*|0|#|
        #
        # How does it work?
        # Wire 5,6,7 are default low. When a wire is high, the wires 1,2,3,4 are read. When not 0 a button was pushed
        #
        # The keypad is connected to MCP23017 bus B: [1:GPB0],[2:GPB01],[3:GPB02],[4:GPB03],[5:GPB04],[6:GPB05],[7:GPB06]
        # The MCP23017 is binary sequential so 0b0000 0001 is  GPB0, 0b0000 0010 is GPB1, ... , 0b1000 0000 is GPB7
    
    def GetKey(self):
        self.bus.write_byte_data(self.DEVICE,self.IODIRB,0b00001111)  # set port GPB0->GPB3 to input, GPB4->GPB7 to output, output = 0
        self.bus.write_byte_data(self.DEVICE,self.OLATB,0b00000000)   # set all ports to 0, notice that GPB0->GPB3 values are ignored during set but used when read
        while not self.ButtonPressed:
            for x in range (0,3):  # scan the 3 columns
                RowValue = self.KeypadColumn[x]                    # convert the vertical column to appropriate code
                self.bus.write_byte_data(self.DEVICE,self.OLATB,RowValue)    # set the output ports
                self.PushedKey = self.bus.read_byte_data(self.DEVICE,self.GPIOB) & 0x0f  # fetch the input from the keypad and filter out the output ports with AND function
                if not self.PushedKey == 0x00:                     # if a button on keypad is pushed
                    self.ButtonPressed = True
                    break
        self.PushedButton = self.Keypad[self.KeypadRow[self.PushedKey]][x]            # get the appropriate button from matrix and print
