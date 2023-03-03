WIFI调试
===========

1 基本调试技巧
-----------------

http://120.48.82.24:9102/net/wifi.html

2. 查找AP设备
-----------------

.. code-block:: shell

    $ iwlist wlan0 scan

    wlan: SCAN COMPLETED: scanned AP count=23
    mlan0     Scan completed :
          Cell 01 - Address: 42:79:81:E8:22:9A
                    ESSID:"53d4a9ae_F"
                    Mode:Master
                    Frequency=2.412 GHz (Channel 1)
                    Quality:0/5  Signal level:-96 dBm  Noise level:-96 dBm
                    Encryption key:on
                    Bit Rates:1 Mb/s; 2 Mb/s; 5.5 Mb/s; 11 Mb/s; 6 Mb/s
                              9 Mb/s; 12 Mb/s; 18 Mb/s; 24 Mb/s; 36 Mb/s
                              48 Mb/s; 54 Mb/s
                    Extra:Beacon interval=100
                    IE: IEEE 802.11i/WPA2 Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
                    IE: Unknown: DD180050F2020101810003A4000027A4000042435E0062322F00
                    IE: Unknown: DD0F8CFDF0010102010002010109020300
                    Extra:band=bg
          Cell 02 - Address: 06:74:9C:30:FE:DE
                    ESSID:"CU-CYJG2" [2]
                    Mode:Master
                    Frequency=2.412 GHz (Channel 1)
                    Quality:1/5  Signal level:-89 dBm  Noise level:-96 dBm
                    Encryption key:on
                    Bit Rates:1 Mb/s; 2 Mb/s; 5.5 Mb/s; 6 Mb/s; 9 Mb/s
                              11 Mb/s; 12 Mb/s; 18 Mb/s; 24 Mb/s; 36 Mb/s
                              48 Mb/s; 54 Mb/s
                    Extra:Beacon interval=100
                    IE: IEEE 802.11i/WPA2 Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
                    IE: WPA Version 1
                        Group Cipher : CCMP
                        Pairwise Ciphers (1) : CCMP
                        Authentication Suites (1) : PSK
                    IE: Unknown: DD180050F2020101860003A4000027A4000042435E0062322F00
                    IE: Unknown: DD0900037F01010000FF7
                    IE: Unknown: DD07001AA96B8B4567
                    Extra:band=bg
          Cell 03 - Address: 44:6A:2E:1E:0D:A0
                    ESSID:"Airdoc-guest" [3]

.. tip:: 
    
    - signal level -50db就还可以, 不接天线 ``< -90``, 接入天线后会 ``> -50``
