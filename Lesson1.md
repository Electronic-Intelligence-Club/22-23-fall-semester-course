# Introduction to Github & Arduino

## What is Github?
** Let's build from here **
- A complete developer platform to build, scale, and deliver secure software.

## Arduino IDE

### Intro
- Before diving into the work of wiring and programing, let’s get well prepared on your PC

- Arduino IDE is a powerful tool to program all Arduino hardware products

- A place where you can write your Arduino Code, compile it and uploading it to your Arduino boards


### Download
- (may take a long time to get access to the website, change DNS to 8.8.8.8 if necessary)

Official Website: https://www.arduino.cc/


### USB drive(optional)
Some PCs do not have USB drive software in advance

This may lead to the problem that your PC can not identify the Arduino hardware you plug in

It is necessary to have one before your start uploading the program.

#### Check if you need to download a USB drive
1. Open Arduino IDE
2. Connect to Arduino Uno with USB Wire
3. Click on 'Tools' and move to 'Port'

* if your port does not show a ‘USB-Port’, then perhaps:
- The USB wire does not work, or
- A USB drive on your PC is needed
Just get one from the Internet

* If your Port shows a USB connection with Arduino Board, then you are already done.

###  Breadboard & Wire Connection

- Breadboard is a construction base to build temporary electric circuits. 

- A frequently used tool to design circuits before soldering wires between actual electronic components

- In the whole semester & in the following hands-on practices in the future,  we will use this stuff to build our circuit. 

### First Arduino Program: Blink
Materials:
- Breadboard x 1
- 560 ohm resistor x 1
- 10 mm LED x 1
- Jumper Wires x n
- Arduino Uno Board

### Arduino Code
- Arduino Code is written with C language
- Following the syntax as well as the style of C
- For students who have learned Python, Arduino code is really easy with some simple documentation reading
- For students who do not have any programing experience, then you may need a really hard work to follow

* Good Chinese Resource to learn Arduino Code:

Arduino 函数参考：https://arduino-wiki.clz.me/?file=home-%E9%A6%96%E9%A1%B5
Arduino 编程基础：https://www.arduino.cn/thread-45048-1-1.html

* English Version Resources to learn Arduino Code:

http://forefront.io/a/beginners-guide-to-arduino/
https://www.youtube.com/watch?v=AJkEQ9t4WfY

### Arduino Code: Structure(Boring but important)
- Functions of Arduino code:
1. Void Setup()
Only run once right after the Arduino works

2. Void loop()
Loop infinitely as long as not powered-off

3. User-defined Function()
Customize your own function for your specific need
