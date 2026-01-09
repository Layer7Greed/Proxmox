This Proxmox-based homelab is designed to host lightweight containers and virtual machines, providing a flexible environment for testing, development, and gaming.

Schritt 1 Updates Script hinzufügen, dabei wird der Proxmox VE Helper-Script als hilfe genutzt *PVE Post Install*:
<img width="1444" height="823" alt="image" src="https://github.com/user-attachments/assets/16bd0b03-223d-47aa-b49a-7ead4828708c" />

Wie man sehen kann sind die Enterprise Optionen (Repos) jetzt durch die Konfig beim Ausführen des Scripts ausgeschalten. 
<img width="1918" height="711" alt="image" src="https://github.com/user-attachments/assets/06ce8266-3313-434e-abb6-98fffcb12a2b" />

Nun kann man auf Updates drücken --> Refresh und dann anschließend auf uprgrade damit man immer auf den neuesten Stand ist, sollte man das in regelmäßigen Abständen durchführen.
<img width="1605" height="675" alt="image" src="https://github.com/user-attachments/assets/e0c41836-6a5a-4cd4-93e8-74a5660956fb" />

In Proxmox gibt es einige "features" unteranderem in der Zukunft Projekte die mit einander verbunden werden können, wie zum Beispiel das Anhängen eines NAS-Servers für den Speicher, damit dieser die Backups, der VM übernimmt, als auch für ISO Files. 
Leider habe ich die HW für diese nice to haves noch nicht, dennoch wird das höchstwahrscheinlich in der Zukunft hinzugefügt. Ich füge es local am Proxmox Server. 
<img width="1349" height="376" alt="image" src="https://github.com/user-attachments/assets/9777b6f1-5bac-4745-a33b-c923e031f4e1" />

Wie auch in dem Bild unten angeängt sieht man den Ubuntu Server mit ungefähr 3GB, ich belasse es auf den, da ich durch Recherchen festgestellt habe, dass Ubuntu Server sowieso am stabilsten sind:
<img width="1912" height="176" alt="image" src="https://github.com/user-attachments/assets/c6eda980-280c-4234-93cb-337a69021b93" />

Nun zum Erstellen der VM, der erste soll ein Game-Server sein für PZ (Project Zomboid) in schnell durchgang:
Hier wird das Image selected alles andere auf standard gelassen
<img width="716" height="534" alt="image" src="https://github.com/user-attachments/assets/b3d57fb3-19d7-49d4-8b9d-61f8a70eea76" />

in System Fenster wurde ebenfalls alles auf Standard gelassen

dann im "DISKS" sagen wir auf den local-lvm abspeichern dieser wurde autoamtisch generiert als der Proxmox Server eingerichtet wurde, dieser ist für unsere VMs der Speicherort (SSD). und weiteres wurde 40GB Speicherplatz eingestellt.
<img width="715" height="532" alt="image" src="https://github.com/user-attachments/assets/6ce191bb-e8c8-4de6-abca-961bac5f281e" />

in der CPU Sektion wurden 2 Kerne zugewiesen für diese VM, da alles drüber Overkill wäre, PZ ist ein Single Core lastig ist. Weiteres wurde das Type auf HOST eingestellt, damit auch wirklich die Kerne des CPUs verwendet werden, asonsten hätte man eine art
kompatible generische Variante, wichtig ist beim hosting, dass es immer der echte Kern ist des CPUs damit auch ticks und alles stimmen. 
<img width="718" height="534" alt="image" src="https://github.com/user-attachments/assets/6004f177-c1be-4a06-83de-d1ae01c80775" />

Weiteres ist die Netzwerk-Sektion dran, hierbei wurde alles beibehalten auf Standard bis auf Firewall, dieses wurde ausgeschalten aufgrund des Overheads, in Ubuntu wird folgendes konfiguriert für UFW:
sudo ufw allow ssh
sudo ufw allow 16261/tcp
sudo ufw allow 16262/udp
sudo ufw enable
<img width="712" height="533" alt="image" src="https://github.com/user-attachments/assets/89cf1fe8-17cb-4c40-9c04-b37f3c81e168" />

192.168.0.135 für mein Game-Server
Weiteres wurde Ubuntu aufgesetzt als VM und standard Einstellungen übernommen.

Erfolg ist nicht der Schlüssel zum Glück, sondern Glück ist der Schlüssel zum Erfolg. Wenn du gerne tust, was du tust, wirst du auch erfolgreich sein.
 Dieses Zitat von Albert Schweitzer betont, dass die Leidenschaft für das eigene Tun entscheidend für den Erfolg ist. Ubuntu Server ist somit eingerichtet:

<img width="574" height="441" alt="image" src="https://github.com/user-attachments/assets/761d96a3-e5cc-4124-8ad1-3dcd74ef6615" />

Zu den Grundlagen der Proxmox Umgebung gilt auch noch das Erstellen von Container (kurze Beschreibung von Container und wie es funktioniert).
Zuerst muss der Speicherort ausgewählt werden dann auf das CT Templates da hat man auch Templates kann man auf die Option drücken und dann images wie z.b bei uns Ubuntu und Debian für Ubuntu/Debian Container. 
Prozess bei der Erstellung ist im Prinzip, dasselbe wie auch bei den virtuellen Maschinen, nur dass Container Hardware teilen. Somit wären die Grundlagen und das Setup von Proxmox beendet. 

Jetzt folgt noch ein Teil und zwar das remote Verbinden zum Proxmox Server, allerdings müssen wir das sicher herstellen. 

LXC Lightweight virtualization Technolgoie

Docker Container "OCI Container" 
LXCs laufen einen vollen init system nativly unterschied zu OCI wegen S6 Overlay lmage.
Weiteres wird jetzt Tailscale in ein unprivilged LXC installiert und eingestellt
This tutorial covers everything from basic LXC setup to the fascinating technical details of how TUN devices work under the hood.

Including, but not limited to:

The difference between LXC and OCI (Docker) containers
How to configure an unprivileged LXC for Tailscale
The magic behind /dev/net/tun and kernel networking
Why TUN devices need special privileges
How VPN traffic flows through your system
When to use userspace vs kernel networking modes

wir wählen dafür den Debian 12 aus:
<img width="897" height="597" alt="image" src="https://github.com/user-attachments/assets/71baf367-e933-4933-9713-86eb9866c0dd" />

Folgende Einstellungen für General:
<img width="714" height="531" alt="image" src="https://github.com/user-attachments/assets/34cec631-2d6e-4216-9aba-4cccff6d8dfd" />

Template, dass 12.standard tempalte, welches wir zuvor installierten. Speicherort Local.

Speicherplatz belassen wir auf standard obwohl Tailscale 8 niemals nutzen wird können wir es dabei lassen:
<img width="717" height="533" alt="image" src="https://github.com/user-attachments/assets/39f8d80f-bbe7-4793-ae79-7b1b84566a7f" />

Kerne haben wir 1 zugewiesen.

Ram 512 MiB
<img width="714" height="531" alt="image" src="https://github.com/user-attachments/assets/e328b882-3a6c-49f4-aaf7-4978b0a91f70" />

Netzwerk alles auf standard, außer IP-Adresse, diese sollt unser Router ihm per DHCP dynamisch zuweisen

<img width="712" height="531" alt="image" src="https://github.com/user-attachments/assets/123f63a7-eccb-4535-ab84-10942d3ee08f" />

jetzt wird auf dem Node pve in Shell folgender Befehl ausgeführt:
vi /etc/pve/lxc/100.conf

shift g um ganz unten zum Code zu gelangen und o zum einfügen.

von dieser Website: https://tailscale.com/kb/1130/lxc-unprivileged
wurden diese befehle kopiert:
lxc.cgroup.devices.allow: c 10:200 rwm
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
Erkläre kurz was die machen

dann mittels ESC und :wq (write / quit)

jetzt hat der LXC container das Recht um ein DEVNET Tun Device zu erstellen

von Command aus starten:
root@pve:~# pct start 1000
<img width="302" height="41" alt="image" src="https://github.com/user-attachments/assets/bb402b26-92ef-4d67-99ed-6cc66fb65b3a" />
er löuft.

jetzt in LXC Container in der Console folgendes:

apt update
apt install curl
curl -fsSL https://tailscale.com/install.sh | sh
root@lxc-tailscale-01:~# tailscale up --ssh

durch den letzten Befehl wurde folgendes generiert:
https://login.tailscale.com/a/1320f917016fd2?refreshed=true

Ich habe zu dem Tailscale network mein Handy hinzufegüt, jetzt können mein Container und Handy kommunzieren und mein Handy kann sich durch VPN in das Netzeinhängen um mit den Geräten zu kommunizieren.
-- DEVNET TUN -- 
Brücke zwischen kernal und User. Speciales Door way Network Interace denkt so
Kernel übersetzt zwischen Hardware und Software Requests übersetzen
Tailscale wenn er ein VPN device erstellt muss er Dev net tun öffenn und specielles konfigureiren korrigiere diese Passage habe es von dem Youtube Video:https://www.youtube.com/watch?v=JC63OGSzTQI

Normal:
Browser geht zu google.conm
DNS lookup gibt mir die IP
Kernal does a routing trable lookup creats a packet with destinitaion
sends traffic via matching interface eth0

mit Tunnel
Browser will Tailnet resource marschine rquest
Reemote destination is a CGnat space of 100.64.x.x
Creates a packet with destation only reachable via Tailscale 100.64.x.x
Kernel does a routing table lookupo
Sends traffic via matching interface tun0 defice
Tailscale is listing on tunß
Recieves raw packet.
Encrypts and trransmits packet over tailscale tunnel.
andere Tailscale decrypted und injected es in den Netzwerk. 
Interessant, Browser weiß nichts von dem VPN
Browser schickt normalen packet zum normalen Netzwerk. Tailnat schaltet ein
mit LXC muss man einen Node erstellen.
Privileged "admin" ist sicherheits risio, wenn du kein Kernel networking device erstellen möchtest. 

SSH Configuration 2:
root@lxc-tailscale-01:~# nano /etc/ssh/sshd_config
unter:
<img width="1457" height="614" alt="image" src="https://github.com/user-attachments/assets/6b7198af-c55b-41fe-bfae-8a4ae4eb7f9e" />
muss man das # entfernen und hinter PermitRootLogin yes schreiben, dammn mittels crtl o / enter to write and x to close 

IP-Adresse raus nehmen, damit es statisch bleibt und nicht entnommen wird.
<img width="1080" height="390" alt="image" src="https://github.com/user-attachments/assets/1a7ef0ec-996d-41b1-9a45-890ffbbca136" />

<img width="607" height="266" alt="image" src="https://github.com/user-attachments/assets/7d8e642a-1346-4d51-b369-52134e2850e6" />

Ping funktioneirt:

<img width="591" height="173" alt="image" src="https://github.com/user-attachments/assets/9732ebed-02b9-44ea-85dc-6daa7cb1c9a1" />

Port forwarding aktivieren:
root@lxc-tailscale-01:~# nano /etc/sysctl.conf 
auskommentieren
<img width="1437" height="615" alt="image" src="https://github.com/user-attachments/assets/32c3350b-7cd9-4592-a510-f68a1d53f5c0" />

root@lxc-tailscale-01:~# tailscale up --advertise-routes=192.168.0.0/24 --advertise-exit-node  wenn noch nicht up ist

root@lxc-tailscale-01:~# tailscale set --advertise-routes=192.168.0.0/24 --advertise-exit-node ansonsten sieht der Befehl so aus. 

mit diesem Befehl können wir via Tailscale in dem gesamten Netzwerk uns verbindne.
nochmals in Tailscale interface einstelle also edit route settings:
Edit route settings of lxc-tailscale-01
Subnet routes
Connect to devices you can’t install Tailscale on by advertising IP ranges as subnet routes. 
Exit node
Allow your network to route internet traffic through this machine. Learn more
<img width="506" height="533" alt="image" src="https://github.com/user-attachments/assets/71c31caf-14da-4c70-a780-ac0278a30cd0" />

Durch diese Regeln kann ich jetzt obwohl ich nicht zuhause bin auf meinem Proxmox server zugreifne über VPN-Verbindug. Getestet auf mein Handy siehe Screenshot.

Durchaus war diese Übung erfolgreich und umfangreich nicht nur Basics sondern auch durch diese etwas fortgeschritterne Settings. Weitreres würde ich Services installieren, um mein Netwerk sicherer zu machen. 

