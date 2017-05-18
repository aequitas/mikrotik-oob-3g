Configuring a Mikrotik RouterOS router to use a USB stick 3G modem as a out-of-band monitoring and management interface.

# Rationale
- Testing out the USB port on my Mikrotik router, a feature I left untouched to long.
- To remind me even when I'm not home that my ISP sucks.
- Experiment potential practical usecases for this kind of OOB management.

# Features:

- ...

# Todos:

- SMS monitoring alerts
- PPP OOB management (VPN/IPsec/IPv6?/call-home/on-sms trigger)
- Prepaid balance check via SMS (SALDO -> 1266, mikrotik scripting)

# Sources:

- http://dostmuhammad.com/blog/disable-pin-code-using-gsm-modem-at-commands/

- Routerboard RB2011UiAS-2HnD-IN
- Huawei E160G (T-Mobile stick)

## Cheap 'M2M' SIM card sources:

- [KPN Prepaid](https://www.kpn.com/mobiel/prepaid-simkaart) (prepaid, simonly, needs periodic refresh)
- [EasyM2M](http://www.easym2m.eu/) (free trial)

# Userful commands

List USB devices:

    [user@router] > /system resource usb print
    # DEVICE VENDOR                                   NAME                                   SPEED
    0 1:1    Linux 3.3.5 ehci_hcd                     RB400 EHCI                             480 Mbps
    1 1:6    HUAWEI Technology                        HUAWEI Mobile                          480 Mbps

List serial ports:

    [user@router] > /port print
    Flags: I - inactive
     #   DEVICE NAME                                             CHANNELS USED-BY                                           BAUD-RATE
     0   1:10   usb1                                                    3                                                   9600

Verify stick works by sending AT command:

    /system serial-terminal usb2
    ATI
    Manufacturer: huawei
    Model: E160G
    Revision: 11.608.12.00.55
    IMEI: xxxxx
    +GCAP: +CGSM,+DS,+ES

Check pin:

    AT+CPIN?
    +CPIN: SIM PIN

Test pin:

    AT+CPIN="0000"
    OK

Disable pin:

    AT+CLCK="SC",0,"0000"
    OK
    AT+CPIN?
    +CPIN: READY
    OK

Send SMS:

    /tool sms send phone-number=0123456789 message=test port=usb1

# Internet

To obtain internet over 3G a PPP connection is used. First verify modem information:

    [user@router] > /interface ppp-client info 0
           modem-status: ready
             pin-status: no password required
          functionality: full
           manufacturer: huawei
                  model: E160G
               revision: 11.608.12.00.55
          serial-number: xxxx
       current-operator: KPN (cellid unknown)
      access-technology: 3G
         signal-strengh: -75 dBm
       frame-error-rate: n/a

Also if everything works correctly a disabled PPP interface should have been created:

    [user@router] > /interface ppp-client print
    Flags: X - disabled, R - running
     0 X  name="ppp-out1" max-mtu=1500 max-mru=1500 mrru=disabled port=usb2 data-channel=0 info-channel=0 apn="internet" pin=""
          user="" password="" profile=default phone="" dial-command="ATDT" modem-init="" null-modem=no dial-on-demand=yes
          add-default-route=yes default-route-distance=0 use-peer-dns=yes keepalive-timeout=30 allow=pap,chap,mschap1,mschap2

Taking it out of disabled state might disable SMS functionality depending on modem!

Next set connection details like APN, prevent it from routing all internet traffic and enable the interface.

    [user@router] > /interface ppp-client set 0 apn=portalmmm.nl add-default-route=no
    [user@router] > /interface ppp-client enable 0

Now watch connectivity status coming up and the internet works:

    [user@router] > /interface ppp-client monitor ppp-out1
              status: connected
              uptime: 57s
            encoding:
                 mtu: 1500
                 mru: 1500
       local-address: x.x.x.x
      remote-address: 0.0.0.0

    [user@router] > /ping 8.8.8.8 interface=ppp-out1
      SEQ HOST                                     SIZE TTL TIME  STATUS
        0 8.8.8.8                                    56  47 120ms
        1 8.8.8.8                                    56  47 125ms
        2 8.8.8.8                                    56  47 136ms



# POIDH

I broke the SIM connector when I tried to insert a microsim using a crude jig :(. So I got some aluminium foil to 'repair' the connectors.

!['solution'](/img/IMG_20170516_195326.jpg)
!['temporary'](/img/IMG_20170516_195350.jpg)
!['stick'](/img/IMG_20170516_195709.jpg)

