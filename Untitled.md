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
	Lavora sulle memorie primaria e secondaria
- **a medio termine**/swapping
	I processi vengono spostati tra mem. secondaria e mem. primaria
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
Un processo è composto da *codice*, *dati* e *risorse*



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

