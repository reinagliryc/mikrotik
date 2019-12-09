# Remplacer une livebox par un routeur Mikrotik hAP ac² (RBD52G-5HacD2HnD-TC)


__Pourquoi remplacer la livebox ?__
Car il est impossible de passer la livebox en mode bridge. 

__Pas de tv ?__
La livebox play étant bruyante et énergivore (18w), une mibox (4w) sera utilisée à la place.

## Procédure pour configurer la connexion internet

__En bref__
- configurer le vlan 832 sur le port wan (connecté à l'ONT)
- configurer un client dhcp avec les options spécifiques au service orange
- ajouter une règle switch pour mettre un CoS de 6 pour les trames ethernet portant le DHCP
- ajouter une règle NAT

__Les commandes via ssh ou un terminal WinBox__

Sur la base d'une configuration classique

>/interface vlan
>add interface=ether1 name=vlan832-internet vlan-id=832

>/ip dhcp-client option
>add code=77 name=userclass value="'+FSVDSL_livebox.Internet.softathome.Livebox4'"
>add code=60 name=vendor-class-identifier value="'sagem'"
>add code=90 name=authsend value="0x00000000000000000000001a0900000558010341010d_code_connexion_fti" 

>/interface ethernet switch rule
>add dst-port=67 mac-protocol=ip new-vlan-priority=6 ports=switch1-cpu protocol=udp switch=switch1

>/ip firewall nat
>add action=masquerade chain=srcnat comment="defconf: masquerade" ipsec-policy=out,none out-interface=vlan832-internet

ps: il est également possible de le faire depuis l'interface WinBox ou Web.

__Upgrade à étudier__

- Utilisation d'un module SFP d'Orange pour supprimer l'ONT

Sources:
https://lafibre.info/remplacer-livebox/index-des-solutions-de-remplacement-de-la-livebox/
https://sebastien.warin.fr/2017/02/11/4284-fonctionnement-fibre-orange-remplacement-livebox-routeur-mikrotik-routeros/
https://lafibre.info/remplacer-livebox/cacking-nouveau-systeme-de-generation-de-loption-90-dhcp/
http://blog.jpauli.tech/replacing-the-isp-cpe-box-the-orange-case/
