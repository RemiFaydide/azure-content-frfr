##Paramètres TCP pour les machines virtuelles Azure

Les machines virtuelles Azure communiquent avec l’Internet public à l’aide de la traduction d’adresses réseau ou [NAT][nat]. Les appareils NAT attribuent une adresse IP publique et un port à une machine virtuelle Azure, permettant à celle-ci d’établir un socket pour communiquer avec d’autres appareils. Si le flux de paquets transitant via ce socket s’interrompt après une durée donnée, l’appareil NAT supprime le mappage et libère le socket pour d’autres machines virtuelles.

Ce comportement NAT courant peut entraîner des problèmes de communication sur les applications TCP qui prévoient le maintien d’un socket au-delà d’un délai. Il y a deux délais à prendre en compte pour les sessions avec une *connexion établie* :

- **entrant** via [Azure Load Balancer][azure-lb-timeout]. Ce délai est de 4 minutes par défaut. Sa valeur maximale est de 30 minutes.
- **sortant** à l’aide de [SNAT][snat] \(Source NAT). Ce délai est de 4 minutes et n’est pas modifiable.

Pour garantir le maintien des connexions au-delà de ce délai, vous devez vous assurer que votre application garde la session active ou configurer le système d’exploitation sous-jacent en ce sens. Les paramètres à utiliser sont différents pour les systèmes Linux et Windows, comme indiqué ci-dessous.

Sous [Linux][linux], vous devez modifier les variables de noyau ci-dessous : net.ipv4.tcp\_keepalive\_time = 120 net.ipv4.tcp\_keepalive\_intvl = 30 net.ipv4.tcp\_keepalive\_probes = 8
 
Sous [Windows][windows], vous devez modifier les valeurs ci-dessous dans le Registre : KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


Les paramètres ci-dessus garantissent l’envoi d’un paquet de maintien d’activité après 2 minutes (120 secondes) d’inactivité, puis toutes les 30 secondes. Si l’envoi de 8 de ces paquets échoue, la session est fermée.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md