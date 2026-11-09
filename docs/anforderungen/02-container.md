# 02 · Container und Kubernetes

Funktionsumfang entsprechend Portainer Community und Business Edition.

---

## 1. Umgebungen (CON-ENV)

Eine „Umgebung“ ist eine verwaltete Container-Laufzeit: ein Docker-Host, ein
Swarm-Cluster, ein Kubernetes-Cluster oder ein Podman-Host.

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-ENV-001 | Lokale Docker-Umgebung über den Unix-Socket des Hosts automatisch erkennen und einbinden | M | P |
| CON-ENV-002 | Entfernte Docker-Umgebung über die Docker-API (TCP mit TLS, gegenseitige Zertifikatsprüfung) | M | P |
| CON-ENV-003 | Entfernte Umgebung über einen Agenten (gerichtete Verbindung vom Panel zum Agenten) | M | P |
| CON-ENV-004 | Edge-Agent mit ausgehender Verbindung (Tunnel), Modi „Standard“ (dauerhafter Tunnel bei Bedarf) und „asynchron“ (nur periodische Abfrage, für schwache Verbindungen) | M | P |
| CON-ENV-005 | Docker-Swarm-Cluster als Umgebung | M | P |
| CON-ENV-006 | Kubernetes-Cluster als Umgebung: per Agent im Cluster, per kubeconfig-Import oder über einen Edge-Agenten | M | P |
| CON-ENV-007 | Podman-Umgebung (rootful und rootless) über die Podman-API | M | P |
| CON-ENV-008 | Anlegen verwalteter Kubernetes-Cluster bei Cloud-Anbietern (z. B. AKS, EKS, GKE, DigitalOcean, Linode, Civo) | K | P |
| CON-ENV-009 | Erstellung eines Kubernetes-Clusters auf eigenen Servern (z. B. k3s/MicroK8s) per Assistent | K | P |
| CON-ENV-010 | Umgebungsgruppen und Tags. Rechte und Edge-Zuordnung über Gruppen und Tags. | M | P |
| CON-ENV-011 | Status jeder Umgebung (erreichbar, Version, Ressourcen) per periodischem Schnappschuss, Intervall konfigurierbar | M | P |
| CON-ENV-012 | Assistent mit kopierfertigem Installationsbefehl für Agent und Edge-Agent (Docker, Swarm, Kubernetes, Podman) | M | P |
| CON-ENV-013 | Automatische Registrierung von Edge-Agenten mit Wartebereich zur Freigabe durch einen Administrator | S | P |
| CON-ENV-014 | Aktualisierung von Agenten aus dem Panel heraus, einzeln und gruppenweise | S | P |
| CON-ENV-015 | Umgebungsbezogene Sicherheitseinstellungen für Nicht-Administratoren: Bind-Mounts, privilegierte Container, Host-PID/-Netzwerk, Geräte-Mapping, sysctl, Capabilities jeweils verbietbar; nur eigene Stacks sichtbar | M | P |
| CON-ENV-016 | GPU-Verwaltung: vorhandene GPUs einer Umgebung anzeigen und für Container freigeben | S | P |

## 2. Container (CON-CTR)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-CTR-001 | Liste aller Container mit Name, Status, Health, Image, Erstellzeit, IP, veröffentlichten Ports (klickbar), Stack, Eigentümer | M | P |
| CON-CTR-002 | Aktionen einzeln und als Massenaktion: Starten, Stoppen, Beenden (kill), Neustarten, Pausieren, Fortsetzen, Entfernen (optional mit Volumes) | M | P |
| CON-CTR-003 | Container erstellen per Formular mit allen Optionen von `docker run`: Name, Image (aus Registry), Pull erzwingen, Ports (automatisch/manuell, TCP/UDP), Befehl, Entrypoint, Arbeitsverzeichnis, Benutzer, Konsole (interaktiv/TTY) | M | P |
| CON-CTR-004 | Volumes beim Erstellen: benannte Volumes, Bind-Mounts, tmpfs, nur lesbar | M | P |
| CON-CTR-005 | Netzwerkeinstellungen: Netzwerk, Hostname, Domainname, MAC-Adresse, IPv4/IPv6-Adresse, DNS-Server, zusätzliche `/etc/hosts`-Einträge | M | P |
| CON-CTR-006 | Umgebungsvariablen einzeln und als Import aus einer `.env`-Datei | M | P |
| CON-CTR-007 | Labels setzen und anzeigen | M | P |
| CON-CTR-008 | Neustart-Richtlinie: never, always, on-failure (mit Anzahl), unless-stopped | M | P |
| CON-CTR-009 | Laufzeit und Ressourcen: privilegierter Modus, Init-Prozess, Laufzeit (z. B. nvidia), Geräte, GPUs, sysctls, Shared Memory, CPU-Limit und -Reservierung, RAM-Limit und -Reservierung, Capabilities einzeln hinzufügen/entfernen | M | P |
| CON-CTR-010 | Logging-Treiber und Optionen je Container | S | P |
| CON-CTR-011 | Healthcheck konfigurieren und Health-Status anzeigen | S | P |
| CON-CTR-012 | Container bearbeiten/duplizieren: Konfiguration übernehmen und als neuen Container anlegen oder den bestehenden ersetzen | M | P |
| CON-CTR-013 | Container neu erstellen mit optionalem Pull des neuesten Images | M | P |
| CON-CTR-014 | Detailansicht: Status, Start-/Erstellzeit, Image, Ports, Umgebung, Labels, Volumes, Netzwerke mit IP, Neustart-Richtlinie, vollständige Inspektion als JSON | M | P |
| CON-CTR-015 | Logs: live verfolgen, Zeitstempel, Zeilenanzahl, Zeitraum, Suche/Filter, Zeilenumbruch, Download | M | P |
| CON-CTR-016 | Statistiken in Echtzeit: CPU, RAM, Netzwerk, Block-I/O, laufende Prozesse im Container | M | P |
| CON-CTR-017 | Konsole: Befehl im Container ausführen (`exec`) mit wählbarer Shell und wählbarem Benutzer | M | P |
| CON-CTR-018 | Am Hauptprozess anhängen (`attach`) | M | P |
| CON-CTR-019 | Container umbenennen | M | P |
| CON-CTR-020 | Container als Image speichern (commit) | S | P |
| CON-CTR-021 | Container einem Netzwerk hinzufügen oder daraus entfernen | M | P |
| CON-CTR-022 | Webhook je Container zum Neuerstellen mit neuem Image (für CI/CD) | S | P |
| CON-CTR-023 | Image-Aktualitätsanzeige: Hinweis, wenn in der Registry ein neueres Image mit demselben Tag existiert | S | P |
| CON-CTR-024 | Eigentum und Zugriffssteuerung je Container (siehe PLT-RBAC-009) | M | P |
| CON-CTR-025 | Dateien im Container durchsuchen, hoch- und herunterladen | S | N |
| CON-CTR-026 | Automatische Container-Aktualisierung nach Zeitplan (Pull, Neuerstellung, Rollback bei fehlgeschlagenem Healthcheck) | S | N |

## 3. Images (CON-IMG)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-IMG-001 | Liste der Images mit Tags, Größe, Erstellzeit, Status „verwendet/ungenutzt“ | M | P |
| CON-IMG-002 | Image ziehen (pull) aus konfigurierten Registries, einfacher und erweiterter Modus | M | P |
| CON-IMG-003 | Image bauen: Dockerfile im Web-Editor, Upload eines Build-Kontexts (tar) oder URL eines Git-Repositorys/Tarballs. Build-Ausgabe live, Build-Argumente. | M | P |
| CON-IMG-004 | Image taggen, Tags entfernen | M | P |
| CON-IMG-005 | Image in eine Registry übertragen (push) | M | P |
| CON-IMG-006 | Images exportieren (`docker save`) und importieren (`docker load`) | M | P |
| CON-IMG-007 | Image-Details: Schichten (Historie mit Befehlen), Umgebung, Labels, Ports, Entrypoint, Größe | M | P |
| CON-IMG-008 | Ungenutzte und hängende Images entfernen (prune), auch über den Zeitplan | M | P |
| CON-IMG-009 | Schwachstellen-Scan von Images (z. B. mit Trivy/Grype) mit Bewertung nach Schweregrad | S | N |
| CON-IMG-010 | Image-Signaturprüfung (Cosign/Notation) vor der Bereitstellung, als Richtlinie erzwingbar | K | N |

## 4. Netzwerke, Volumes, Configs, Secrets (CON-RES)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-RES-001 | Netzwerke auflisten, anlegen, entfernen. Treiber bridge, overlay, macvlan, ipvlan, host, none. | M | P |
| CON-RES-002 | Netzwerkoptionen: Subnetz, Gateway, IP-Bereich, IPv6, intern, anfügbar (attachable), Treiberoptionen, Labels | M | P |
| CON-RES-003 | macvlan in zwei Schritten (Konfiguration je Knoten, Netzwerk im Swarm) | S | P |
| CON-RES-004 | Verbundene Container eines Netzwerks anzeigen und trennen | M | P |
| CON-RES-005 | Volumes auflisten, anlegen, entfernen. Treiber local, NFS, CIFS/SMB und Plugin-Treiber mit Optionen. | M | P |
| CON-RES-006 | Anzeige, welche Container ein Volume nutzen, und Kennzeichnung ungenutzter Volumes | M | P |
| CON-RES-007 | Volume-Browser: Inhalte durchsuchen, Dateien hoch- und herunterladen, umbenennen, löschen | S | P |
| CON-RES-008 | Volume sichern und wiederherstellen (als Archiv, lokal oder S3) | S | N |
| CON-RES-009 | Swarm-Configs anlegen (Editor), anzeigen, klonen, entfernen | M | P |
| CON-RES-010 | Swarm-Secrets anlegen (Inhalt nur beim Anlegen sichtbar), entfernen | M | P |
| CON-RES-011 | System-Bereinigung: gestoppte Container, ungenutzte Netzwerke, Images, Build-Cache, Volumes, einzeln wählbar, mit Anzeige des Speichergewinns | M | P |

## 5. Stacks (CON-STK)

Ein „Stack“ ist eine Anwendung aus mehreren Containern, definiert als
Compose-Datei (Docker/Podman/Swarm) oder als Manifest (Kubernetes).

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-STK-001 | Stack anlegen über Web-Editor (YAML mit Syntaxprüfung), Datei-Upload, Git-Repository oder Vorlage | M | P |
| CON-STK-002 | Compose-Spezifikation vollständig unterstützt, inkl. `profiles`, `depends_on` mit Bedingungen, `extends`, `include` | M | P |
| CON-STK-003 | Umgebungsvariablen für den Stack im Formular und per `.env`-Upload | M | P |
| CON-STK-004 | Git-Bereitstellung: Repository-URL, Branch/Tag, Pfad zur Compose-Datei, zusätzliche Compose-Dateien, Zugang per Token/Passwort/SSH-Schlüssel, Git-Submodule | M | P |
| CON-STK-005 | GitOps: automatische Aktualisierung bei Änderungen im Repository per Abfrageintervall oder Webhook, mit Erzwingen-Option (lokale Abweichungen überschreiben) | M | P |
| CON-STK-006 | Relative Pfade: Bind-Mounts relativ zum Repository-Verzeichnis auf dem Host | S | P |
| CON-STK-007 | Stack bearbeiten und neu bereitstellen, optional mit Pull neuer Images und Entfernen verwaister Dienste | M | P |
| CON-STK-008 | Stack starten, stoppen, löschen | M | P |
| CON-STK-009 | Stack-Detailansicht: Container/Dienste, Logs aller Container zusammengeführt, Ressourcenverbrauch | M | P |
| CON-STK-010 | Stack in eine andere Umgebung migrieren oder duplizieren | S | P |
| CON-STK-011 | Erkennen und Übernehmen außerhalb des Panels erstellter Stacks („externe Stacks“, eingeschränkte Bearbeitung) | M | P |
| CON-STK-012 | Versionsverlauf der Stack-Definition mit Rückkehr zu einer früheren Version | S | N |
| CON-STK-013 | Stack als Vorlage speichern | M | P |

## 6. Vorlagen (CON-TPL)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-TPL-001 | Katalog von Anwendungsvorlagen (Container und Stacks) mit Logo, Beschreibung, Kategorie, Suchfunktion | M | P |
| CON-TPL-002 | Vorlagen-Katalog aus externer URL (JSON-Format, kompatibel zum Portainer-Template-Format v2/v3) | M | P |
| CON-TPL-003 | Eigene Vorlagen anlegen, mit Variablen (Formularfelder, Auswahllisten), Zugriffssteuerung | M | P |
| CON-TPL-004 | Bereitstellung aus Vorlage mit Anpassung von Ports, Volumes, Variablen vor dem Start | M | P |

## 7. Registries (CON-REG)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-REG-001 | Registries anlegen: Docker Hub (authentifiziert), AWS ECR, Azure ACR, Google Artifact Registry, GitHub/GitLab Container Registry, Quay, Harbor, ProGet, beliebige eigene Registry | M | P |
| CON-REG-002 | Zugangsdaten sicher gespeichert (verschlüsselt) und je Umgebung freigebbar | M | P |
| CON-REG-003 | Registry-Browser: Repositories und Tags anzeigen, Tags löschen, umbenennen | S | P |
| CON-REG-004 | Anzeige des verbleibenden Docker-Hub-Pull-Limits | S | P |
| CON-REG-005 | Zugriff auf Registries je Team/Benutzer beschränkbar | M | P |
| CON-REG-006 | Eigene lokale Registry per Assistent als Container bereitstellen | K | N |

## 8. Docker Swarm (CON-SWM)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-SWM-001 | Cluster-Übersicht: Knoten, Rollen (Manager/Worker), Status, Verfügbarkeit, Engine-Version, Ressourcen | M | P |
| CON-SWM-002 | Grafische Cluster-Visualisierung: Verteilung der Tasks auf Knoten | M | P |
| CON-SWM-003 | Knoten: Verfügbarkeit ändern (active/pause/drain), Rolle ändern, Labels verwalten, entfernen | M | P |
| CON-SWM-004 | Swarm initialisieren, Join-Token anzeigen und rotieren | S | N |
| CON-SWM-005 | Dienste anlegen: Image, Replikation (replicated/global), Replikate, Ports (Ingress/Host-Modus), Volumes, Netzwerke, Umgebung, Labels, Configs, Secrets, Ressourcen, Platzierungsregeln und -präferenzen, Neustart-, Update- und Rollback-Richtlinie, Logging | M | P |
| CON-SWM-006 | Dienste skalieren, aktualisieren, Rollback, erzwungenes Update, entfernen | M | P |
| CON-SWM-007 | Tasks eines Dienstes mit Status, Knoten, Fehlermeldungen. Logs je Dienst und je Task. | M | P |
| CON-SWM-008 | Webhook je Dienst zur Aktualisierung | S | P |

## 9. Kubernetes (CON-K8S)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-K8S-001 | Cluster-Dashboard: Knoten, Namespaces, Anwendungen, Services, Ingresses, Volumes, Ressourcenreservierung und -nutzung | M | P |
| CON-K8S-002 | Knoten: Details, Labels, Taints, cordon/uncordon, drain, Ressourcenauslastung, Ereignisse | M | P |
| CON-K8S-003 | Namespaces anlegen und löschen, Ressourcenkontingente (CPU, RAM, Speicher), LoadBalancer-Kontingent, Ingress-Klassen und Registries je Namespace freigeben, Systemnamespaces ausblenden | M | P |
| CON-K8S-004 | Anwendungen per Formular anlegen: Deployment, StatefulSet oder DaemonSet, Image, Replikate, Umgebung, ConfigMaps/Secrets als Variablen oder Dateien, persistente Volumes, Ressourcenlimits, Platzierung (Node-Affinity), Auto-Scaling (HPA), Veröffentlichung als ClusterIP/NodePort/LoadBalancer/Ingress | M | P |
| CON-K8S-005 | Anwendungen per Manifest (YAML-Editor, Upload, URL, Git mit GitOps-Abgleich) | M | P |
| CON-K8S-006 | Anwendungsdetails: Pods, Status, Ereignisse, YAML, Logs je Pod/Container, Konsole in den Pod, Neustart, Rollback auf vorherige Revision, Skalierung | M | P |
| CON-K8S-007 | Helm: Repositories verwalten, Charts suchen und installieren (values.yaml im Editor), Releases aktualisieren, zurückrollen, deinstallieren | M | P |
| CON-K8S-008 | ConfigMaps und Secrets anlegen, bearbeiten, löschen | M | P |
| CON-K8S-009 | Services, Ingresses (Hosts, Pfade, TLS, Annotations) und Ingress-Controller verwalten | M | P |
| CON-K8S-010 | Volumes: PersistentVolumeClaims, PersistentVolumes, StorageClasses (Zugriffsmodi, Erweiterbarkeit freigeben) | M | P |
| CON-K8S-011 | Webbasiertes `kubectl`-Terminal mit gültigem Kontext des Benutzers | M | P |
| CON-K8S-012 | kubeconfig-Download für Benutzer, Zugriff über einen Proxy des Panels mit dessen RBAC | M | P |
| CON-K8S-013 | Kubernetes-RBAC: Abbildung der Panel-Rollen auf ClusterRoles/Roles, Anzeige von ServiceAccounts, Roles, Bindings | S | P |
| CON-K8S-014 | Cluster-Ereignisse anzeigen und filtern | M | P |
| CON-K8S-015 | CronJobs und Jobs anzeigen, manuell auslösen, Verlauf | S | P |
| CON-K8S-016 | Metriken über metrics-server: CPU/RAM je Knoten, Pod und Container | S | P |
| CON-K8S-017 | Zentrale Richtlinien je Cluster: Pod-Security-Standards, Ressourcen-Overcommit, Gatekeeper/Kyverno-Richtlinien | K | P |
| CON-K8S-018 | Generische Anzeige und YAML-Bearbeitung beliebiger Ressourcen inkl. CRDs | S | N |

## 10. Edge-Computing und Flotten (CON-EDG)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-EDG-001 | Edge-Gruppen: statisch (manuelle Auswahl) oder dynamisch (über Tags) | M | P |
| CON-EDG-002 | Edge-Stacks: Bereitstellung eines Stacks auf alle Umgebungen einer Edge-Gruppe, Statusanzeige je Gerät | M | P |
| CON-EDG-003 | Gestaffelte Ausrollung von Edge-Stacks (Batch-Größe, Pausieren, Rollback bei Fehlern) | S | P |
| CON-EDG-004 | Edge-Jobs: Skripte nach Zeitplan auf dem Host von Edge-Geräten ausführen, Logs sammeln | M | P |
| CON-EDG-005 | Edge-Konfigurationen: Dateien gruppenweise auf Geräte verteilen | S | P |
| CON-EDG-006 | Zeitgesteuerte Aktualisierung und Rollback von Agenten auf Edge-Geräten | S | P |
| CON-EDG-007 | Geräteerkennung und Erstkonfiguration für Massen-Rollouts (Vorab-Registrierung mit Token) | S | P |
| CON-EDG-008 | Bereitstellung auf Edge-Geräten auch ohne private Registry über Image-Caching | K | P |

## 11. Ereignisse und Host (CON-EVT)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| CON-EVT-001 | Docker-Ereignisse anzeigen (Container, Images, Netzwerke, Volumes) mit Filter | M | P |
| CON-EVT-002 | Host-Details der Umgebung: Engine-Version, Betriebssystem, Kernel, CPU, RAM, Storage-Treiber, Logging-Treiber, Plugins | M | P |
| CON-EVT-003 | Host-Dateisystem durchsuchen (über den Agenten), sofern freigegeben | S | P |
| CON-EVT-004 | Docker-Daemon-Konfiguration (`daemon.json`) im Editor bearbeiten und Daemon neu starten | S | N |
| CON-EVT-005 | Anzeige des Speicherverbrauchs nach Typ (`docker system df`) | M | P |
