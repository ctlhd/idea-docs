время жизни соеденения, по дефолту 24ч
/ip firewall connection tracking set tcp-established-timeout=1h

/ip dhcp-server set [find name="defconf"] lease-time=1d
срок аренды dhcp

Отключение не используемых helper'ы (ALG или service port)
/ip firewall service-port
set ftp disabled=yes
set tftp disabled=yes
set irc dissbled=yes
set h323 disabled=yes
set sip disabled=yes
set pptp disabled=yes
set dccp disabled=yes
set sctp disabled=yes

Запретить ICMP ответы с WAN портов в цепочке INPUT и отключить MAC Ping, в этом случае наш микротик не будет отвечать на пинг, то есть снижает вероятность сканирования, попыток подключкения и переборов намного меньше будет, но есть одно но....ксли нам понадобится как то пинговать этот роутер, не помешаетл нам самим это правило?)
/ip firewall filter add chain=input action=drop protocol-icmp icmp-options=8:0 in-interface-list=WAN src-address-list="!AllowIPRemoteManagment comment="Drop IN echo request"
/tool mac-server ping set enabled-no

Настройки беспроводной сети:

/caps-man channel {
           add band=2ghz-g/n control-channel-widht=20mhz extension-channel=disabled frequ,ncy=2412,2437,2462 name=2.4Channels reselect-interval=1d tx-power=20
           add band=5ghz-n/ac control-channel-width=20mhz extension-channel=Ce frequency=5180,5220,5260,5300,5680,5745,5785 name=5Channels reselect-interval=1d tx-power=20 skip-dfs-channels=yes
           }
           
           
Настройки безопасности:
/caps-man security add authentication-types=wpa2-psk encryption=aes-ccm group-encryption=aes-ccm disable-pmkid=yes name=OfficeNetPass passphrase="$PassOffice"


Настройки access-list'a когда у клиента плохой сигнал, и точка вырубает его что бы он мог соеденится к другой точке:
/caps-man access-list {
           add action-accept allow-signal-out-of-range=5s disabled=no interface=any mac-address=00:00:00:00:00:00 signal-range=-75..0 ssid-regexp=""
           add action=reject allow-signal-out-of-range=always disabed=no interface-any mac-address=00:00:00:00:00:00 signal-range=-120..120 ssid-regexp=""
           		}
           		
Настройка правила потока:
/caps-man datapath add client-to-client-forwarding=yes local-forwarding=yes name=OfficeNet


Конфигурации для применения на wifi точках:
/caps-man configuration {
       add channel=2.4Channels country=uzbekistan3 datapath=OfficeNet distance=indoors guard-interval=long max-sta-count=32 mode=ap multicast-helper=default name=OfficeNet2 rates=StandartDataRates rx-chains=0,1 securit=OfficeNetPass ssid="$DDISOffice-2.4Ghz" tx-chains=0.1
       add channel=5Channels country=uzbekistan3 datapath=OfficeNet distance=indoors guard-interval=long max-sta-count=32 mode=ap multicast-helper=default name=OfficeNet5 rates=StandartDataRates rx-chains=0.1 security=OfficeNetPass ssid="$SSIDOffice-5Ghz" tx-chains00.1
       
Настройка автоконфигурацию в сети:
/caps-man provisioning {
      add action=create-disabled hw-supported-modes=gn master-configuration= OfficeNet2 name-format=prefix-identity name-prefix=2Ghz
      add action=create-disabled hw-supported-modes=ac master-configuration= OfficeNet5 name-format=prefix-identity name-prefix=5Ghz
                       }
                       
Включение CAPsMAN:
/caps-man manager set enabled-yes