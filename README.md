Configuring a Mikrotik RouterOS router to use a USB stick 3G modem as a out-of-band monitoring and management interface.

# Rationale
- Testing out the USB port on my Mikrotik router, a feature I left untouched to long.
- To remind me even when I'm not home that my ISP sucks.
- Experiment potential practical usecases for this kind of OOB management.

# Features:

- ...

# Todos:

- SMS monitoring alerts
- PPP OOB management (VPN/IPsec/IPv6?/call-home)

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

# POIDH

I broke the SIM connector when I tried to insert a microsim using a crude jig :(. So I got some aluminium foil to 'repair' the connectors.

!['solution'](/img/IMG_20170516_195326.jpg)
!['temporary'](/img/IMG_20170516_195350.jpg)
!['stick'](/img/IMG_20170516_195709.jpg)

