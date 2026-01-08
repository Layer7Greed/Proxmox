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

SAVE CHANGES

Erfolg ist nicht der Schlüssel zum Glück, sondern Glück ist der Schlüssel zum Erfolg. Wenn du gerne tust, was du tust, wirst du auch erfolgreich sein.
 Dieses Zitat von Albert Schweitzer betont, dass die Leidenschaft für das eigene Tun entscheidend für den Erfolg ist. Ubuntu Server ist somit eingerichtet:

<img width="574" height="441" alt="image" src="https://github.com/user-attachments/assets/761d96a3-e5cc-4124-8ad1-3dcd74ef6615" />

Morgen gibt es die Einrichtung des eigentlichen Servers für PZ. Stand 08.01.2026 

