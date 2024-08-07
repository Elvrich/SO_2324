**Interleaving**

**Events**
- Interrupt - da fuori 
- Trap - dal sistema

**Single Stream Batch Processing System**
- *Batch* = non interattivo

Il SSBPS è utilizzato nella gestione dei job (compiti per la CPU). Quando occorrono, vengono messi in una coda.

### **modello a livelli / dual mode**
Ogni CPU ha un *instruction set* di istruzioni che sa eseguire
Non tutte le istruzioni dell'*instruction set* sono Eliggibili. Ci devono essere dei livelli di permesso
> Esempio: **ram**
>una porzione della memoria è identificata da due registri "base" e "limite", che sono rispettivamente l'inizio e la fine dello spazio assegnato ad un processo

Uscire da questo intervallo può essere rischioso, ergo risulta utile avere un sistema che implementi dei livelli di "diritti" o "responsabilità". Per esempio, le operazioni I-O non sono liberamente utilizzabili dai processi.
Il genere di istruzioni protette si chiamano *System Call*

### **System Call**
Le Sys.Call, quando chiamate, sollevano una *Trap* cambiando il **bit di modalità** in modo da poter eseguire un set protetto di istruzioni

Il *modello a livelli* è la generalizzazione del *dual mode* e prevede più livelli di "accesso" alle istruzioni

> esempio comando `cp file1 file2` *copy*
> apre o crea il `file2` e fa una sequenza di letture su `file1`

**Processi**
creazione e terminazione di processi evocano delle *syscall* 

### **Come si realizza un SO?**
- linguaggio (tipicamente C)
	- Architettura del *sw*
		- Unix - architettura stratificata
			Gli utenti interagiscono con i diversi livelli per avere effetto sul *hw*. Gli utenti interagiscono  con i comandi (o compilatori, interpreti, librerie di sistema), il quale comunica col **Kernel** tramite  le *syscalls*, che usa i *driver* dei dispositivi per interagire con i controller(o terminali, dischi, nastri, eccetera)
		- Microkernel
			Identifica una parte minima di kernel e dei moduli da applicarci per estendere le funzionalità. I Microkernel prevede, di base, l'implementazione di processi, RAM e comunicazione tra processi
			I moduli, quelli in relazione tra loro, devono per forza passare attraverso il microkernel per comunicare tra loro. Guadagna in memoria ma creando un collo di bottiglia
		- Cloud (macchina virtuale)
			Le macchine virtuali sono un insieme di processi "montati" su *hw* virtuale che a sua volta deve interagire con quello fisico, creando troppi strati

### **Processi**
Hanno un PCB (process control block) contenente diverse informazioni, tra cui 
PID: process identifier
programma: nome del prog in esecuzione
utente: "proprietario" della esecuzione
"RAM" 
*stato*: running, waiting, ready, nuovo, terminato
	**Init** (pid == 1), un processo importante


### Interleaving istruzioni e Context switch
**Interleaving istruzioni**
Realizza il parallelismo virtuale

**Context switch**
Cambia lo stato del processo e aggiorna il PCB

### Scheduling
Meccanismo col quale si sceglie quale sia la prossima richiesta da servire, tra i processi "ready".

Alcuni esempi di **scheduling** sono:
- **a lungo termine**: 
	Lavora sulle memorie primaria e secondaria, decide quale processo deve passare da memoria secondaria a primaria, quando la primaria ha spazio
- **a medio termine**/swapping
	I processi vengono spostati tra mem. secondaria e mem. primaria, focalizzato sulla sostituzione tra processi in memorie diverse. Quindi di decidere di muovere un processo in memoria secondaria in favore di uno che deve essere spostato in memoria principale
- **a breve termine**:
	scelta di quale tra i processi ready riceve la CPU

**Criteri (algoritmi) di Scheduling**
1. FIFO (First In First out)
2. Shortest Jobs First (shortest remaining time first) (causa *starvation*)
3. round robin
4. a priorità
5. multilivello
6. multilivello con feedback

### Prelazione
se per un criterio di scheduling avviene almeno uno di questi cambi di stato:
- waiting -> ready
- running -> ready
allora avviene prelazione della CPU


### meccanismo di comunicazione tra processi
- modello a **memoria  condivisa**
- modello a **scambio di messaggi**

### Memoria condivisa: buffer limitato
array statico di elementi 
Tipicamente trattata come una coda (dato che è un buffer)
avremo due celle di memoria che dicono dove si può inserire un elemento e una cella che dice da dove prelevo un dato

### Modello a scambio di messaggi
**coda a capacità nulla**: rendez-vous


### Thread 
formati da *ID* + *stack*
sono diversi segmenti attivi interni al codice
Un processo è composto da *codice*, *dati* e *risorse*. 
I thread condividono le variabili globali

La *comunicazione* e il *context-switch* tra thread sono più rapidi

Come il **SO** gestisce i thread:
- Il **SO** deve conoscere la relazione processo-thread (associa thread a quale proc.)
- deve **Gestire la CPU**

Vediamo 3 modi di gestire la CPU per i Thread:
1. a ogni processo il SO assegna una sola risorsa di esecuzione
2. a ogni processo il SO assegna tante risorse di esecuzione quanti sono i suoi thread
3. **molti a molti**:
	Il Sistema operativo assegna al processo un numero di risorse (Lwp), i quali rappresentano la possibilità dei Thread di essere inseriti nella coda *Ready*. Il Processo utente assegna gli Lwp a dei suoi Thread interni (Thread utente) e il SO si occupa di mandarli in esecuzione. Quando un Thread utente viene rimosso dalla coda Ready, avviene una upcall dal SO al processo utente, in modo che quest'ultimo sia notificato e possa "rivalutare" a chi assegnare Lwp

### Esecuzione concorrente asincrona
Problematiche legate alla condivisione di dati

### Sezione critica
Buone proprietà al problema della *sezione critica*
1. mutua esclusione
2. attesa limitata
3. progresso: I processi che non usano la variabile condivisa non impediscono gli altri processi

### Sezione critica soluzione 1

processo j:
``` C
while(turn != j) {}
//< sezione critica >
turno = i
```
processo i:
``` C
while(turn != i) {}
//< sezione critica >
turno = j
```
Il turno va contro la proprietà del *progresso*, perché se un processo esce dalla propria sezione critica, non può rientrarci finché un altro processo gli passi il turno
Infine, questa soluzione ha delle attese attive. Le soluzioni *vere* implementano meccanismi che evita il check in loop del turno (quando finisce la sezione critica, si solleverà un evento che riattiva i processi in attesa)

### sezione critica soluzione 2
uso un vettore `boolean flag[]` dove se `flag[i] == True` allora *processo i* vuole entrare in sezione critica e se `flag[j] == True` allora lo vuole il *processo j* 
*processo i*
```C
flag[i] = True;    //ingresso
while (flag[j]) {} //ingresso
//< sezione critica >
flag[i] = False;   //uscita
```
Visto che le due righe di ingresso non sono atomiche, può avvenire interleaving durante l'esecuzione tra le due, permettendo a entrambe di accedere alla sezione critica e rompendo il sistema
Tuttavia rispetta la proprietà di *progresso*

### algoritmo di Peterson
Variabili necessarie
`turno` -> {`i`,` j`}
`flag[i]`, `flag[j]`
*Processo i*:
``` C
flag[i] = True; //ingresso
turno = j  //ingresso
while (flag[j] && turno == j) {} //ingresso
//< sezione critica >
flag[i] = False;//uscita
```
Notiamo che la sezione di ingresso è complicata, anche per gestire 2 soli processi.
Soddisfa le tre proprietà. Tuttavia avviene la *racing/race condition*, ovvero i processi sono in competizione per accedere alla propria sezione critica
**Problemi**: 
- La sezione di ingresso diventa molto complessa (8 righe. Implementa l'algo del fornaio, cioè il numero per il bancone).
- Attesa attiva (busy waiting)

### soluzioni hardware
La CPU fornisce istruzioni speciali in esecuzione atomica
- test and set
- swap

### Test & Set
``` C
boolean TestAndSet(boolean *variabile) {
	boolean valore = *variabile;
	*variabile = True;
	return valore;
}
```
Quindi permette di:
*processo 1*:
```C
boolean lock = False;// variabile condivisa
while (TestAndSet(&lock)) {}
//< sezione critica >
lock = False;
```
**difetti**:
- attesa attiva
- non garantisce che la risorsa sia condivisa in maniera equa


### Swap
< mancante >

### Semafori
operazioni: 
`P()` -> verifica (-1)
`V()` -> incrementa (+1)
**Struttura dell'oggetto semaforo** in breve:
```C
struct sem(
	value int
	
)
```

---

>comandi divertenti:
>```bash
>strace -wc <command>
>```
>visualizza il numero di syscalls evocate da questo comando
>
>```bash
>vmstat
>
>```
>```bash
>ps -aux
>```
>elenca tutti i processi in esecuzione
>```
>wc -l
>```
>word count `-l` (opzione che indica di contare le linee)
>```bash
>ps -aux | wc -l
>```
>compone i due comandi per restituire il numero di processi in esecuzione
>

> [!Note] 
> scheduling a lungo termine

