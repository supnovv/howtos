
# GOEP

Generic Object Exchange Profile  
Latest Version - V21 2012.07.24  
BARB - barb-main@bluetooth.org  

ABSTRACT

This profile defines the requirements for Bluetooth devices 
necessary for the support of the object exchange usage models. 
The requirements are expressed by defining the features and procedures that are required 
for interoperability between Bluetooth devices in the object exchange usage models.  

(define features and procedures for object exchange)

FOREWORD

The purpose of this document is to work as a generic profile document 
for all application profiles using the OBEX protocol.
Interoperability between devices from different manufacturers is provided for a specific service and usage model 
if the devices conform to a Bluetooth SIG defined profile specification.

A profile defines a selection of messages and procedures (generally termed capabilities) 
from the Bluetooth SIG specifications and gives an unambiguous description of the air interface 
for specified service(s) and usage model(s).

All defined features are process-mandatory. This means that if a feature is used, it is used in a specified manner. 
Whether the provision of a feature is mandatory or optional is stated separately 
for both sides of the Bluetooth air interface.

## 1. Introduction

The Generic Object Exchange profile defines the protocols and procedures that shall be used
by the applications providing the usage models which need the object exchange capabilities.
The usage model can be, for example, Synchronization (SYNCH), File Transfer (FTP), or Object Push (OPP) model. 
The most common devices using these usage models can be notebook PCs, PDAs, smart phones, and mobile phones.

Bluetooth Specification includes a number of separate specifications for OBEX and applications using it.

- Bluetooth IrDA Interoperability Specification [1]  
  Defines how the applications can function over both Bluetooth and IrDA.  
  Specifies how OBEX is mapped over L2CAP and TCP.  
  Defines the application profiles using OBEX over Bluetooth.

- Bluetooth Generic Object Exchange Profile Specification (This specification)  
  Generic interoperability specification for the application profiles using OBEX.  
  Defines the interoperability requirements of the lower protocol layers for the application profiles.

- Application Profiles  
  Define the interoperability requirements for applications using OBEX.  
  Does not define the requirements for the Baseband, LMP or L2CAP.  

PROFILE STACK

The following roles are defined for this profile:  
Server - the device provides an object exchange server to and from which data objects can be pushed and pulled;   
Client - the device that can push or/and pull data object(s) to/from from the Server.

```
Client    <--> Server  
OBEX      <--> OBEX  
SDP       <--> SDP  
LMP|L2CAP <--> LMP|L2CAP  
Baseband  <--> Baseband  
```

The Baseband, LMP, and L2CAP are the OSI layer 1 and 2 Bluetooth protocols.
SDP is the Bluetooth Service Discovery Protocol. 
OBEX [1] is the Bluetooth adaptation of IrOBEX [5].

> [1] IrDA Interoperability, version 2.0 or later  
> [5] IrDA Object Exchange Protocol (IrOBEX) with Published Errata, Version 1.5 (IRDA.ORG)

Client can push data to Server and pull data from Server. 
For the device containing the Server, it is assumed that 
the user may have to put it into the discoverable and connectable modes 
when the inquiry and link establishment procedures, respectively, are processed in the Client.

The profile only supports point-to-point configurations. 
As a result, the Server is assumed to offer services only for one Client at a time. 
However, the implementation may offer a possibility for multiple Clients at a time but this is not a requirement.

PROFILE FUNDAMENTALS

The profile fundamentals, with which all application profiles must comply, are the following:

1. Before a Server is used with a Client for the first time, 
   a bonding procedure including the pairing may be performed (see Section 8.3.1). 
   This procedure must be supported, but its usage is dependent on the application profiles. 
   The bonding typically involves manually activating bonding support and following a pairing procedure 
   as defined in GAP on the keyboards of the Client and Server devices. 
   This procedure may have to be repeated under certain circumstances; for example, 
   if a common link key (as a bonding result) is removed on the device involved in the object exchange.

2. In addition to the link level bonding, an OBEX initialization procedure may be performed (see Section 5.3) 
   before the Client can use the Server for the first time.
   The application profiles using GOEP must specify whether this procedure must be supported 
   to provide the required security level.

3. Security can be provided by authenticating the other party upon connection establishment, 
   and by encrypting all user data on the link level. 
   The authentication and encryption must be supported by the devices; 
   but whether they are used depends on the application profile using GOEP.

4. Link and channel establishments must be done according to the procedures defined in GAP (see [2]). 
   Link and channel establishment procedures in addition to the procedures in GAP 
   must not be defined by the application profiles using GOEP.

5. There are no fixed master/slave roles, and this profile does not require any lower power mode to be used.

## 2. Application Layer


## 3. Lower Layer Requirements


# IRDA

IrDA Interoperability  
Latest Version - V20r00 2010.08.26  
OBEX WG - OBEX-feedback@bluetooth.org  

ABSTRACT

The IrOBEX protocol is utilized by the Bluetooth technology. 
In Bluetooth, OBEX offers the same features for applications as within the IrDA protocol hierarchy, 
enabling the applications to work over the Bluetooth protocol stack as well as the IrDA stack.

## 1. Introduction

## 2. OBEX Object and Protocol

## 3. OBEX over L2CAP

## 4. OBEX over TCP/IP


# IrOBEX (IRDA.ORG)

Infrared Data Association Object Exchange (IrDA OBEX) Protocol:

OBEX is a compact, efficient, binary protocol that enables a wide range of devices 
to exchange data in a simple and spontaneous manner.  A major use of OBEX is a “Push” or “Pull” application, 
allowing rapid and ubiquitous communications among portable devices or in dynamic environments. 
For instance, a laptop user pushes a file to another laptop or PDA; 
an industrial computer pulls status and diagnostic information from a piece of factory floor machinery; 
a digital camera pushes its pictures into a film development kiosk, 
or if lost can be queried (pulled) for the electronic business card of its owner. 
However, OBEX is not limited to quick connect transfer disconnect scenarios - 
it also allows sessions in which transfers take place over a period of time, 
maintaining the connection even when it is idle. 
PCs, pagers, PDAs, phones, printers, cameras, auto-tellers, information kiosks, calculators, 
data collection devices, watches, home electronics, industrial machinery, medical instruments, 
automobiles, and office equipment are all candidates for using OBEX.

The following specifications are included.
- IrDA Object Exchange (OBEX) Protocol v1.5
- IrDA OBEX Test Specification  

Specifications are FREE to members and members will not be charged. Prices below are non-member fees. 
You will received an invoice as soon as your request is processed. Please choose either Version 1.3 or Version 1.5.
- IrDA OBEX Protocol v 1.3 ($1000) OR
- IrDA OBEX Protocol v 1.5 ($1500)
- IrDA OBEX Test Specification ($1000)