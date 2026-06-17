# Sofascore Football Match Shotmap Scraper ⚽🥅

Questo repository contiene un Jupyter Notebook basato su Python (`sofascore_shotmap.ipynb`) progettato per automatizzare l'estrazione di dati granulari e avanzati sulle mappe di tiro da pagine web dinamiche di Sofascore (configurato nello specifico per il match Bologna vs. Borussia Dortmund).

Lo script utilizza **Selenium WebDriver** per interagire con i componenti dinamici della pagina, chiudere i banner di interruzione, gestire il cambio di tab dei team e analizzare sistematicamente sia le coordinate spaziali (x, y) che le metriche statistiche di ogni tiro effettuato.

---

## 📌 Funzionalità Principali

* **Gestione dei Pop-up:** Rileva e chiude automaticamente i banner dei cookie e le selezioni della lingua per evitare blocchi dell'interfaccia durante lo scraping.
* **Navigazione Dinamica dei Tab:** Individua e clicca via JavaScript gli elementi per navigare direttamente alla sezione *Statistiche* del match.
* **Scraping Bilaterale (Team-Specific):** * **Bologna:** Scorre il pannello sinistro interagendo con i pulsanti per indicizzare i tiri tramite identificativi univoci (`minute|shooter|start_x`).
  * **Borussia Dortmund:** Passa al pannello destro per estrarre la collezione di tiri della squadra avversaria.
* **Estrazione Metriche Avanzate:** Raccoglie variabili esplicite per ogni evento di tiro, inclusi minuto, nome del giocatore, Expected Goals (`xG`), Expected Goals on Target (`xGOT`), esito e situazione di gioco.
* **Coordinate Mappa Spaziale:** Cattura gli elementi SVG dinamici per registrare le coordinate esatte di partenza (`cx`, `cy`) sul campo e le coordinate di arrivo nello specchio della porta (estratte dagli attributi `transform: translate()`).
* **Pipeline di Pulizia Dati:** Standardizza i valori testuali (es. normalizza caratteri speciali come `ī` in `i`, e mappa i vettori mancanti di `xGOT` in `None` di Python) prima di organizzare il tutto in un DataFrame di Pandas.

---

## 🛠️ Requisiti e Installazione

Lo script utilizza librerie standard per il data processing e lo scraping. Puoi configurare l'ambiente eseguendo questo comando nel terminale:

```bash
pip install requests beautifulsoup4 pandas openpyxl lxml selenium

```

### Prerequisiti:

* **Google Chrome** installato sul sistema.
* Una versione funzionante di Chrome WebDriver corrispondente alla tua versione locale del browser (nelle ultime versioni di Selenium la gestione è nativa e automatica).

---

## 📂 Architettura della Pipeline

1. **Inizializzazione del Driver:** Avvia un'istanza di Chrome e naviga direttamente all'URL del match di Sofascore.
2. **Configurazione UI:** Chiude i moduli di interruzione, sposta il focus sul tab delle statistiche e attende il caricamento dei container tramite regole `WebDriverWait`.
3. **Scraping Bologna:** Itera attraverso le sequenze di tiro all'interno del blocco `#tabpanel-left`.
4. **Cambio Team:** Trova il pulsante di selezione del Dortmund, clicca e riattiva la logica di estrazione all'interno del blocco `#tabpanel-right`.
5. **Strutturazione Dati:** Unisce le liste di tiri in un unico DataFrame di Pandas, applica le procedure di pulizia e mostra il dataset tabellare.
6. **Chiusura Sessione:** Termina il browser in sicurezza all'interno di un blocco `finally` per preservare la memoria di sistema.

---

## 📊 Schema dei Dati Estratti

| Campo Colonna | Descrizione | Tipo / Formato |
| --- | --- | --- |
| `team` | Nome della squadra che attacca (es. Bologna, Borussia Dortmund) | Testo (Stringa) |
| `shot_number` | Indice cronologico del tiro all'interno della sequenza estratta | Intero |
| `minute` | Il minuto specifico del match in cui è avvenuto il tiro | Testo (es. `45'`) |
| `shooter` | Nome del giocatore che ha calciato (normalizzato) | Testo (Stringa) |
| `xG` | Valore della metrica Expected Goals per quel tiro | Stringa Numerica |
| `xGOT` | Expected Goals on Target (impostato a `None` se parato/fuori) | Stringa / None |
| `outcome` | Risultato categorizzato della giocata (es. Saved, Blocked, Goal, Missed) | Testo (Stringa) |
| `situation` | Il contesto tattico dell'azione (es. Open Play, Set Piece) | Testo (Stringa) |
| `start_x` / `start_y` | Coordinate pixel della griglia in cui è stato scoccato il tiro | Stringa Numerica |
| `arrival_x` / `arrival_y` | Coordinate spaziali di arrivo del pallone nello specchio della porta | Stringa / None |

---

## ⚠️ Note e Risoluzione Problemi

> [!WARNING]
> **Identificatori Web Dinamici:** I provider di dati sportivi come Sofascore aggiornano frequentemente le classi CSS e i selettori di tracciamento. Se la pagina subisce modifiche strutturali, i selettori XPath rigidi (utilizzati per trovare l'ancora delle statistiche o i nodi dei team) potrebbero restituire un errore di tipo `NoSuchElementException`. In tal caso, ispezionare il codice sorgente HTML aggiornato e rimappare le variabili dei selettori nel notebook.

```

```
