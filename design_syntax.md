Design Syntax
=============

```

Design Syntax
=============

This chapter explains the syntax used for creating designs. It does so by showing some examples and following up by a syntax specification.

Pins:
-----

Inputs and outputs have arbitrary chosen identifier like so:
  Inputs: inPin1, inPin2, abc:
  Outputs: outPin1, outPint2, outPin3, outPin4, def;

Parts consist of an arbitrary chosen identifier followed by a part type in all upper-case:
  Parts: myNand NAND, myNot NOT, anotherNand NAND, andAnAnd AND;

Wires can connect:
  Wires:
    inPin1 -> outPin1,          //Inputs to outputs
    inPin1 -> myNot.in,         //inputs to part-inputs
    myNot.out -> outPin2,       //part-outputs to outputs
    myNot.out -> myNand.in1,    //part-outputs to part-inputs
    1 -> outPin3,               //constants so output
    0 -> myNand.in2;            //constants to part-inputs

Only "1" or "0" can be used as constant. If any input pin has no wire attached to it, it will automatically get the input "0".

Busses:
-------

The examples so far involved only pins. If a design has to use many pins,
busses can be used instead. To define a bus containing multiple pins, one can write:
  Inputs: inBus1[8], inBus2[8], inBusWide[16], inPin1;
  Outputs: outBus1[8], outBus2[8], outBusWide[16], outPin;
This way e.g. inBus1 is a bus with a bus width of 8 (meaning it contains 8 separate pins) and inBusWide has bus width of 16.

When specifying wires in the "Wires"-section, busses of same size can be connected:
  inBus1 -> outBus1

Single pins inside the bus can be selected and connected as well:
  inBus1[3] -> outPin
Connects the third pin of inBus1 to the outPin. Node that inBus1[3] represents a single pin in the "Wires"-section, while in the "Inputs"- and "Outputs"-section it would define a bus of size three. Also note that the numbering of the pins starts with "1" (no zero-based index).

It is possible to select not only a pin, but also a sub-bus of a bus:
  inBus1 -> outBusWide[9:16]
  inBus[1:8] -> outBusWide[9:16]
Both lines mean the same thing. This connects inBus1[1] with outBusWide[9], inBus1[2] with outBusWide[10], etc. up to inBus1[8] with outBusWide[16].

A single pin can also be connected to a (sub-)bus (not vice versa). In this case the pin connects to all pins of that (sub-)bus
  inPin1 -> outBus[2:4] //connects to sub-bus [2:4]
  inPin1 -> outBus2     //connects to complete bus

When working with busses, it is important to know the proper notation of values. Let's say you have a bus "bus[8]" with value "00001111". This means that pin "bus[1]" has value "1" and pin "bus[8]" has value "0". Therefore you read the separate pins from right to left, when written as a binary number.

Formal Specification:
---------------------

A design consists of the "Inputs", "Outputs", "parts" and "Wires" section"
[design] = [input_section] [output_section] [part_section] []wire_section

Each section starts with a corresponding section identifier, followed by a colon, one or more of its corresponding elements and ends with a semicolon:
[input_section]  = "Inputs:"  [input] (, [input])* ";"
[output_section] = "Outputs:" [output] (, [output])* ";"
[part_section]   = "Parts:"   [part] (, [part])* ";"
[wire_section]   = "Wires:"   [wire] (, [wire])* ";"

Inputs and outputs can be either single pins (in which case they consist of a simple identifier) or a busses (in which case their bus width has to be specified).
[inputs] = [input_pin_id] | ([input_bus_id][bus_size])
[outputs] = [output_pin_id] | ([output_bus_id][bus_size])

Identifiers have to start with a letter optionally followed by an arbitrary amount of letters or numbers:
[input_pin_id]  = [id]
[output_pin_id] = [id]
[input_bus_id]  = [id]
[output_bus_id] = [id]
[id]            = [letter] ([letter | digit])*
[letter]        = "a"|...|"z"
[digit]         = "0"|...|"9"

A bus size is given as a number in square brackets. A number has at least one digit.
[bus_size] = "[" [number] "]"
[number]   = [digit]+

Parts can be written as pairs of part identifiers and part types. Part types are always upper-case:
[part] = [part_id] [part_type]
[part_id] = [id]
[part_type] = [uppercase_letter] ([uppercase_letter | digit])*
[uppercase_letter] = ("A"|..|"Z")

Wires have a start and end. Start and end can be regular pins, busses of the designed element or of its parts:
[wire] = [start] "->" [end]
[start] = [pin_value] | [input_pin] | [input_bus] | [part_id]"."[output_pin] | [part_id]"."[output_bus]
[end] = [output_pin] | [output_bus] | [part_id]"."[input_pin] | [part_id]"."[input_bus]

A pin can be either a reference to a pin id or a specific pin inside a bus:
[pin_value] = "0" | "1"
[input_pin] = [input_pin_id] | [input_bus_id][pin_selection]
[output_pin] = [output_pin] | [output_bus] | [part_id]"."[input_pin] | [part_id]"."[input_bus]

A bus can be either a reference to a bus id or a group of pins inside a bus:
[input_bus] = [input_bus_id] | [input_bus_id][bus_selection]
[output_bus] = [output_bus_id] | [output_bus_id][bus_selection]
[bus_selection] = "[" [number] ":" [number] "]"
```
