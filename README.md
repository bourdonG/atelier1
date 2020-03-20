CAPTURE 1

1.
Capgchamb6.goffinet.net, on trouve l’information à plusieurs endroits, dans GNS3 mais également dans OpenVPN
On ouvre la topologie (lan-simple-cisco)
Démarrage du router Cisco en premier lieu, jusqu’à récupérer l’invite de commande dans la console.
Puis démarrage des terminaux (PC Unbuntu)

On se connecte sur PC2 suivant les logins root//testtest
On installe le programme « curl » sur la machine virtuelle -> apt install curl

La commande ‘’ curl http://capgchamb6.goffinet.net:8003 ‘’permet de faire une requête HTTP avec la méthode GET sur l’url en question.

Pour plus de précision :
HTTP:// : protocole http utilisé par défaut pour une url
capgchamb6.goffinet.net:8003 : le nom d’hôte et le port utilisé pour faire la requête

2.
On capture le trafic en faisant un clic droit sur la liaison qui connecte le PC2 au switch puis Start capture -> Wireshark démarre

Résultat de la commande :

root@pc2:~# curl http://capgchamb6.goffinet.net:8003
{
  name: "capgchamb6.goffinet.net"
  ipv4: "51.15.223.171"
}

Voir capture1.pcapng

3.
Dans Wireshark on notera,
Source Port : 8003

4.
Il s’agit du paquet 4 (couche application -> HTTP)

5.
Le paquet qui commence la session est le 1 (SYN) (on remarque la source qui est bien le PC2 -> 192.168.1.2
Le paquet qui termine la session est le 11 (FIN, ACK) (envoyé du serveur 51.15.223.171 vers PC2)


CAPTURE 2

On installe le programme DIG sur le PC2 -> apt install dnsutils

Résultat :

root@pc2:~# dig @ns1.google.com -t TXT o-o.myaddr.l.google.com +short -4
"51.15.223.171"

Explication de la commande ‘’dig @ns1.google.com -t TXT o-o.myaddr.l.google.com +short -4”

A l’aide de la commande dig -help

dig : permet de diagnostiquer si il y a des disfonctionnements dans la résolution de nom
@ns1.google.com : le serveur DNS en question
TXT : on interroge le champ divers
-t : le type
+short :  permet de montrer uniquement le résultat sous la forme souhaité (ici ipv4)
-4 : force à utiliser une requête ipv4

Le résultat est donc bien "51.15.223.171"

2.
Voir capture2.pcapng

3.
Il y a 2 transactions DNS générées
De la source (PC2 -> 192.168.1.2) qui demande au serveur de faire la requête ‘’dig’’ (ns1.google.com -> 216.239.32.10)

4.
Pour la première query la question est d’exécuter la commande :
dig -t TXT o-o.myaddr.l.google.com +short -4

Pour la deuxième transaction, c’est la réponse soit : "51.15.223.171"

5.
Pour la première question, elle est posée à ns1.google.com (216.239.32.10)
La deuxième, elle est posée à pc2 (192.168.1.2)

