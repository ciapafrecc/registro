# Registro presenze — guida per metterlo online

Il sito è un unico file, `index.html`. GitHub Pages lo pubblica, ma da solo non può ricordare nulla:
per l'**accesso con email e password** e il **salvataggio dei dati** serve un servizio online.
Qui si usa **Firebase** (di Google): ha un piano gratuito (Spark) più che sufficiente per questo uso.

Ci vogliono circa 15 minuti. Fai i passi nell'ordine. I nomi dei menu nella console di Firebase
possono cambiare leggermente da una versione all'altra, ma la struttura resta questa.

---

## A. Crea il progetto Firebase

1. Vai su <https://console.firebase.google.com> e accedi con un account Google.
   Tocca **Crea un progetto**, dagli un nome (es. `registro-presenze`).
   Google Analytics non serve: puoi disattivarlo.

2. **Attiva l'accesso con email e password.**
   Menu a sinistra → **Authentication** → **Inizia** → scheda **Metodo di accesso**
   → **Email/Password** → attiva il primo interruttore → **Salva**.

3. **Crea il database.**
   Menu a sinistra → **Firestore Database** → **Crea database**.
   - Se ti chiede l'edizione, scegli **Standard**.
   - Posizione: scegli una in Europa (es. `eur3` o `europe-west`).
   - Modalità: **produzione**.

4. **Incolla le regole di sicurezza** (importante: fanno in modo che ognuno veda solo i propri dati).
   In Firestore Database apri la scheda **Regole**, cancella tutto il testo e incolla questo, poi **Pubblica**:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /utenti/{uid} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
       match /utenti/{uid}/appelli/{appelloId} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```

5. **Copia i dati di collegamento.**
   Tocca l'ingranaggio accanto a "Panoramica del progetto" → **Impostazioni progetto**.
   In fondo, in **Le tue app**, tocca l'icona **`</>`** (Web), dai un nome all'app (es. `presenze`),
   lascia **spenta** l'opzione "Firebase Hosting" e tocca **Registra app**.
   Compare un blocco di codice con `const firebaseConfig = { ... }`: tieni aperta la pagina,
   ti servono i valori tra le graffe.

---

## B. Incolla i dati in `index.html`

Apri `index.html` e cerca il blocco **CONFIGURAZIONE** (è all'inizio dello script, sotto il commento
con scritto "incolla qui i dati del tuo progetto Firebase"). Sostituisci ogni `INCOLLA_QUI`
con il valore corrispondente di Firebase:

```js
var FIREBASE_CONFIG = {
  apiKey: "AIza....",
  authDomain: "registro-presenze-xxxx.firebaseapp.com",
  projectId: "registro-presenze-xxxx",
  storageBucket: "registro-presenze-xxxx.firebasestorage.app",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abcdef"
};
```

Tieni le virgolette e le virgole. Se Firebase mostra anche `measurementId`, puoi ignorarlo.

> L'`apiKey` di Firebase non è una password: è fatta per stare nel codice di un sito pubblico.
> A proteggere i dati sono l'accesso con email e password e le regole del passo A4.

---

## C. Pubblica su GitHub Pages

1. Su <https://github.com> crea un **nuovo repository** (es. `registro-presenze`), pubblico.
2. **Add file → Upload files**, trascina `index.html` (e se vuoi questo `README.md`) e tocca **Commit changes**.
   Il file deve chiamarsi esattamente `index.html` e stare nella cartella principale.
3. Vai in **Settings → Pages**. Sotto *Build and deployment* scegli
   **Source: Deploy from a branch**, **Branch: `main`**, cartella **`/ (root)`**, poi **Save**.
4. Dopo uno-due minuti il sito è online a un indirizzo come
   `https://IL-TUO-NOME-UTENTE.github.io/registro-presenze/`.

Per modificare i dati di Firebase anche dopo (passo B), apri `index.html` su GitHub,
tocca la matita, modifica, **Commit changes**: il sito si aggiorna da solo.

---

## D. Primo accesso

1. Apri l'indirizzo del sito e tocca **Crea un account**: scrivi email e una password di almeno 6 caratteri.
2. Se in seguito Firebase segnala un dominio non autorizzato (o il messaggio per reimpostare la
   password non arriva): **Authentication → Impostazioni → Domini autorizzati → Aggiungi dominio**
   e scrivi `IL-TUO-NOME-UTENTE.github.io` (solo il dominio, senza `https://` e senza nome del repository).
3. Su iPhone: da Safari tocca **Condividi → Aggiungi alla schermata Home** e il registro si apre come un'app.

Resti collegato su quel dispositivo finché non tocchi **Esci dall'account**
(si trova in *Account e backup*, toccando la striscia “Salvataggio online attivo” in fondo alla home).
Su un altro dispositivo basta aprire lo stesso indirizzo e accedere con la stessa email e password.

---

## E. Portare i dati dal vecchio file

Nella versione che usavi prima: **Account e backup → Scarica backup** (oppure **Copia backup**).
Nel sito nuovo, dopo l'accesso: **Account e backup → Scegli un file di backup**
(oppure incolla il testo e tocca **Ripristina dal testo**). Il backup si aggiunge ai dati presenti, non cancella nulla.

## F. Usarlo con un amico

Basta che apra lo stesso indirizzo e crei il **proprio** account: ogni account vede soltanto i propri
gruppi e appelli, separati da quelli degli altri.

---

## Se qualcosa non va

| Cosa vedi | Cosa fare |
|---|---|
| In home c'è la striscia rossa "Dati solo su questo dispositivo" | I dati di Firebase non sono stati incollati (passo B), o sono rimasti dei `INCOLLA_QUI`. |
| "L'accesso con email e password non è attivo" | Passo A2: attiva **Email/Password**. |
| "Il database non ha dato il permesso" | Passo A4: incolla e **Pubblica** le regole. |
| "I dati di Firebase in index.html non sono corretti" | Passo B: ricontrolla di aver copiato `apiKey` e `projectId` per intero. |
| "Non riesco a caricare i dati" | Manca la connessione: tocca **Riprova**. |
| Il sito mostra la pagina del repository o un errore 404 | Controlla che il file si chiami `index.html` e che in *Settings → Pages* sia scelto il branch `main`; attendi un paio di minuti. |
| Password dimenticata | Nella schermata di accesso scrivi l'email e tocca **Password dimenticata?** (controlla anche lo spam). |

## Cose da sapere

- **Offline**: se perdi la connessione mentre fai un appello, l'appello resta in coda sul telefono e parte
  da solo quando torna internet. Per aprire il sito servono comunque almeno una volta i file online.
- **Costi**: con questo uso (poche persone, pochi appelli) si resta ampiamente nei limiti gratuiti.
  Firebase non chiede una carta per il piano Spark.
- **Backup**: ogni tanto scarica un backup da *Account e backup*: costa dieci secondi.
