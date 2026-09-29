# Netzwerkverkehr mit Wireshark untersuchen

## Ziel

Selbst erzeugten DNS-, ICMP- und TCP-Verkehr in einer Kali-Linux-VM aufzeichnen und verstehen.

## Umgebung

- Kali Linux in VirtualBox, Netzwerkmodus NAT
- Netzwerkinterface `eth0`: während des Labs `10.0.2.15`
- Loopback-Interface `lo` für den lokalen TCP-Test
- Werkzeuge: Wireshark, `dig`, `ping`, `python3`, `curl`

## Durchführung

### DNS

Aufzeichnung auf `eth0`, Anzeigefilter: `dns`

```bash
dig example.com A
```

### ICMP

Aufzeichnung auf `eth0`, Anzeigefilter: `icmp`

```bash
ping -c 4 10.0.2.2
```

### TCP und HTTP

Lokalen Webserver starten und die Aufzeichnung auf `lo` beginnen. Anzeigefilter: `tcp.port == 8000`

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

In einem zweiten Terminal:

```bash
curl -v --noproxy '*' --max-time 10 -I http://127.0.0.1:8000/
```

Anschließend den Server mit Strg+C beenden.

## Eigene Beobachtungen

1. **DNS:** Die DNS-Anfrage fragt nach der IPv4-Adresse von `example.com`, und die Antwort liefert zwei A-Adressen zurück.
2. **ICMP:** Die vier Ping-Anfragen an `10.0.2.2` wurden jeweils beantwortet; es gab keinen Paketverlust.
3. **TCP:** Vor der HTTP-Anfrage wird die TCP-Verbindung mit SYN, SYN/ACK und ACK aufgebaut. Nach der HTTP-Antwort wird die Verbindung wieder beendet.

Der lokale Webserver antwortete auf die HTTP-HEAD-Anfrage mit `HTTP/1.0 200 OK`.

## Was ich gelernt habe

Ich habe besser verstanden, wie DNS, ICMP und TCP in Wireshark tatsächlich als einzelne Pakete sichtbar sind.

## Screenshots

- [DNS-Anfrage und Antwort](screenshots/dns.png)
- [ICMP Echo Requests und Replies](screenshots/icmp.png)
- [TCP-Verbindungsaufbau, HTTP und Verbindungsabbau](screenshots/tcp.png)


