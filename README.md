🤖 Automated Content & Trend Engine

n8n | Google Gemini 1.5 Flash | Docker | WordPress

Dieses System ist eine vollautomatisierte Pipeline zur Content-Veredelung und Trend-Analyse. Es transformiert rohe RSS-Daten und Reddit-Diskussionen mittels Künstlicher Intelligenz in hochwertige Blog-Beiträge und strategische Trend-Reports.

🏗 Infrastruktur & Hosting

Das gesamte System wird auf einer Azure VM betrieben und ist vollständig containerisiert, um maximale Portabilität und einfache Skalierung zu gewährleisten.
Docker-Setup

Die Architektur basiert auf einem Nginx Reverse Proxy, der den verschlüsselten Datenverkehr (SSL via Let's Encrypt) an die internen Dienste weiterleitet.
YAML

# Auszug aus der docker-compose.yml
services:
  nginx:
    image: nginx:alpine
    ports: ["80:80", "443:443"]
    volumes:
      - ./nginx/conf:/etc/nginx/conf.d
      - ./nginx/www:/var/www
    networks: [content-net]

  n8n:
    image: n8nio/n8n:latest
    environment:
      - N8N_HOST=n8n.wiredu.cloud
      - WEBHOOK_URL=https://n8n.wiredu.cloud/
    volumes:
      - ./n8n_data:/home/node/.n8n
    networks: [content-net]

🚀 Pipeline 1: News-to-Article (Daily News)

Dieser Workflow verarbeitet einzelne Nachrichtenmeldungen in Echtzeit.
1. Daten-Akquise (Ingestion)

    RSS Reader: Überwacht spezialisierte Quellen (z. B. Gaming-News, Tech-Blogs).

    Filter-Logik: Ein Filter-Node prüft das Publikationsdatum (pubDate), um nur neue Artikel der letzten X Stunden zu verarbeiten und Duplikate zu vermeiden.

2. KI-Processing (The Brain)

    Modell: Google Gemini 1.5 Flash.

    Prompting: Die KI erhält den Rohtext und den Auftrag, diesen nach journalistischen Kriterien umzustrukturieren, SEO-Keywords zu integrieren und ein sauberes JSON-Objekt auszugeben.

3. Publishing

    JSON Parser: Ein Code-Node extrahiert das JSON aus dem KI-String.

    WordPress API: Erstellt automatisch einen neuen Post mit Titel, Content, Tags und Kategorie.

📈 Pipeline 2: Trend-Radar (Deep Analysis)

Im Gegensatz zur News-Pipeline analysiert dieser Workflow die Masse der Informationen, um Muster zu erkennen.
1. Multi-Source Aggregation

    Input: Kombiniert RSS-Feeds mit Reddit-Daten (/r/technology.rss, /r/gaming.rss).

    Merge & Batch: Alle Titel der letzten 24 Stunden werden in einem Merge-Node gesammelt.

2. Data Compression

    Summarize: Ein Item Lists Node bündelt bis zu 100 Schlagzeilen zu einem einzigen kompakten Textblock (Headline-List). Dies spart Token-Kosten und ermöglicht der KI den "Blick für das große Ganze".

3. Trend-Prompting

Gemini analysiert die Liste auf:

    Top Trend: Welches Thema dominiert die Diskussion?

    Momentum Score: Wie schnell verbreitet sich das Thema (1-100)?

    Outlier: Identifikation von Nischen-Themen mit Potenzial.

4. Visual Reporting

Das Ergebnis wird über einen HTML-Template-Node in ein professionelles Layout gegossen:

    CSS-Styling: Inklusive dynamischer Progress-Bars für den Momentum-Score.

    Post-Radar: Veröffentlichung als spezialisierter "Trend-Report" auf dem Blog.

🛠 Fehlerbehebung & Wartung
Modul	Problem	Lösung
Mermaid	Parse Error	Labels in "Anführungszeichen" setzen.
Gemini	JSON-Formatierung	match(/\{[\s\S]*\}/) im Code-Node nutzen, um Backticks zu entfernen.
Nginx	502 Bad Gateway	Prüfen, ob der n8n-Container im gleichen Docker-Netzwerk wie Nginx ist.
JS Code	undefined map	Sicherheits-Check einbauen: Array.isArray(ideas) ? ideas : [].
🔒 Sicherheit

    SSL: Alle Subdomains sind über Certbot (Nginx-Plugin) verschlüsselt.

    Secrets: API-Keys für Gemini und WordPress-Credentials werden sicher in den n8n-Credentials gespeichert, nicht im Code.



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
        C -->|100 Headlines as 1 String| D[Gemini 1.5 Flash]
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

Dokumentation Stand: Januar 2026
