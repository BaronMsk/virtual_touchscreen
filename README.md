Simple virtual input device for testing things in Linux. Creates a character device and an input device.

![screenshot](screenshot.png).


# Building
`make`

## application

Use run `virtual_touchscreen.clj` or just use pre-built `virtual_touchscreen.jar` from Github releases

## Some testing


`insmod virtual_touchscreen.ko`  
 
`dmesg | grep virtual_touchscreen`   
virtual_touchscreen: Major=250    
  
`cat /dev/virtual_touchscreen`   

Usage: write the following commands to /dev/virtual_touchscreen:    
    x num  - move to (x, ...)    
    y num  - move to (..., y)    
    d 0    - touch down    
    u 0    - touch up   
    s slot - select multitouch slot (0 to 9)   
    a flag - report if the selected slot is being touched   
    e 0   - trigger input_mt_report_pointer_emulation    
    X num - report x for the given slot    
    Y num - report y for the given slot   
    S 0   - sync (should be after every block of commands)   
    M 0   - multitouch sync   
    T num - tracking ID    
    also 0123456789:; - arbitrary ABS_MT_ command (see linux/input.h)    
  each command is char and int: sscanf("%c%d",...)    
  <s>x and y are from 0 to 1023</s> Probe yourself range of x and y    
  Each command is terminated with '\n'. Short writes == dropped commands.    
  Read linux Documentation/input/multi-touch-protocol.txt to read about events    

`printf 'x 200\ny 300\nS 0\n' > /dev/virtual_touchscreen`    
`printf 'd 0\nS 0\n' > /dev/virtual_touchscreen`    
`printf 'u 0\nS 0\n' > /dev/virtual_touchscreen`    

`hd /dev/input/event11 # or whatever udev assigns`    
`printf 'x 200\ny 300\nS 0\n' > /dev/virtual_touchscreen`    
`rintf 'd 0\nS 0\n' > /dev/virtual_touchscreen`    
`printf 'u 0\nS 0\n' > /dev/virtual_touchscreen`    


And events should flow from the newly created input device:


`hd /dev/input/event11 # or whatever udev assigns`    
00000000  df 32 48 4f a6 10 02 00  03 00 00 00 c8 00 00 00  |.2HO............|    
00000010  df 32 48 4f ab 10 02 00  03 00 01 00 2c 01 00 00  |.2HO........,...|    
00000020  df 32 48 4f bf 10 02 00  00 00 00 00 00 00 00 00  |.2HO............|    
00000030  e3 32 48 4f af af 09 00  01 00 4a 01 01 00 00 00  |.2HO......J.....|    
00000040  e3 32 48 4f bc af 09 00  00 00 00 00 00 00 00 00  |.2HO............|   
00000050  e7 32 48 4f 3d bb 05 00  01 00 4a 01 00 00 00 00  |.2HO=.....J.....|   
00000060  e7 32 48 4f 50 bb 05 00  00 00 00 00 00 00 00 00  |.2HOP...........|   
    
There is also experimental script to read /dev/input/eventX of some real device and output data for virtual_touchscreen.    
    
There is GUI application that can also provide data for virtual touchscreen    
(virtual_touchscreen.clj, bundled version: https://vi-server.org/pub/virtual_touchscreen.jar , SHA256 (virtual_touchscreen.jar) = 917698e287e1b707e09c3040d6347f5f041d7a60fef0a6f5e51c2b93ccd39f3c)    
It listens port 9494 and provides virtual_touchscreen input for connected clients.    
    
Example:    
`hostA$  java -cp clojure.jar clojure.main virtual_touchscreen.clj`    

`hostB#  nc hostA 9494 > /dev/virtual_touchscreen`    


## GUI

There is a GUI application that can also provide data for virtual touchscreen: `virtual_touchscreen.clj`. ([pre-built bundled version](https://vi-server.org/pub/virtual_touchscreen.jar); SHA256=917698e287e1b707e09c3040d6347f5f041d7a60fef0a6f5e51c2b93ccd39f3c, also available on Github Releases)

It listens port 9494 and provides virtual_touchscreen input for connected clients.

Example:

    hostA$  `java -cp clojure.jar clojure.main virtual_touchscreen.clj`   

    hostB#  `nc hostA 9494 > /dev/virtual_touchscreen`    

## Misc

There is also experimental script to read `/dev/input/eventX` of some real device and output data for virtual_touchscreen. It is long unmaintained although. Maybe see forks for alternative script.