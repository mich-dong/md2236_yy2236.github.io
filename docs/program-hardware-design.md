---
title: Program/hardware design
nav_order: 2
---

# Software Design

The recurring theme of our software design boils down to information management. The key challenge in an Agent-Based Model is keeping track of each agent’s parameters, updating them as necessary, all within the limited hardware of the RP2040. 

We immediately ran into issues at the start with this approach (we did not have grids at the time). Because we could not localize a given ball to any specific part of the screen, we had to search through every pair of balls when checking for infections. This proved to fail with only a meager 100 balls, in which infections would not occur because by the time the sweep has reached the specific pairing of ball IDs, they are no longer in proximity. To resolve this, we implemented two features: the grids system. This way, for a given ball, we only need to sweep through the balls that’s in its grid, greatly reducing the runtime.

## Grids & Dynamic Arrays through Memory Allocation
The grids system assigns each ball to one of twelve grids, to which they are spawned in at the grid centers with a small random velocity to create stochastic modeling. The assigned grid number then determines the x and y coordinates at which the ball is repelled by the grid walls, which are calculated from the grid number. 
Despite seeming simplicity, the implementation of the grids and related features (travel, wall detection, color) proved to be complicated when achieved with C on the RP2040 due to the lack of native dynamic arrays. That is, C arrays have a set size that is decided when an array is initialized. For small features, we can simply create a larger-than-needed array. 

The grid system proved particularly problematic. For example, in order to sweep through the other balls in a ball’s grid when checking for infections, we need to be able to determine both which grid the ball is in, along with which other balls are in said array. This requires both an array keeping track of each ball’s grid number, but also an array per grid keeping track of the ball IDs in it. All of this, of course, had to be updated live whenever a ball changes grids. 
To implement this within limited memory, we created pseudo-dynamic arrays using malloc() and realloc(), two native methods in C that allows us to allocate a designated memory block that functions similar to an array. It can then be resized by reallocating a new memory block of the new size, before moving the data over. To achieve this, we wrote custom functions appendGrid() and popGrid(), the former which resizes up, then adds a specific ball ID to a specific grid array, and the latter removes a specific index in the grid array, before resizing down. 

This proved particularly difficult to implement for many reasons. Firstly, memory allocation issues are hard to debug, as they tend to manifest as the program freezing due to some key memory being overwritten. This symptom is incredibly hard to diagnose, and so often debugging boils down to trial and error, as well as deploying new features in small chunks. It is unhelpful that this issue can be caused by a variety of issues. Any error in the size of the allocation, which array is allocated, the order of variable assignments can all cause this issue. 

The sizes of the arrays were a key obstacle, as they cannot be determined from an array alone, but must be kept track of separately so that appendGrid() and popGrid() knows what size to reallocate to. In early development, we decided to keep the size of a grid array in the 0th index of itself. This design remains in parts of our code, but ultimately caused lots of errors because any for loop indexing error could overwrite the size, thus breaking the appendGrid() and popGrid(), thus causing stalling issues. (We thus transitioned to structs later on for other parts that required dynamic arrays) A significant portion of our development was thus spent on implementing dynamic arrays correctly. 

--- 

## Structs

--- 

## ADC

The ADC readings from the switches are managed on its own protothread in Core 0 at a rate of ~333Hz. Each loop, a counter counts up, going from 0 to 4, then back to 0. The counter value is then converted to binary, which is outputted through 3 GPIO pins to the external multiplexor. We then yield for 20us, allowing the multiplexor signal to settle, before we read the ADC channel pin connected to the multiplexor’s output. The value is then assigned to a temporary holder for the corresponding parameter. Digital oscillation filtering is done on all ADC values in order to remove low frequency oscillations in the analog values from inherent noise. We purposefully slowed down this thread to 333Hz rather than the system clock (250Mhz) as well in order to further filter out noise. 

--- 

## Infection

--- 

## Social Distancing 


--- 

## Death


---

## Sound Effects
A sound effect plays when infections occur, which are created with a 12-bit DAC (digital-to-analog converter), which transforms a digital sound signal at the desired frequency into an analog output sent through the 3.5 mm audio jack. This produces a clear “thunk!” sound for each collision. To reduce CPU overhead, we leverage the RP2040’s built-in DMA (Direct Memory Access) channels to continuously deliver the correct data to the DAC without direct CPU involvement. These DMA channels handle data transfers between memory and the GPIOs, allowing audio playback to run independently of the processor.

--- 

## Multithreading
Trial and error proved that no additional threads can be added to Core 1 besides the graphs animation thread without causing program error. Through employing both the cores of the RP2040, we are able to parallel compute parts of our program, fully utilizing our CPU usage. 

--- 

## Core 1: Graphs

--- 
## VGA Protocol 
This portion is taken from our Lab 2 Report to provide full context. 
The VGA protocol, including functions that provided basic animation functions were provided to us. The following paragraph describes not our code, but Professor Bruce Land and Professor Hunter Adams’.

The VGA protocol’s building block is the function drawPixel(), which essentially modifies the input pixel coordinate within the color signal’s character array to the input color. This array is then sent via a DMA channel to three PIO state machines on instance 0. Four bits of the character array is used to store color data. Most animation helper functions we used (drawRect(), fillRect(), drawCircle(), and writeString()) were built upon drawPixel(). However, clearLowFrame(), drawHLine(), clearRect() instead uses memset()and directly accesses the address of the VGA signal array to change it. This can only be done because the VGA character array stores neighboring pixels in neighboring addresses, so certain combinations of pixels can be changed directly this way. 

The PIO state machines are state machines that can be hard coded with assembly to run code, independently of the CPU, with access to DMA channels and GPIO signals. This makes them suitable for generating the appropriate VGA signal from the VGA character array. PIO state machine 0 is used to generate the HSYNC signal and PIO state machine 1 is used to generate the VSYNC signal, both slowed to the pixel clock rate of 25MHz; that is, the rate at which the VGA display updates. PIO state machine 2 generates the RGB signals at 125MHz. (These are default values that are later changed for optimization).

The RGB signal pulls 4 bits of data from the Tx FIFO at a time, which is automatically populated by DMA 0 (itself chained to DMA 1 so that it repeatedly updates the VGA signal array). To coordinate this, the HSYNC signal is used to signal the update of a single line on the VGA display. The VSYNC signal is used to signal the update of a single frame on the VGA display. State machine 2 is controlled by both VSYNC and HYSNC to only output the RGB signal when both are active. 

---

# Hardware Design

Our hardware design includes: a Raspberry Pi 2040 on a Pi Pico board, a MCP4822 DAC, a CD74HC4051 8-1 ADC Multiplexor, a 3.5mm audio jack, 5 10kOhms slider potentiometers, 1 reset button, 3 protoboards, a VGA PCB (designed and provided by Professor Hunter Adams), and a 3D printed enclosure. 

All of our designs were done on breadboard, then hand-soldered in order to improve connection quality, and longevity of the project. We aimed to minimize space usage, and layered our protoboards in order to save space while leaving access for the micro-usb port, the VGA port, and the 3.5mm audio jack. We put insulating tape between each protoboard in order to prevent unwanted electrical connections. 

---

## VGA Connection

ADD PHOTO 

There are 6 pins required to communicate via the VGA protocol with the screen: 4 colors (RED, GREEN (low), GREEN (high), and BLUE) and two synchronization signals (HYSNC and VSYNC). (See Diagram X for pinouts). These pins are connected to a custom PCB board with 330 Ohms resistors soldered in series with the BLUE, RED, and GREEN (high) signals and a 470 Ohms resistor in series with the GREEN (low) signal. These signals then connect to a VGA male head, which can be connected to a monitor via a VGA cable. The resistors, combined with the internal resistance of the display, decrease the input (3.3V) to 0.7V at the output voltage. (a different valued resistor is used on GREEN (high) in order to differentiate the two signals) This is because 3.3V is too high for the display and will damage the circuitry. Since there are four bits of data available for the color, this means that the total color signal is a 42=16 bits 25MHz signal. The synchronization pins are command signals: VSYNC determines when new frames are to be updated and HYSNC determines when each row of pixels within that frame is updated. The display is 640 pixels long, 480 pixels tall. 

--- 

## Multiplexor & Potentiometers

We connected 3 of the GPIOs of the RP2040 to the binary select pins for a 8-1 ADC Multiplexor so we can correctly sweep through each of the mux’s inputs. The output of the mux is then connected to the correct input GPIO pin on the RP2040. An ADC multiplexor must be used and not a digital one (such as the 74LS series) as we are passing analog signals through the mux. 

The five slider switches we used are 10kOhm potentiometers. We decided to use these over the traditional dial potentiometers to better visualize the maximum and minimum of the changeable parameters. These potentiometers are powered by the 3.3V rail off the RP2040, with the divider pin connected to the mux’s inputs. As we found digital filtering was sufficient in this case, we did not use any analog filtering. 

Originally, we planned on employing more ICs in order to minimize the work done by the Raspberry Pi Pico, as well as an opportunity to learn about different circuit designs. This plan involved generating a clock signal with a Pierce oscillator circuit, which would then be fed to a 4-bit counter. This 4-bit counter would thus count at the input frequency and be able to sweep through the 8-1 multiplexor entirely independent of the RP2040. Ultimately, we were not able to debug this design in time, and chose a simpler implementation. 

--- 

## Reset Button

In order to reset the simulation and load new programs onto the RP2040, we wired an external button to the RUN signal (pin 30) and ground (pin 28) so that we do not have to reach into the enclosure to reset. Though power cycling could work, using the RUN signal is friendlier on both the RP2040 and the user. 


