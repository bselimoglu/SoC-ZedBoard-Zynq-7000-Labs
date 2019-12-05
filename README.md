# SoC-ZedBoard-Zynq-7000-Labs
Hardware and Software Co-design implementations.
The files is implemented for System-on-Chip Design Lecture's laboratory sessions.
# LAB1
## Design and Working Principle
In lab1, except given template, I wrote necessary states and signals to mod_mul_with_oled.vhd. Also, in order to enable oled display of Zedboard example_en signal set with added states by me. Given mod_mul_with_oled template has LoadA_0, LoadA_1, LoadB_0, LoadB_1 states to get the input a_mon and b_mon as input of MonPro function. After these states, I designed states for modulo, addition or division operations. For a_mon and b_mon inputs swi_en main input from Zedboard is used. First most eight bits are taken and then other eight bits. To control the received inputs from user, debounced switch is used. To start the calculation again debounced signal is waited. After calculation is start, every rising edge of the clock cycle one operation is done. When Zedboard is ready to calculate the modulus one led is set, after the calculation a different led is set and state will be Done state.

![](https://github.com/bselimoglu/SoC-ZedBoard-Zynq-7000-Labs/blob/master/_fsm.png)

The fsm diagram of system is tried to be shown briefly. From s1 to Done states calculate the Montgomery product algorithm (MonPro). Each state operates one operation. To take modulus; the least 16-bits are taken and to divide; the most 17-bits are taken.When the s1 state is executed led_ready_carpim is set and we can understand that calculation can start. After calculation is done, another led is set and again we can understand it is the end of algorithm.
If we can choose a=2 and b=3, for our vhdl code we need to enter for a_monpro = X”001D” and b_monpro = X”0068” and if we chose n=121, n_prime will be 27081 according to 〖R.R〗^(-1)+〖N.N〗^'=1 equation. In this equation R is 2^16 for our hardware IP.
# LAB2: Programming Zynq using Vivado on the ZedBoard
## The Purpose
The purpose of this laboratory is create a simple ARM Cortex-A9 based processor using Vivado and SDK (Software Development Kit). Vivado is used to create the hardware system and SDK (Software Development Kit) is used to create an example application to verify the hardware functionality and run a C program on the ARM processor. In SDK, it is aimed to access memory to read from and write to it with using a memory intensive application benchmark.
## The Architecture
The design flow of the lab is listed below:
  
1. Create a Project using Vivado

2. Create Processor System using IP Integrator

3. Generate Top-Level HDL and Export to SDK

4. Create a Memory Test Application in SDK

5. Verify Functionality in Hardware

To create a processing system (PS) design IP Integrator is used with ARM Cortex A9 core (PS), UART for serial communication and DDR3 controller for external DDR3 SDRAM memory. In PS the only selected configurable block is UART . All other peripherals will be deselected. The IP block design of processing system is shown in Figure 1. 
![](https://github.com/bselimoglu/SoC-ZedBoard-Zynq-7000-Labs/blob/master/zynq7_ps.png)

The next step is generate IP Integrator Outputs, the top-level HDL, and start SDK by exporting the hardware. After open the SDK, one of the standart projects template Memory Tests is generated to test the memory. The test in hardware JTAG and USB ports are used. To establish the serial communication using Terminal, JP7 is set to select USB power, and JP5 is set to JTAG. After output of memtest.c are seen on terminal, I created 2D matrix multiplication with row by row enhanced sequential multiplication algorithm. The algorithm is shown below. 
![](https://github.com/bselimoglu/SoC-ZedBoard-Zynq-7000-Labs/blob/master/Row%20by%20row%20enhanced%20sequential.png)
First two 2D matrix are written on different memory adresses and the result of the multiplication was written to another memory address. The matrix elements were read from the memory when calculating the multiplication.
# LAB3: Interfacing IP Cores in PL with ARM Processor in Zynq
## The Purpose
The purpose of this laboratory is create two AXI GPIO (General Purpose Input/Output) IPs core into PL part of SoC with using Lab3 project file. After GPIOs are created over all system will be intefaced with ARM Cortex-A9 processor. The used parts of SoC are ARM Cortex A9 core (PS part), UART for serial communication, DDR3 controller for external DDR3 SDRAM memory and Two GPIO units for LEDs, push buttons and DIP switches. Once the tasks are completed, we will have the ability to; configure the GP master port of the PS to communicate with the IP cores in the PL, add hardware IP cores into your SoC design, and  setup complier and configuration settings in designing a custom SoC design.
## The Architecture 
The design flow of the lab is listed below:

1. Open the previous project (Lab2) in Vivado

2. Add and configure GPIO peripherals in the system using IP Integrator

3. Connect external peripherals

4. Generate bitstream and export to SDK

5. Create a Memory Test application in SDK

To continue from the previous (Lab2) project file, project file is opened and saved it as Lab3. After Lab3 project is created processing system is updated to re-customize IP. GP0 M AXI interface, clock reset FCLK_RESET0_N and PL fabric clock FCLK_CLK0 are enabled. Over all PS part is shown below.

![](https://github.com/bselimoglu/SoC-ZedBoard-Zynq-7000-Labs/blob/master/zynq7_ps_2.png)

After PS part is updated, from Add IP icon AXI GPIO is selected and its properties are arranged the given lab instructions. First one of the GPIOs is switches and it automatically connected. After switches GPIO is added, buttons GPIO is added but this time its connection done manually according to laboratory instructions. The connections are shown Figure 2. After buttons is addressed, system is synthesized, and Open Synthesized Design is opened to check the I/O Ports. After that bitstream is generated and export the hardware to SDK.
![](https://github.com/bselimoglu/SoC-ZedBoard-Zynq-7000-Labs/blob/master/system_2.png)

In SDK, empty project is created but before that standalone_bsp_0 must be selected. After new application project is created, given lab3.c file should be import to project and output of the project can be seen in SDK Terminal output.
## The Results
System is consisting of processing system and hardware components in programmable logic part of SoC. Processin system has DDR3 Memory controller and UART communication peripheral. The programmable logic has two GPIO and one AXI interconnect. GPIO peripherals were connected to processing system through 32b Master GP0 interface. Switches and buttons connections were established. Software was uploaded after downloading the bitstream and executing the program.
# LAB4: Integrating a Custom IP to the System on Chip
## The Purpose
The purpose of this laboratory is creating a custom hardware IP core and integrate it into a processing system that is previous experiment project by using the Vivado IP Packager. Also, AXI4-Lite interface was used to create a peripheral. Previous lab file lab3 was extended by adding custom AXI peripheral that is multiplier_ip. To create the custom IP, Manage IP part of Tasks section is used, and IP was added with IP Packager. After that SDK is opened and chip was controlled.
## The Architecture 
The design flow of the lab is listed below:

1. Creating a custom IP using peripheral template 

2. Packaging peripheral using IP packager

3. Setting the project setting

4. Adding the custom IP

5. Generating bitstream and exporting to SDK

6. Creating a HelloWorld application in SDK with custom IP device drivers

7. Verifying the design in hardware.

In order to create IP core, Manage IP is opened, and new IP settings are managed. Simulator language is set to mixed to be able to use different languages. After that manage the settings, new IP is created with specifying the name, version, display name, description and location. Its interface mode is selected slave to read the outputs from it. Template multplier_ip’s vhd files are arranged to do multiplication operation on slv_reg0 and svl_reg1 operands and the least significant 8-bit of the result is displayed on the leds. At top level of design, parameterized output port to leds was created and AXI write data in sub-module was connected back up to external output led port. The component lab4_ user_logicis added to slave vhd file. And lab_user_logic file added as design source. The system is synthesized and except these settings, Package IP steps are configured. Ip category is selected as basic element and other steps are arranged according to instructs.
To continue from the previous (Lab3) project file, project file is opened and saved it as Lab4. After Lab4 was created, multiplier_ip_v1_0 IP is added as AXI peripheral. Block Designed was opened and mutiplier_ip is added the overall system and connections were connected automatically. To pin locations were connected necessary output leds with the lab4_zedboard_xdc constrain file. Overall system block design is shown in Figure 1. After output products is generated bitstream is generated and WNS is checked, and it was positive, and hardware was exported. SDK is launched and empty hello world project is created. In its library multiplier_ip.h is added to hello world library and helloworld.c file is updated to write the multiplicands to according address places also register offsets are necessary to write true address places and lastly data was sent to write register. For instance, MULTIPLIER_IP_mWriteReg(0x43C00000, 0, 4);

•	0x43C00000 is base address, 0 is register offset and 4 is data.

![](https://github.com/bselimoglu/SoC-ZedBoard-Zynq-7000-Labs/blob/master/block_design_4.png)

## The Results
System is consisting of processing system and hardware components in programmable logic part of SoC. Processing system has DDR3 Memory controller and UART communication peripheral. The programmable logic has two GPIO, one AXI interconnect and one IP core which is created and named multiplier_ip. The custom IP has four register, first and second registers are multiplied and result was written third one . All register has 32-bit, interface type is lite, and mode is slave.  GPIO peripherals and IP core were connected to processing system through 32b Master GP0 interface. Switches and buttons connections were established. Software was uploaded after downloading the bitstream and executing the program. Multiplication was performed by accessing the memory addresses and write the data these addresses. IP core does the multiplication operation and third register has the result. 
# LAB5: Custom Interfacing in System on Chip
## The Purpose
The purpose of this laboratory is interfacing a custom hardware IP core which has a specific handshaking protocol and integrate it into a processing system that is previous experiment project by using the Vivado IP Packager. Unknown IP core will be provided, and it will be reused instantiated the given IP in our custom AXI peripheral to interface with processing system.
To create the unknown custom IP, Manage IP part of Tasks section will be used and given VHDL code added to IP design. After that IP will be added with IP Packager, then SDK will be opened, and we will communicate with the custom AXI IP core via software application on the ARM processor. Final aim will be to figure out the functionality of the given IP core by writing and reading the registers in our AXI IP peripheral.
## The Architecture 
The design flow of the lab is listed below:

1. Configure GPIO peripherals if it is necessary, 

2. Using Vivado IP packager create a custom IP peripheral that can instantiate another IP core which has a specific interface,

3. Generating bitstream and exporting to SDK,

4. Creating a HelloWorld application in SDK with custom IP device drivers,

5. Demonstrate the functionality of Unknown Custom IP

In the previous lab (LAB4) we create a SoC design that contains PS, multiplier IP which is created by us and all the VHDL codes are provided and two GPIO peripherals. This time we will design a custom IP again and VHDL code will be provided but we will try to figure out the functionality of this IP core. IP core’s specifications were provided, and we will just integrate it to IP core’s top module. All the knowledge from previous labs will be used and SoC architecture will be completed.To start the lab5, optionally a new IP will be designed, or the previous IP will be edited again. In my opinion, the new IP designing in the laboratory is more logical than the editing existing IP. Because editing the IP takes more time. The given specifications of unknown IP are at least 8 different 32-bit registers and can write the output of the result to LED. Some of the registers will be used as input of unknown ip or some of them are input control signal. Output signals of the ip also can be read from these registers. After made the design, it is time to test design of the hardware and we will do reverse engineering by accessing IP from software in ARM processor and find out what this IP core is really doing. We will write and read the values of IP through S_AXI interface of our custom IP.
## The Results
In the weekend work of the lab5, given IP core block design is shown below. weeknd_0 has 8 different 32-bit registers and its unknown ip file has 3(a,b,c) input, 1(result) output and handshake signals. IP uses 8 leds to write the result to them.
![](https://github.com/bselimoglu/SoC-ZedBoard-Zynq-7000-Labs/blob/master/system_5.png)
The given unknown ip VHDL file connected as shown in the Figure 2. slv_reg0 is used for ap_start and ap_rst signals. Sgnal is connected slv_reg1 register to read the ap_done, ap_idle and ap_ready output signals. Slv_reg2, slv_reg3 and slv_reg4 are used for input signals respectively a, b and c of unknown_ip.vhd file. And result is connected to Led_out signal to figure out the what this IP core is really doing.

![](https://github.com/bselimoglu/SoC-ZedBoard-Zynq-7000-Labs/blob/master/connection_of_the_unknown_ip.png)

After observing the input and output signals, unknown IP core writes the c input signal through result.

# LAB6: Interrupts and Timers in SoC
## The Purpose
The purpose of this laboratory is creating Gpio interrupt channels and AXI Timer in hardware and configuring them in software for the programmable SoC platform and generate a timer interrupt and a button interrupt in the software when timer has expired, or button is pressed.
## The Architecture 
The design flow of the lab is listed below:

1. Add and configure GPIO peripherals to support an interrupt channel, 

2. Add and configure Timer module with an interrupt signal,

3. Configure the Zynq PS to enable the Interrupt ports,

4. Generate bitstream and export to SDK,

5. Create a software application in SDK

6. Verify the design in hardware

In the lab, before adding the GPIOs and AXI timer, the Zynq Processing System should be added, and necessary specifications must be enabled. After that run automation connection is done and DDR and FIXED_IO are made external. Also, Processor System Reset is consisted and so Gpios can be added now. After adding Gpios, buttons Gpio’s interrupt channel is enabled. Other Gpio has two channels, one of them for switches other one is for leds. Before connect interrupt signal to Znyq processor system, concat is needed. It takes two interrupt singal input and gives one of them to Zynq PS. And so, hardware design is done, we validate design and create hdl wrapper. Bitstream is generated and SDK is launched.
According to lecture notes, software part of the lab is based on the following programming flow.

•	Initialize peripherals

•	Configure and Initialize GIC via XScuGic_LookupConfig and XScuGic_CfgIntialize functions

•	Enable interrupt channels of periherals

•	Make the connection between the IRQ and the XScuGic_InterruptHandler Handler

•	Make the connection between the Interrupt sourcesn and Gic via XScuGic_Connect

•	Enable the interrupt source in the controller via XScuGic_Enable

•	Create or wait for an interrupt in processing system
## The Results
In the lab6, our main aim is that use SDK to define, develop, and integrate the software components of the same embedded SoC design for different applications. Configuring SDK functions according to application specific requirements is very important task in this laboratory.

# LAB7: Designing Memory Subsystem with DMA Controller to Optimize SoC Performance
## The Purpose
The purpose of this laboratory is accessing PS memory space from PL for higher performance communication in an SoC. In hardware design of the lab7, we enable a high performance (HP) slave port of the PS for CDMA unit to access memory space is PS from PL, add and connect CDMA controller in PL, and perform Direct memory access (DMA) operation between different memory regions in the system and measure the elapsed time for the data movement operation.
## The Architecture 
The design flow of the lab is listed below:

1. Add GPIO peripherals, 

2. Add a bock RAM (BRAM) unit with two ports,

3. Add two BRAM controllers,

4. Add a CDMA controller with interrupt channel,

5. Configure the Zynq PS to enable the interrupt ports,

6. Generate bitstream and export to SDK,

7. Create a software application in SDK and

8. Verify the design in hardware (demonstrate how DMA performs memory transfers).

In the lab, before adding the GPIOs, BRAMs, BRAM Controller, AXI Timer and CDMA, the Zynq Processing System should be added, and necessary specifications must be enabled. After that run automation connection is done and DDR and FIXED_IO are made external. Also, Processor System Reset is consisted and so Gpios can be added now. After adding Gpios, CDMA is added, and its Enable Scatter Gather option is unchecked. Other Gpio has one channels, one of the Gpios for switches other one is for leds. Before connect interrupt signal to Znyq processor system, concat is needed. It takes two interrupt singal input and gives one of them to Zynq PS. After that BRAMs and BRAM Controller are added, we validate design and create hdl wrapper. Bitstream is generated and SDK is launched.

According to lecture notes, software part of the lab is based on the Coherent AXI DMA-based Accelerator Communication. Its write to accelerator is based on following flow.

•	Processor allocates buffer,

•	Processor writes data into buffer,

•	Processor initiates DMA transfer.

It’s read from accelerator is based on following flow.

•	Processor allocates buffer,

•	Processor initiates DMA transfer,

•	Processor waits for DMA to complete and processor reads data from buffer.

## The Results
In the lab7, our main aim is that obtain higher performance by accessing PS part with master ports of PL part. In many applications need higher performance for example video processing. HP ports are designed for maximum bandwidth access to external memory and on-chip-memory (OCM). The speed order of the ports is the following: HP Ports > external DDR > OCM. HP is provides 64 bit bandwidth.





