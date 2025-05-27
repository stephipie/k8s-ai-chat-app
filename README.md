# AI Chat App Deployment in Kubernetes (Basis)

Dieses Projekt demonstriert das Deployment einer Multi-Container AI Chat App (Ollama Backend, Open WebUI Frontend) in einem lokalen Kubernetes Cluster (speziell optimiert für **Docker Desktop Kubernetes**). Die Anwendung wird in einem dedizierten **`ollama` Namespace** bereitgestellt.

---

## Ziel der Übung

Diese Aufgabe dient als umfassende Übung, um die gelernten Kubernetes-Grundlagen (Pods, Deployments, Services, ConfigMaps, Ingress, Namespaces) in der Praxis anzuwenden und das Zusammenspiel dieser Komponenten zu verstehen.

---

## Komponenten & Kubernetes Objekte

Die Anwendung besteht aus folgenden Hauptkomponenten:

* **Ollama (AI/LLM Backend):** Stellt die Sprachmodelle bereit.
    * Kubernetes-Objekte: **Deployment**, **Service** (ClusterIP).
    * *Hinweis zur Persistenz:* In dieser Basisversion werden Modelle nicht persistent gespeichert. Nach einem Pod-Neustart gehen heruntergeladene Modelle verloren.

* **Open WebUI (Frontend):** Bietet die Chat-Oberfläche.
    * Kubernetes-Objekte: **Deployment**, **Service** (ClusterIP).

* **Anwendungskonfiguration:** Die Verbindung zwischen WebUI und Ollama wird über eine **ConfigMap** bereitgestellt.

* **Externer Zugriff:** Der Zugriff auf das WebUI-Frontend erfolgt über **Ingress** und einen Hostnamen (`chat.local`).

* **Isolation:** Alle Ressourcen werden im **`ollama` Namespace** deployt.

---

## ✅ Voraussetzungen

Stell sicher, dass die folgenden Punkte erfüllt sind:

1.  **Docker Desktop** ist installiert und läuft.
2.  **Kubernetes in Docker Desktop** ist aktiviert (`Settings -> Kubernetes -> Enable Kubernetes`).
3.  **Ausreichend Ressourcen** in Docker Desktop zugewiesen (mind. 6 CPU Cores, 12 GB RAM unter `Settings -> Resources -> Advanced`).
4.  **`kubectl`** ist konfiguriert und greift auf dein Docker Desktop Cluster zu (`kubectl get nodes` sollte `docker-desktop` als `Ready` zeigen).
5.  Der **Metrics Server** ist installiert (optional, für `kubectl top`). Installiere ihn bei Bedarf mit:
    `kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml`
6.  **Lokale Hosts-Datei** angepasst: Füge `127.0.0.1 chat.local` zu `C:\Windows\System32\drivers\etc\hosts` (Windows) oder `/etc/hosts` (macOS/Linux) hinzu.

---

## Deployment Schritte

1.  **Klone dieses Repository** (falls noch nicht geschehen) oder navigiere zum Ordner, der die YAML-Dateien enthält (z.B. `kubernetes/ai-chat-app`).

2.  **Alle Kubernetes Ressourcen deployen:**
    ```bash
    # Zuerst den Namespace erstellen
    kubectl apply -f namespace.yaml
    # Dann alle anderen Ressourcen im aktuellen Verzeichnis deployen
    kubectl apply -f .
    ```

3.  **Deployment überprüfen:**
    Stell sicher, dass alle Pods im `ollama`-Namespace laufen.
    ```bash
    kubectl get all -n ollama
    kubectl get ingress -n ollama
    ```

4.  **Zugriff testen:**
    Öffne deinen Browser und navigiere zu `http://chat.local`. Du solltest die Open WebUI-Oberfläche sehen.

---

## Modell-Installation & Test

1.  Melde dich in der Open WebUI an und navigiere zur Modellverwaltung.
2.  Lade ein AI-Modell (z.B. `gemma:2b`) herunter. Dies kann einige Minuten dauern.
3.  Teste den Chat mit dem heruntergeladenen Modell. Eine erfolgreiche Konversation bestätigt die korrekte Verbindung von WebUI zu Ollama.

---

## Aufräumen

Um alle deployten Ressourcen zu entfernen:

```bash
# Löscht den gesamten Namespace und damit alle darin enthaltenen Ressourcen
kubectl delete namespace ollama