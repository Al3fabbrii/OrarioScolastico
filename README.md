# School Timetable Generator

Un generatore automatico di orari scolastici che utilizza algoritmi greedy e ricerca locale per creare orari ottimizzati rispettando vincoli rigidi e preferenze dei docenti.

## 📋 Descrizione

Questo progetto implementa un sistema di generazione automatica di orari scolastici che bilancia vincoli obbligatori (come la non sovrapposizione di lezioni) con obiettivi di ottimizzazione (come la soddisfazione delle preferenze dei docenti per i giorni liberi).

### Obiettivi del Progetto

- **Assegnazione completa**: Garantire che tutte le ore di cattedra vengano assegnate
- **Rispetto dei vincoli**: Evitare sovrapposizioni, rispettare limiti di ore giornaliere e minimizzare le ore buche
- **Ottimizzazione delle preferenze**: Massimizzare la soddisfazione delle preferenze dei docenti per i giorni liberi
- **Distribuzione equilibrata**: Distribuire le lezioni in modo uniforme durante la settimana

## 🎯 Caratteristiche Principali

### Vincoli Rigidi
- Nessuna sovrapposizione di docenti o classi nello stesso slot orario
- Massimo 4 ore giornaliere per docente
- Massimo 1 ora buca per docente in un giorno
- Massimo 2 lezioni della stessa materia per classe nello stesso giorno

### Ottimizzazioni
- Rispetto delle preferenze dei docenti per i giorni liberi (prima scelta: peso 2, seconda scelta: peso 1)
- Minimizzazione delle ore buche
- Accorpamento delle lezioni della stessa materia quando possibile
- Distribuzione equilibrata delle ore durante la settimana
- Evitamento di troppe lezioni consecutive

## 🏗️ Struttura del Codice

### 1. Configurazione Iniziale
```python
GIORNI = ["Lunedì", "Martedì", "Mercoledì", "Giovedì", "Venerdì", "Sabato"]
SLOTS = [1, 2, 3, 4, 5]  # 5 slot orari giornalieri
MAX_ORE_GIORNO = 4
MAX_ORE_BUCHE = 1
```

### 2. Componenti Principali

#### `leggi_cattedre(dati_cattedre)`
Parsing dei dati delle cattedre dei docenti da formato testuale.

#### `genera_preferenze_giorni_liberi(professori, giorni)`
Genera le preferenze dei docenti per i giorni liberi con sistema di pesi.

#### `calcola_punteggio(...)`
Calcola il punteggio di un'assegnazione considerando:
- Ammissibilità della lezione
- Vincoli su ore giornaliere e ore buche
- Preferenze per giorni liberi
- Flessibilità del docente
- Distribuzione settimanale delle lezioni

#### `orario_scolastico(...)`
Algoritmo greedy principale basato su **grado di saturazione**:
1. Seleziona la combinazione docente-classe con maggiore saturazione (meno slot disponibili)
2. Assegna allo slot con miglior punteggio
3. Aggiorna la saturazione e ripete

#### `completa_orario(...)`
Riempie gli slot rimasti vuoti dopo la fase greedy, rilassando gradualmente i vincoli se necessario.

#### `ottimizza_orario_ricerca_locale(...)`
Ottimizza l'orario attraverso ricerca locale:
- **Mosse**: scambio di slot tra lezioni o spostamento di una lezione
- **Accettazione**: solo mosse che migliorano il valore della soluzione
- **Terminazione**: dopo un numero massimo di iterazioni o di tentativi senza miglioramento

### 3. Flusso di Esecuzione

```
main()
  ├── FASE 1: orario_scolastico() - Creazione orario iniziale (greedy)
  ├── FASE 2: completa_orario() - Completamento slot vuoti
  ├── FASE 3: ottimizza_orario_ricerca_locale() - Ottimizzazione
  └── Analisi e visualizzazione risultati
```

## 🚀 Utilizzo

### Prerequisiti
```bash
pip install pandas numpy
```

### Esecuzione
```python
# Definisci i dati delle cattedre
dati_cattedre = """
Smith: 3 ore in 3B, 3 ore in 5C
Johnson: 4 ore in 3A
...
"""

# Esegui l'algoritmo
cattedre = leggi_cattedre(dati_cattedre)
preferenze_giorni = genera_preferenze_giorni_liberi(cattedre.keys(), GIORNI)
orario, giorni_liberi, orari_per_classe = main()
```

## 📊 Output

Il sistema produce:

1. **Orari per classe**: DataFrame con le assegnazioni per ogni classe
2. **Giorni liberi**: Assegnazione dei giorni liberi per ogni docente
3. **Statistiche**:
   - Punteggio della soluzione
   - Slot vuoti rimasti
   - Soddisfazione delle preferenze (prima scelta, seconda scelta, altre)
   - Distribuzione delle ore per docente

### Esempio di Output
```
Valutazione finale dell'orario ottimizzato:
Punteggio della soluzione: 15420
Slot vuoti rimasti: 0

Statistiche sulla soddisfazione delle preferenze:
Prima scelta: 18/34 (52.9%)
Seconda scelta: 12/34 (35.3%)
Altra scelta: 4/34 (11.8%)
```

## 🔧 Personalizzazione

### Modifica dei Vincoli
Puoi modificare i vincoli globali all'inizio del codice:
```python
MAX_ORE_GIORNO = 4  # Cambia il limite di ore giornaliere
MAX_ORE_BUCHE = 1   # Cambia il limite di ore buche
```

### Aggiunta di Nuove Classi
Modifica la lista `CLASSI`:
```python
CLASSI = ["3A", "3B", "4A", "4C", "5B", "5C", "nuova_classe"]
```

### Personalizzazione delle Preferenze
Modifica la funzione `genera_preferenze_giorni_liberi()` per utilizzare preferenze reali invece che casuali.

## 📝 Algoritmo

### Fase 1: Greedy con Grado di Saturazione
L'algoritmo utilizza il **grado di saturazione** come euristica principale:
- Priorità alle combinazioni docente-classe con meno slot disponibili
- Evita situazioni di stallo dove alcune assegnazioni diventano impossibili

### Fase 2: Completamento
Riempie gli slot vuoti con approccio progressivo:
1. Tentativo con vincoli rigorosi
2. Rilassamento graduale dei vincoli se necessario

### Fase 3: Ricerca Locale
Ottimizza la soluzione attraverso:
- **Scambi**: due lezioni si scambiano di posto
- **Spostamenti**: una lezione viene spostata in un altro slot

La ricerca continua finché trova miglioramenti o raggiunge i criteri di terminazione.

## ⚠️ Limitazioni Note

- Il sistema potrebbe non trovare una soluzione completa per configurazioni troppo vincolate
- L'ottimizzazione locale può convergere a minimi locali
- Il tempo di esecuzione aumenta con il numero di docenti e classi


## 📄 Licenza

Questo progetto è distribuito sotto licenza MIT.

---

**Nota**: Questo è un problema NP-completo, quindi per istanze molto grandi potrebbero essere necessari tempi di calcolo significativi o l'accettazione di soluzioni sub-ottimali.
