# Vodafone UK VOIP Residential FTTP Configuration notes
For using your own VOIP hardware (Grandstream WP810) with Vodafone UK Residential FTTP
The locations for the Grandstream settings vary depending on model. The ones on this page are specific to the HT8XX models.

## Known working phones
+ GXP1620 - Wired Phone
+ GXP1625 - Wired Phone
+ HT801 - ATA
+ HT802 - ATA
+ HT812 - ATA
+ WP810 - WiFi Phone
+ WP822 - WiFi Phone
+ Cisco ATA 191
+ Cisco ATA 192
+ Yealink W70B base station: Screenshots and instruction text file: https://drive.google.com/drive/folders/1KWq1dk6jJu_5v9hS35hCy7nRO9d_Lawd?usp=sharing

## DNS
Your device MUST USE VODAFONE DNS SERVERS TO RESOLVE THE RELEVANT DOMAINS.
+ 90.255.255.90
+ 90.255.255.91
+ 90.255.255.254
+ 90.255.255.255

## Obtain your SIP details from Vodafone
+ Primary SIP Server: resvoip.vodafone.co.uk
+ Outbound Proxy: xxx.xx.bbvoice.vodafone.co.uk
+ SIP UserID and Authenticate ID: voi00XXXXXX
+ Authenticate Password: As provided by vodafone

## Configure router for VOIP via NAT
+ [Optional] Create a VLAN with vodafone's DNS servers - their DNS servers are required to login
+ Fix your Grandstream devices IP address on your router (and switch to new VLAN)
+ Open ports 5065 (SIP), 10000-10010 (RTP)

For example for UniFi routers
+ Create a new VLAN: Settings > Networks - New Virtual network > Name: VOIP, Zone: Internal, IPv4 Address: 192.168.3.1, VLAN ID2, Uncheck Auto DNS server, add 90.255.255.91 and 90.255.255.90
+ Fix your Grandstream's IP and switch to the new VLAN: Client devices > find grandstream router > settings > Check Virtual Network Override > VOIP. Add a fixed IP address e.g., 192.168.3 .
+ Add 3 new polices (Setting > Policy Engine > Policy Table > Create New Policy) 
- VOIP SIP: TYPE: NAT Masquerade, TCP/UDP, SOURCE: IP, Specific, GRANDSTREAM IP (192.168...), Port Specific 5065, Destination: ANY, PORT Specific 5065
- VOIP RTP: TYPE: NAT Masquerade, TCP/UDP, SOURCE: IP, Specific, GRANDSTREAM IP (192.168...), Port Specific 10000-10010, Destination: ANY, PORT Specific 10000-10010
Note: you could also just have one VOIP policy and group the ports (5065,10000-10010,19302)

## Grandsream Configuration
Basic settings
+ under IPv4 Address add 1st Preferred DNS server and 2nd Preferred DNS server as 90.255.255.91 and 90.255.255.90. (dont use the statically configured part)

FXS PORTS
+ PORT 1: SIP User ID & Authenticate ID = username, Password = password, Name: VODAFONE

Advanced settings
+ (if no fixed WAN IP, exclude if fixed IP) STUN server is: stun.l.google.com:19302

Profile 1
+ Primary SIP server: resvoip.vodafone.co.uk
+ Prefer Primary SIP Server: yes
+ Outbound proxy: xxx.xx.bbvoice.vodafone.co.uk (as provided by vodafone)
+ Sip user ID: voixxxxxxxxxx (as provided by vodafone)
+ Authenticate ID: voixxxxxxxxxx (as provided by vodafone)
+ Authenticate password: ******** (as provided by vodafone)
+ DNS mode: SRV
+ NAT Traversal: STUN (if no static IP), No (if static IP)
+ Register expiration: 60 minutes
+ Register before expiration: 15 seconds
+ Enable SIP Options/Notify Keep-alive: Options
+ Local SIP port: 5065
+ Local RTP port: 10000
+ Remove OBP from route header: Yes
+ SIP User-Agent: Vox 3.0
+ Enable 100rel: Yes
+ Add Auth header on initial REGISTER: Yes
+ Preferred vocoder: PCMA > G.729 > PCMU > iLBC > OPUS > G.726-32 > G723 > G.722
+ SLIC Setting: UK
+ Caller ID Scheme: SIN 227 – BT
+ OnHook DC Feed Current: 20mA
Leave all other fields as per their default setting.

To block spam IP calls (SPIT) from numbers like 10001, 1001, 100, 11, disable direct IP calls:
+ Check SIP User ID for incoming INVITE: Yes (no direct IP calling if Yes)
+ Allow Incoming SIP Messages: Yes (no direct IP calling if Yes)

Remember to click "Update" and "Apply" buttons located at the bottom of every page to save and activate the changes. you may need to reboot via the FXS Ports page.

Once completed on the status page ensure FXS 1 is "Registered"

## Localisation
If you have purchased a Grandstream telephone adapter (ATA), it will come configured for the US telecom system as standard. As such, it will play back US style tones, sounds and some UK phone features may not work correctly unless you make some configuration changes via the devices web based GUI.

Basic settings
+ Time Zone: GMT (London, Great Britain)
+ Self-Defined Time Zone: GMT0BST,M3.5.0/1,M10.5.0

Advanced settings
+ System Ring Cadence: c=400/200-400/2000;
+ Dial Tone: f1=350@-19,f2=440@-22,c=0/0;  
+ Ringback Tone: f1=400@-20,f2=450@-20,c=400/200-400/2000;
+ Busy Tone: f1=400@-20,c=375/375;
+ Reorder Tone: f1=400@-20,c=400/350-225/525-0/0;  
+ Confirmation Tone: f1=1400@-10,c=0/0;  
+ Call Waiting Tone: f1=400@-20,c=100/2000;
+ Prompt Tone: f1=350@-19,f2=440@-22,c=0/0; 
+ Conference Party Hangup Tone: f1=400@-20,c=0/0;
+ Special Proceed Indication Tone: f1=350@-19, f2=440@-22, c=750/750-0/0;	  
+ Special Condition Tone: f1=350@-13, f2=450@-13, c=750/750;
+ NTP Server: uk.pool.ntp.org

Profile 1
+ MWI Tone: Special Proceed Indication Tone
+ Dial Plan: { 10[015] | 11[129] | 999 | 11[68]xxx | 1[45]7[1-2] | 08001111 | 0845464x | 0[1235789]xxxxxxxxx | 1410[1235789]xxxxxxxxx | 14700[1235789]xxxxxxxxx | 00xxx. | x+ | +x+ | *x+ | xxx+ }
+ SLIC Setting: UK
+ Caller ID Scheme: SIN 227 - BT
+ Hook Flash Timing: Minimum: 60 Maximum: 200
+ Ring Frequency: 25
+ Ring Tone 1: c=400/200-400/2000;
+ Ring Tone 2: c=400/200-400/2000;
+ Ring Tone 3: c=400/200-400/2000;
+ Ring Tone 4: c=400/200-400/2000;
+ Ring Tone 5: c=400/200-400/2000;
+ Ring Tone 6: c=400/200-400/2000;
+ Ring Tone 7: c=400/200-400/2000;
+ Ring Tone 8: c=400/200-400/2000;
+ Ring Tone 9: c=400/200-400/2000;
+ Ring Tone 10: c=400/200-400/2000;
+ Call Waiting Tone 1: f1=400@-20,c=100/2000;
+ Call Waiting Tone 2: f1=400@-20,c=100/2000;
+ Call Waiting Tone 3: f1=400@-20,c=100/2000;
+ Call Waiting Tone 4: f1=400@-20,c=100/2000;
+ Call Waiting Tone 5: f1=400@-20,c=100/2000;
+ Call Waiting Tone 6: f1=400@-20,c=100/2000;
+ Call Waiting Tone 7: f1=400@-20,c=100/2000;
+ Call Waiting Tone 8: f1=400@-20,c=100/2000;
+ Call Waiting Tone 9: f1=400@-20,c=100/2000;
+ Call Waiting Tone 10: f1=400@-20,c=100/2000;

#### Grandstream HT813 telephone adapter
Additional UK regional settings are required for this model.
FXO PORT page:
+ Caller ID Scheme: SIN 227 - BT
+ FSK Caller ID Seizure Bits: 96
+ FSK Caller ID Mark Bits: 55
+ PSTN Disconnect Tone: f1=400@-30,f2=400@-30,c=0/0;
+ Country-based: UK
+ Impedance-based: COMPLEX3 -- 370 ohms + (620 ohms || 310nF)

## Sources
Vodafone forums via the [internet archive](https://web.archive.org/web/20240801000000*/https://forum.vodafone.co.uk/t5/Landline/Landline-phone-with-own-router-on-FTTP/td-p/2709457).
