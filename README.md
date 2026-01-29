# 🤖 Automated Content & Trend Engine
> **n8n | Google Gemini  | Docker | WordPress**

Dieses System ist eine vollautomatisierte Pipeline zur Content-Veredelung und Trend-Analyse. Es transformiert rohe RSS-Daten und Reddit-Diskussionen mittels Künstlicher Intelligenz in hochwertige Blog-Beiträge und strategische Trend-Reports.

---

## 🏗 Infrastruktur & Hosting

Das gesamte System wird auf einer **Azure VM** betrieben und ist vollständig containerisiert.

### Docker-Architektur
Die Architektur basiert auf einem **Nginx Reverse Proxy**, der den verschlüsselten Datenverkehr (SSL via Let's Encrypt) an die internen Dienste weiterleitet.

```yaml
services:
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/conf:/etc/nginx/conf.d
      - ./nginx/www:/var/www
    networks:
      - content-net

  n8n:
    image: n8nio/n8n:latest
    environment:
      - N8N_HOST=n8n.wiredu.cloud
      - WEBHOOK_URL=[https://n8n.wiredu.cloud/](https://n8n.wiredu.cloud/)
    volumes:
      - ./n8n_data:/home/node/.n8n
    networks:
      - content-net
````

📈 Trend-Radar Workflow (Visualisierung)

Der Trend-Radar analysiert die Masse der Informationen, um Muster zu erkennen, anstatt nur Einzelnachrichten zu verarbeiten.

````mermaid
graph TD
    %% Datenquellen
    subgraph Data_Ingestion [Data Ingestion]
        A1[RSS Feed: Gaming] --> B[Merge Node]
        A2[RSS Feed: Tech] --> B
        A3[Reddit: /r/technology] --> B
        A4[Reddit: /r/gaming] --> B
    end

    %% Verarbeitung
    subgraph Processing [Data Compression & AI]
        B --> C[Item Lists: Summarize]
        C -->|100 Headlines as 1 String| D[Gemini]
        D -->|JSON Output| E[Code Node: JSON Parser]
    end

    %% Publikation
    subgraph Output [Visual Reporting]
        E --> F[HTML Template Node]
        F -->|Styled Content| G[WordPress: Trend-Radar Post]
    end

    %% Styling
    style D fill:#4285F4,stroke:#fff,stroke-width:2px,color:#fff
    style G fill:#21759b,stroke:#fff,stroke-width:2px,color:#fff
    style A3 fill:#ff4500,stroke:#fff,stroke-width:1px,color:#fff
    style A4 fill:#ff4500,stroke:#fff,stroke-width:1px,color:#fff
````
🚀 Kern-Prozesse
  1. News-to-Article Pipeline

    Input: Echtzeit-Monitoring von spezialisierten RSS-Quellen.

    Filter: Prüfung auf pubDate, um Duplikate zu vermeiden.

    KI-Veredelung: Gemini  transformiert Rohtexte in SEO-optimierte WordPress-Posts.

  2. Trend-Radar Strategie

    Kompression: Ein Item Lists Node bündelt bis zu 100 Schlagzeilen zu einem Kontext-Block.

    Analytik: Identifikation von Top Trends, Momentum Scores und Outliern (Nischenthemen).

    Automatisches Design: Erstellung von HTML-Reports mit dynamischen Progress-Bars für WordPress.
   
   
🔒 Sicherheit & Best Practices

    Secrets: Alle API-Keys (Gemini, WP-Application Passwords) liegen verschlüsselt in den n8n-Credentials.

    SSL: Automatische Zertifikatserneuerung via Certbot auf der Azure-Instanz.
