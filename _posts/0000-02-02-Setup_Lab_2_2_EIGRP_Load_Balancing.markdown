---
layout: post
title: Setup Lab 2 2 EIGRP Load Balancing
category: labs
tags: [CCNP ROUTE]
---
### Overview

#### IP Address

<table class="table table-striped table-hover">
	<thead>
		<tr>
			<th>Device</th>
			<th>Interface</th>
			<th>IP Address</th>
			<th>Subnet Mask</th>
			<th>Network</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<th rowspan="5">R1</th>
			<td>Lo11</td>
			<td>10.1.1.1</td>
			<td>255.255.255.252</td>
			<td>10.1.1.0/30</td>
		</tr>
		<tr>	
			<td>Lo15</td>
			<td>10.1.1.5</td>
			<td>255.255.255.252</td>
			<td>10.1.1.4/30</td>
		</tr>	
		<tr>
			<td>Lo19</td>
			<td>10.1.1.9</td>
			<td>255.255.255.252</td>
			<td>10.1.1.8/30</td>
		</tr>	
		<tr>
			<td>s0/0/0</td>
			<td>10.1.102.1</td>
			<td>255.255.255.248</td>
			<td>10.1.102.0/29</td>
		</tr>	
		<tr>
			<td>s0/0/1</td>
			<td>10.1.103.1</td>
			<td>255.255.255.248</td>
			<td>10.1.103.0/29</td>
		</tr>	
		<tr>
			<th rowspan="5">R2</th>
			<td>Lo21</td>
			<td>10.1.2.1</td>
			<td>255.255.255.252</td>
			<td>10.1.2.0/30</td>
		</tr>
		<tr>	
			<td>Lo25</td>
			<td>10.1.2.5</td>
			<td>255.255.255.252</td>
			<td>10.1.2.4/30</td>
		</tr>	
		<tr>
			<td>Lo29</td>
			<td>10.1.2.9</td>
			<td>255.255.255.252</td>
			<td>10.1.2.8/30</td>
		</tr>	
		<tr>
			<td>s0/0/0</td>
			<td>10.1.102.2</td>
			<td>255.255.255.248</td>
			<td>10.1.102.0/29</td>
		</tr>	
		<tr>
			<td>s0/0/1</td>
			<td>10.1.202.2</td>
			<td>255.255.255.248</td>
			<td>10.1.202.0/29</td>
		</tr>	
		<tr>
			<th rowspan="5">R3</th>
			<td>Lo31</td>
			<td>10.1.3.1</td>
			<td>255.255.255.252</td>
			<td>10.1.3.0/30</td>
		</tr>
		<tr>	
			<td>Lo35</td>
			<td>10.1.3.5</td>
			<td>255.255.255.252</td>
			<td>10.1.3.4/30</td>
		</tr>	
		<tr>
			<td>Lo39</td>
			<td>10.1.3.9</td>
			<td>255.255.255.252</td>
			<td>10.1.3.8/30</td>
		</tr>	
		<tr>
			<td>s0/0/0</td>
			<td>10.1.103.3</td>
			<td>255.255.255.248</td>
			<td>10.1.103.0/29</td>
		</tr>	
		<tr>
			<td>s0/0/1</td>
			<td>10.1.203.2</td>
			<td>255.255.255.248</td>
			<td>10.1.203.0/29</td>
		</tr>	
	</tbody>
</table>

### Configuration

#### Router 1

	Router > enable
	Router # conf t
	R1(config) # int lo11
	R1(config-if) # ip address 10.1.1.1 255.255.255.252
	R1(config-if) # int lo15
	R1(config-if) # ip address 10.1.1.5 255.255.255.252
	R1(config-if) # int lo19
	R1(config-if) # ip address 10.1.1.9 255.255.255.252
	R1(config-if) # int s0/0/0
	R1(config-if) # ip address 10.1.102.1 255.255.255.248
	R1(config-if) # clock rate 64000
	R1(config-if) # no shutdown
	R1(config-if) # int s0/0/1
	R1(config-if) # ip address 10.1.103.1 255.255.255.248
	R1(config-if) # no shutdown

	Router > enable
	Router # conf t
	R1(config) # router eigrp 100
	R1(config-router) # network 10.0.0.0
	R1# foreach address {
	10.1.1.1
	10.1.1.5
	10.1.1.9
	10.1.2.1
	10.1.2.5
	10.1.2.9
	10.1.3.1
	10.1.3.5
	10.1.3.9
	10.1.102.1
	10.1.103.1
	10.1.102.2
	10.1.102.2
	10.1.203.3
	10.1.203.3 } { ping $address }

	R1(config) # no ip cef
	R1(config) # int s0/0/0
	R1(config-if) # no ip route-cache
	R1(config-if) # int s0/0/1
	R1(config-if) # no ip route-cache
	R1 # router eigrp 100
	R1(config) # access list 100 permit icmp any any echo
	R1 # no debug ip pack 100
	R1 # ping 1.1.2.1 repeat 50
