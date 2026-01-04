# Igniter
Analogue igniter circuit for transistorised ignition with mechanical advance

<img src=./docs/images/Igniter_schematic.png alt= “” width=60% height=60%>

### Kettering ignition
This project replaces the igniter used in earlier transistorised Kettering vehicle ignition. 
Old **points** ignition systems use a form of transformer (commonly referred to as an ignition coil) to create a spark for a petrol engine. 
During operation, the low-tension (LT) coil in this transformer is energised with 12 volts, creating a magnetic field. 
When the spark is required, the power is cut to the low-voltage coil, and the magnetic field in the coil transformer collapses, sending energy through the coupled high-tension coil. 
The high-tension coil output has lower resistance to current flow than the low-tension coil as it has more windings and generates higher voltage at lower amps, allowing the circuit to flow through a sparkplug air gap rather than the low-tension coil circuit. This Kettering system is simple and the current through the LT coil can be switched with a mechanical switch, "points" following a cam driven by the engine. A capacitor (**condensor**) is added to the 12v side of the circuit to give the points sufficient time to create a large air gap and force energy discharge through the HT side of the coil transformer. 
The timing of this spark ignition changes relative to the rotational speed of the engine; ignition occurs just after the piston starts a downward cycle in order to reduce mechanical shock. The optimal time to ignite the fuel mixture after the pistons change in direction varies with engine speed, Kettering mechanical systems use a simple mechanical spark advance mechanism which alters the switching points position relative to the cam operating the switch. The cam profile is designed to provide current to the coil for enough time to create a magnetic field powerful enough for an adequate spark. This field generation time is known as **dwell**.
However, switching several amps of current through mechanical contacts at several thousand cycles per minute causes wear and spark erosion. These switch point contacts have to be regularly adjusted, cleaned and replaced. The springs that close these points contacts are of variable quality and strength, and this process of regular servicing is a drawback to old vehicles.
### Transistor-assisted ignition
Manufacturers then adopted power transistors to perform the task of the points switching, this is the "igniter" used in 1980s transistorised Kettering ignition design, before the adoption of capacitor discharge ignition, **CDI**, which uses a switched capacitor discharge through a step-up transformer to create the spark. These early systems still used the same mechanical spark timing advancer. 

Early transistorised ignition placed a magnet on the crankshaft to generate an electrical signal in a nearby "pick-up" coil, a variable reluctance sensor similar to a magnetic tape head. This signal then switched the transistor controlling 12 volt power to the ignition coil. Later designs adopted Hall-effect sensors to detect crankshaft position from a passing magnet and a microprocessor to calculate the spark advance timing. 

I had tried several aftermarket ignition systems and found reliability to be an issue. Cheaper aftermarket systems would place the power transistors inside the points cover, where engine heat and high current would cause them to prematurely fail. Hall-effect pickups are also more sensitive to heat than simple pick-up coils, call-out mechanics would describe these as the most common failure that would strand a vehicle. While this transistorised Kettering design is simple and reliable, 1980s electronics eventually die.

As the design of high power transistors has improved since the 1980's, it seemed rational to create a reliable igniter using the original pick-ups to switch standard ignition coils. Several designs already exist which use a low-power transistor to switch the power-transistor, people have used ignition modules from cars or designed signal-conditioning circuitry to operate with the signal from pick-up coils with varying success. The disadvantage with the signal from an inductive pick-up coil is that it creates a low voltage pulse at low speed and a high voltage pulse at high speed. Any signal detection that relies on the voltage passing a set threshold would vary with engine speed.

A second problem with this design is that the dwell, or duration the coil is energised, cannot be established from a single signal without adding digital processing or some equivalent bucket-brigade analogue circuit. I found that manufacturers used a **Constant Energy** design to address this requirement. The magnetic pickup signal was designed so that the duration of the pulse would give two signals, beginning where the signal crossed a set threshold and ending where the signal dropped below a second threshold. 
The duration of this value corresponded roughly to the dwell requirements at a specific RPM, i.e. a constant energy is supplied to the coils at all engine speeds.
Measuring this pick-up coil voltage revealed a problem; the variation between peak voltage at high RPM and low RPM is considerable, making the design of accurate circuitry operating across this range complex. Instead of using clipping diodes on the voltage signal, I found that the current signal provided a consistent signal shape across RPM while retaining the duration required for dwell. The design here uses a current op-amp (or Norton amp) to generate a square-wave power transistor trigger signal at set current values. 

<img src=./docs/images/ce_waveform.png alt= “” width=60% height=60%>
<img src=./docs/images/scope.png alt= “” width=60% height=60%>

### Signal conditioning
The ancient LM3900 is a quadruple current differencing op-amp, in the [technical notes](https://www.ti.com/lit/an/snoa653/snoa653.pdf?ts=1767440537261) a "squaring amplifier" is described creating a clean square-wave signal based on the input from a variable reluctance transducer, or inductive pick-up. With slight variations of resistance values to adapt to the intended signal start and stop thresholds, this circuit is an elegant solution to pick-up signal conditioning with minimum components. The LM2900 is a similar chip that operates across wider temperature range.

<img src=./docs/images/tech_note_squaring_amplifier.png alt= “” width=60% height=60%>

### IGBT switching
The main switching transistors used in this design are N-channel Insulated Gate Bipolar Transistors, IRGBC40U. The advantage of these modern designs they have the high current and voltage switching capacity associated with power MOSFETs but do not require the additional driver circuitry to switch MOSFET gates. These IGBT gates are driven by common 2N2222 transistors with a diode and resistor network to allow rapid gate charge drain. Each IGBT has an additional 1N4007 diode to reduce the counter EMF voltage at the transistor, along with a high-voltage capacitor to dampen the voltage spike during switching. Note that the heatsink tab of the TO220 case should not be electrically connected to ground, and insulation is required for an earthed heatsink.

As there are no digital components in this circuit, nor particularly delicate components, the power filtering uses an 18 volt transient voltage suppression diode and a low-pass filter based around inductors from an ATX PC power supply. There is a diode to protect against reverse polarity. Finally there is a separate 12v square wave signal driven by an independent transistor for the purpose of a digital tachometer signal. 

The board design uses chunky screw connectors for the pick-ups coils and ignition coil connections. These are best changed for soldered wires terminated in the bullet and block connecters for the motorcycle model. This prototype design used a large diecast metal box for heat dissipation and component access. In retrospect this turned out to be a reliable design that does not transfer much heat and can use a simple plate heatsink with component epoxy potting. So far around 20,000 miles without issue in ambient temperature from -5C to 42C.
If using this design with a Kawasaki KZ650/Z650, GT550 etc, the mechanical advancer must be changed for the advancer used with transistorised ignition models. 

The Gerber RS274X files are accepted by most PCB manufacturers, this is a double-sided board using through-hole plating. Solder the resistors first, and clean flux off the board prior to use, particularly around high value resistors. I used a conformal acrylic coating over the finished board to improve resistance to the elements. This ignition is being used with Kawasaki LTD Spectre magnetic pickups, 5 ohm ignition coils and 7mm silicone/graphite HT cables. Note that it is safe to leave this ignition on without running the engine as it does not switch power to the coils when stationary.


### BOM

| Value | Silkscreen Outline | Number | Reference Designation |   
| ------------- | ------------- |  ------------- |  ------------- | 
|0.1uF | CAPR1500-1700X650X1150 | 2 | C2A,C2B |
|0.1uF |       KERKO6X5R5 |   1|    C12|          
|1N4007 |      DO-41|        3|    D2A,D2B,DP1|  
|1N4148|       DO-35|        2|    D1A,D1B|      
|1kOhm|        R0204R7_5|    2|    R8A,R8B|      
|2.2kOhm |     R0204R7_5|    2|    R9A,R9B|      
|2MOhm|        R0204R7_5|    1|    R3A|          
|2MOhm|        R0204R7_5|    3|    R3B,R7A,R7B|  
|2MOhm|        R0204R10|     2|    R6A,R6B      |
|2N2222A|      TO-92(Bent)|  3|    Q2A,Q2B,QT1  |
|3MOhm|        R0204R7_5|    2|    R4A,R4B      |
|10MOhm|       R0204R7_5|    2|    R5A,R5B      |
|10uF|         ELKO5R2_5|    1|    CP2          |
|10Ohm|        R0204R7_5|    3|    R14A,R14B,RT2|
|12kOhm|       R0204R7_5|    3|    R10A,R10B,RT1|
|50uH|         DRO_TDK14X8|  1|    LP1          |
|100uF|        ELKO8R3_5|    1|    CP1          |
|200kOhm|      R0204R7_5|    4|    R1A,R1B,R2A,R2B|
|220Ohm|       R0204R7_5|    2|    R11A,R11B    |
|BZV85-C18|    DIO_P6KE_V|   1|    TVS1         |
|HDR1X2|       SMK2R5MIL_A|  4|    J1,J2,J4,J7  |
|HDR1X2|       SMK2R5MM_A|   1|    J5           |
|IRGBC40U|     TO220V|       2|    Q1A,Q1B      |
|LM3900N|      DIP14300|     1|    U1  |

<img src=./docs/images/pcb1.png alt= “” width=60% height=60%>
<img src=./docs/images/pop_pcb.png alt= “” width=60% height=60%>
