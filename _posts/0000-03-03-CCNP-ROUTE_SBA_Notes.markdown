---
layout: post
title: CCNP ROUTE Skills Based Assessment Notes
category: notes
---

At my Cisco academy, you are allowed to bring notes/commands to the exam.  
These notes ensured I got a 100/100 in the SBA for CCNP ROUTE v6.

### TCL Script Example
	Router# tclsh					! Start the TCL shell
		foreach var {				! Begin the foreach loop
		X.X.X.X					! Array element
		Y.Y.Y.Y
		Z.Z.Z.Z
		} {
		ping $var }				! action against $var

### Setting up an interface
	 interface <int>				! Loopbacks will automatically go up when initialized
	   description ...				! Optional description for the interface
	   ip address <ip> <subnet mask>		! The address of the interface, including the subnet mask
	   clock rate <clock>				! Clock rate in Kb/s
	   bandwidth <bw>				! EIGRP needs bandwidth to do computation
	   no shutdown					! Open the interface

	   ipv6 address <ipv6>				! Set an IPV6 address
	   ivp6 rip <NAME> enable			! Enable RIPv1 via IPV6

	   tunnel source <int>				! The tunnel source interface
	   tunnel destination <ip>			! The tunnel destination IP
	   tunnel mode ipv6ip				! Allow IPV6 through the Tunnel

### Routing

#### Gateway of Last Resort

    ip route 0.0.0.0 0.0.0.0 <int>			! The the quad zero exit interface

#### Floating Static Routes

	ip route <network> <subnet> <destination ip>
	ip route <network> <subnet> <destination ip> <priority>

### Create BGP Autonomous System (AS)
	router bgp <AS>					! Initialize the BGP AS
	  no synchronization				! Only announce if local convergence
	  neigbor <ip> remote-as <AS>			! Declare neighbor
	  neigbor <ip> route-map <route-map> in		! Set route-maps for neighbor
	  no auto-summary				! Disable summarization
	  network <summary>				! Announce a network
	  ip route <network> <subnet> null0		! Add the nullroute
	clear ip bgp					! Refresh BGP (Use this if something doesn't update)

	route-map <routemap> <permit|deny> <no>
	  set local-preference <no>

### Create EIGRP Autonomous System (AS)
	router eigrp <AS>				! Initialize the EIGRP AS
	  network <network> [wildcard]			! Add the networks to be broadcast
	  passive-interface default			! Set all interfaces to suppress ALL EIGRP routing updates
      neighbor <DEST-IP> <INT>			! If passive interface is set, manually setup neigbor
	  no passive-interface <int>			! Re-enable the routing updates on this interface
	  no auto-summary				! Disable summarization
    interface <int>					! Enter the interface to set Hello interval
      ip hello-interval eigrp <AS> <sec>		! Hello interval, must be the same in each end
							! MD5 on EIGRP
    key chain <KEYCHAIN>				! Create a new keychain in config mode
      key 1						! First key
        key-string <KEY>				! Keystring

    interface <int>
      ip authentication mode eigrp <AS> md5		! Setup MD5 for link
      ip authentication key-chain eigrp <AS> <KEYCHAIN>

### Create OSPF
	interface <interface>				! Select the interface for Point to Point
	  ip ospf network point-to-point		! Setup OSPF point-to-point
	router ospf <AS>				! Initialize the OSPF AS
	  router-id <id>				! Set the Router-ID (Do this before creating networks)
	  area <no> range <start-ip> <end-ip>		! Optionally setup the Area
	  network <network> [wildcard] area <no>	! Add the networks to broadcast
	  passive-interface default
	  no passive-interface <int>
	  redistribute <routing protocol> <no> subnets metric <no>
	  distribute-list <acl> <direction>
	  default-information originate always
	interface <int>
	  ip ospf network broadcast			! Set OSPF to broadcast on the interface.
	  ip ospf priority <priority>			! Set priority (force DR)
      ip ospf network point-to-point		! Loopbacks must be point-to-point to broadcast correct mask 
    access-list <no> <deny|permit> <network wildcard|any> ! Remember to Deny, then Permit
	router ospf <AS>
	  area <no> stub				! STUB, on ABR
	  area <no> nssa				! NSSA, on ABR
	  area <no> stub no-summary			! Totally STUB, on ABR
	  area <no> nssa no-summary			! Totally NSSA, on ABR
	router ospf <AS>				! All areas must connect to Backbone, otherwise create virtual link
	  area <AS>	virtual-link <dest ip>		! Transit area (between 0 and X) - also opposite direction

### IPv6 Routing
	ipv6 unicast-routing				! Initialize routing for IPv6
	ipv6 router rip <process>			! Initialize RIP for the process
	interface <int>					! Select interface
	  ipv6 rip <process> enable			! Enable process on interface
