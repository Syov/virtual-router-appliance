---

copyright:
  years: 2017
lastupdated: "2018-11-10"

keywords: nat, setup, 5400

subcollection: virtual-router-appliance

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:download: .download}
{:note: .note}
{:important: .important}

# Setting Up NAT Rules on Vyatta 5400
{: #setting-up-nat-rules-on-vyatta-5400}

This topic contains examples of the Network Address Translation (NAT) rules used on a Vyatta.

## One-to-many NAT rule (masquerade)
{: #one-to-many-nat-rule-masquerade-}

Enter the following commands in the prompt:

~~~
set nat source rule 1000 description 'pass traffic to the internet'
set nat source rule 1000 outbound-interface 'bond1'
set nat source rule 1000 protocol 'tcp_udp'
set nat source rule 1000 source address '10.125.49.128/26'
set nat source rule 1000 translation address 'masquerade'
commit
~~~

Connection request from machines in the `10.xxx.xxx.xxx` network are mapped to the IP on bond1 and receive an associated ephemeral port when going outbound. The intention is to assign one-to-many masquerade rule numbers higher so that they do not conflict with lower NAT rules that you may have.

You must configure the server to pass its internet traffic through the VRA so that its default gateway is the Private IP address of the managed virtual LAN (VLAN). For example, for `bond0.2254` the gateway is `10.52.69.201`. This should be the gateway address for the server passing Internet traffic.
{: note}

Use the following command to help troubleshoot NAT:

  '''
  run show nat source translations detail
  '''
  {: tip}

## One-to-one NAT rule
{: #one-to-one-nat-rule}

The commands below show how to setup a one-to-one NAT rule. Notice the rule numbers are set up to be lower than the masquerade rule. This is so that the one-to-one rules will take precedence over the one-to-many rules.

IP addresses that are mapped one-to-one cannot be masqueraded. If you translate an IP inbound, you must translate that IP outbound in order for traffic to go both ways.
{: tip}

The following commands are for a source and destination rule. Type `show nat` in configuration mode to see the NAT rule type.

  Use the following command to help troubleshoot NAT: `run show nat source translations detail`.
  {: tip}

Enter the following commands in the prompt after ensuring you are in configuration mode:

~~~
set nat source rule 9 outbound-interface 'bond1'
set nat source rule 9 protocol 'all'
set nat source rule 9 source address '10.52.69.202'
set nat source rule 9 translation address '50.97.203.227'
set nat destination rule 9 destination address '50.97.203.227'
set nat destination rule 9 inbound-interface 'bond1'
set nat destination rule 9 protocol 'all'
set nat destination rule 9 translation address '10.52.69.202'
commit
~~~

If traffic comes in on IP `50.97.203.227` on bond1, that IP will be mapped to IP `10.52.69.202` (on any interface defined). If traffic goes outbound with the IP of `10.52.69.202` (on any interface defined), it will get translated to IP `50.97.203.227` and proceed out bound on interface bond1.

IP addresses that are mapped one-to-one cannot be masqueraded. If you translate an IP inbound, you must translate that same IP outbound in order for its traffic to go both ways.
{: note}

## Adding IP ranges through your VRA
{: #adding-ip-ranges-through-your-vra}

Depending on your VRA configuration, you may want to accept specific IBM© Cloud IP addresses.

New vRouter deployments come with IBM Cloud's services network IP addresses defined in a firewall rule called `SERVICE-ALLOW`.

The following is an example of `SERVICE-ALLOW`. This is not a complete private IP rule set.

```
~~~
set firewall name SERVICE-ALLOW rule 1 action 'accept'
set firewall name SERVICE-ALLOW rule 1 destination address '10.0.64.0/19'
set firewall name SERVICE-ALLOW rule 2 action 'accept'
set firewall name SERVICE-ALLOW rule 2 destination address '10.1.128.0/19'
set firewall name SERVICE-ALLOW rule 3 action 'accept'
set firewall name SERVICE-ALLOW rule 3 destination address '10.0.86.0/24'
~~~
```

Once you have defined the firewall rules, you can assign them as you see fit. Two examples are listed below.

Applying to a zone: `set zone-policy zone private from dmz firewall name SERVICE-ALLOW`

Applying to a bond interface: `set interfaces bonding bond0 firewall local name SERVICE-ALLOW`
