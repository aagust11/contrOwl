# contrOwl

# ContrOwl
## Sistema segur per a la realització d’exàmens digitals

### 1. Objectiu del sistema

**ContrOwl** és una plataforma destinada a permetre la realització d’exàmens, proves i activitats digitals en un entorn controlat.

El docent ha de poder crear una sessió, assignar-hi una **URL autoritzada** i generar un **codi d’accés de 6 caràcters**. L’alumnat introdueix aquest codi al client ContrOwl, s’identifica amb el seu nom i accedeix exclusivament a la URL configurada pel docent.

Durant la prova, ContrOwl ha de restringir l’ús de l’ordinador i permetre al docent supervisar en temps real l’estat dels dispositius.

El sistema ha d’impedir o detectar intents d’abandonar l’entorn segur i ha de permetre al docent recuperar remotament un dispositiu que hagi quedat bloquejat.

---

# 2. Arquitectura general

ContrOwl estarà format per tres components principals:

## ContrOwl Admin / Teacher

Aplicació web utilitzada pel docent o administrador per:

- crear sessions;
- generar codis;
- assignar URLs;
- veure alumnes connectats;
- veure les pantalles dels alumnes;
- consultar captures;
- veure incidències;
- revisar els segons previs a un bloqueig;
- desbloquejar remotament un alumne;
- finalitzar sessions.

## ContrOwl Client

Aplicació instal·lada als dispositius de l’alumnat.

S’encarrega de:

- demanar el codi de sessió;
- demanar el nom de l’alumne;
- carregar la URL autoritzada;
- executar el mode segur;
- limitar accions del sistema;
- compartir l’estat de pantalla amb el docent;
- detectar incidències;
- bloquejar l’entorn quan sigui necessari;
- rebre ordres de desbloqueig remot.

## ContrOwl Server

Servei intermediari responsable de:

- gestionar sessions;
- validar codis;
- distribuir configuracions;
- comunicar clients i professors;
- mantenir l’estat dels alumnes;
- gestionar captures i previsualitzacions;
- conservar temporalment informació relacionada amb incidències.

---

# 3. Apartat d’administració

ContrOwl disposarà d’un apartat específic:

# ADMIN

Aquest apartat serà el centre de control de cada sessió.

El docent podrà crear una nova sessió indicant:

- nom de la sessió;
- assignatura, opcional;
- grup, opcional;
- URL que s’ha d’obrir;
- codi d’accés;
- configuració de seguretat.

Exemple:

**Sessió:** Examen Estructures  
**URL:** `https://examen.insmollet.cat/estructures`  
**Codi:** `7K4TP9`

---

# 4. Codi de sessió

Cada sessió disposarà d’un codi únic de:

## 6 caràcters

El docent tindrà dues possibilitats.

### Generació automàtica

Serà l’opció predeterminada.

ContrOwl proposarà automàticament un codi aleatori.

Exemple:

`K7M4PX`

El docent podrà acceptar-lo o generar-ne un altre.

### Codi manual

El docent també podrà escriure directament el codi que vulgui.

ContrOwl comprovarà que:

- tingui 6 caràcters;
- no estigui ja en ús;
- sigui vàlid.

---

# 5. Caràcters no ambigus

Els codis generats automàticament evitaran caràcters fàcilment confusibles.

Per exemple, no s’haurien d’utilitzar:

- `0`
- `O`
- `1`
- `I`
- `L`

Es podria utilitzar un conjunt semblant a:

`23456789ABCDEFGHJKMNPQRSTUVWXYZ`

---

# 6. Majúscules i minúscules

Els codis de ContrOwl **no distingiran entre majúscules i minúscules**.

Per tant:

`K7M4PX`

serà equivalent a:

`k7m4px`

o:

`K7m4Px`

Internament, el sistema normalitzarà automàticament tots els codis.

---

# 7. Accés de l’alumnat

Quan l’alumne obri ContrOwl Client, apareixerà una pantalla molt simple.

## Introdueix el codi de sessió

```text
[ _ _ _ _ _ _ ]

ENTRAR
```

ContrOwl validarà el codi amb el servidor.

Si és correcte, passarà al següent pas.

---

# 8. Identificació obligatòria de l’alumne

Abans de mostrar la URL de l’examen, ContrOwl demanarà:

## Escriu el teu nom i cognoms

```text
Nom i cognoms:

[____________________________]

CONTINUAR
```

Aquest pas serà obligatori.

La URL de l’examen **no es carregarà fins que l’alumne s’hagi identificat**.

---

# 9. Associació alumne-dispositiu

Quan l’alumne introdueixi el seu nom, ContrOwl associarà:

- sessió;
- codi;
- nom de l’alumne;
- dispositiu;
- identificador intern del client;
- hora d’entrada;
- estat de connexió.

A partir d’aquell moment, al panell del docent no es mostrarà només un dispositiu, sinó una entrada identificada.

Per exemple:

**Laia Martínez — PC-23**

---

# 10. Entrada a l’examen

Després d’introduir el nom:

1. ContrOwl registra l’alumne;
2. activa les restriccions;
3. entra en mode segur;
4. carrega la URL definida pel professor.

L’alumnat no haurà d’escriure manualment la URL.

El codi de sessió determina automàticament quina pàgina s’ha d’obrir.

---

# 11. Panell del docent

El docent podrà veure tots els alumnes connectats en una mateixa pantalla.

Per exemple:

```text
EXAMEN ESTRUCTURES · 3r ESO B

24 alumnes connectats

┌───────────────────┬───────────┬─────────────┐
│ Alumne            │ Estat     │ Incidències │
├───────────────────┼───────────┼─────────────┤
│ Anna Puig         │ 🟢 Actiu  │ 0           │
│ Marc Vila         │ 🟢 Actiu  │ 1           │
│ Júlia Serra       │ 🔴 Bloc.  │ 2           │
│ Pol Garcia        │ ⚫ Offline │ -           │
└───────────────────┴───────────┴─────────────┘
```

---

# 12. Vista en directe de les pantalles

El docent podrà veure des del seu ordinador una previsualització de la pantalla de cada alumne.

Cada targeta mostrarà:

- nom de l’alumne;
- estat;
- miniatura de la pantalla;
- hora de l’última actualització;
- incidències.

Exemple conceptual:

```text
┌────────────────────────┐
│ Anna Puig          🟢  │
│                        │
│   [ pantalla actual ]  │
│                        │
│ Última actualització   │
│ fa 1 s                 │
└────────────────────────┘
```

---

# 13. Supervisió de pantalles

La monitorització podrà funcionar mitjançant actualitzacions freqüents o transmissió de baixa freqüència.

No és necessari transmetre vídeo complet a 30 o 60 FPS.

Per a un entorn docent és preferible prioritzar:

- consum de xarxa reduït;
- moltes pantalles simultànies;
- prou actualització per detectar què està fent l’alumne.

Per exemple, ContrOwl podria mostrar actualitzacions periòdiques de la pantalla i augmentar-ne la freqüència quan el docent selecciona un alumne concret.

---

# 14. Vista ampliada

Si el docent clica sobre la pantalla d’un alumne, s’obrirà una vista ampliada.

Mostrarà:

- nom de l’alumne;
- pantalla actual;
- estat del dispositiu;
- incidències recents;
- hora;
- opcions de control.

---

# 15. Captura manual

Quan el docent estigui visualitzant un alumne, podrà prémer:

# FER CAPTURA

ContrOwl demanarà immediatament al client una captura de pantalla.

La captura quedarà vinculada a:

- alumne;
- sessió;
- data;
- hora.

Això permetrà al docent guardar una evidència concreta d’allò que estava apareixent a la pantalla.

---

# 16. Captures automàtiques per incidència

Quan es produeixi una incidència important, ContrOwl podrà generar automàticament una captura.

Per exemple:

- intent d’Alt+Tab;
- intent d’obrir una altra aplicació;
- intent d’accedir a una URL no autoritzada;
- pèrdua de focus;
- tancament del client.

D’aquesta manera, la incidència no només indicarà què ha passat, sinó també què es mostrava al dispositiu.

---

# 17. Memòria visual temporal

ContrOwl mantindrà temporalment els últims segons de la pantalla de cada alumne.

L’objectiu és poder reconstruir què ha passat immediatament abans d’una incidència.

Es pot implementar mitjançant un **buffer circular local**.

Per exemple:

**últims 10-15 segons**.

El sistema no necessita guardar permanentment tot l’examen.

Només es conserva temporalment aquesta finestra curta.

---

# 18. Funcionament del buffer

Durant l’examen:

```text
t-15
t-14
t-13
...
t-2
t-1
t
```

Les imatges més antigues es van eliminant automàticament.

En funcionament normal aquest buffer no s’envia ni es desa permanentment.

---

# 19. Congelació del buffer davant una incidència

Si ContrOwl detecta una incidència crítica:

1. bloqueja el client si correspon;
2. congela temporalment el buffer;
3. conserva els segons previs;
4. registra l’acció que ha provocat la incidència;
5. envia la informació al docent.

Això permet analitzar què ha passat abans del bloqueig.

---

# 20. Revisió d’una incidència

Quan un alumne queda bloquejat, el docent podrà clicar-hi.

ContrOwl mostrarà una vista semblant a:

```text
JÚLIA SERRA

ESTAT: BLOQUEJAT

Motiu:
Intent de canvi d’aplicació

Acció detectada:
ALT + TAB

Hora:
10:43:21

[ reproducció dels últims segons ]

10:43:10 ─────────────── 10:43:21
```

---

# 21. Historial visual de la incidència

El docent podrà revisar els últims segons com una seqüència.

Per exemple:

```text
◀──────────────●──────────────▶

-10 s                     0 s
```

Això permet distingir entre:

- una acció accidental;
- una incidència tècnica;
- un intent real de sortir de ContrOwl.

---

# 22. Motiu exacte del bloqueig

ContrOwl haurà de mostrar clarament què ha generat el bloqueig.

Exemples:

```text
ALT + TAB detectat
```

```text
Tecla Windows detectada
```

```text
Intent d’obrir chrome.exe
```

```text
Intent d’accedir a google.com
```

```text
ContrOwl ha perdut el focus
```

```text
Client tancat inesperadament
```

---

# 23. Bloqueig de l’alumne

Quan ContrOwl bloquegi un alumne, la seva pantalla mostrarà una interfície simple.

Per exemple:

# CONTROWL

## Sessió temporalment bloquejada

Espera que el professor desbloquegi el dispositiu.

No s’ha de mostrar:

- contrasenya;
- camp d’administrador;
- botó de sortida;
- opció per desactivar ContrOwl.

---

# 24. Desbloqueig remot

El docent podrà desbloquejar qualsevol alumne des del panell d’administració.

Seleccionarà l’alumne i podrà prémer:

# DESBLOQUEJAR

El servidor enviarà una ordre segura al ContrOwl Client corresponent.

El client:

1. validarà l’ordre;
2. eliminarà l’estat de bloqueig;
3. tornarà a mostrar l’examen;
4. registrarà el desbloqueig.

---

# 25. Desbloqueig sense contrasenya al dispositiu de l’alumne

Aquest és un requisit fonamental.

**No existirà cap contrasenya de desbloqueig que el professor hagi d’introduir físicament a l’ordinador de l’alumne.**

El professor podrà desbloquejar el dispositiu directament des del seu ordinador.

Per tant:

```text
Professor
     ↓
ContrOwl Admin
     ↓
ContrOwl Server
     ↓
Client de l’alumne
     ↓
DESBLOQUEJAT
```

---

# 26. Avantatge de no utilitzar contrasenya local

Una contrasenya local presenta un problema evident.

Si l’alumnat l’arriba a veure o descobrir, el mecanisme de seguretat queda compromès.

ContrOwl evitarà aquest problema.

L’autorització de desbloqueig serà remota i associada al compte autenticat del docent.

---

# 27. Autorització segura del desbloqueig

Tot i que no es demani una contrasenya cada vegada, el sistema haurà de validar que l’ordre prové d’un docent autoritzat.

Per exemple mitjançant:

- sessió autenticada;
- token segur;
- connexió xifrada;
- identificador de sessió;
- identificador del dispositiu.

Un alumne no ha de poder construir manualment una petició de desbloqueig.

---

# 28. Registre del desbloqueig

Cada desbloqueig quedarà registrat.

Exemple:

```text
10:43:21   ALT+TAB detectat
10:43:21   Client bloquejat
10:43:25   Professor revisa incidència
10:43:31   Desbloqueig autoritzat
10:43:32   Client desbloquejat
```

---

# 29. Desbloqueig massiu

El docent també podrà seleccionar diversos alumnes o tota la classe.

Opcions:

- desbloquejar alumne;
- desbloquejar seleccionats;
- desbloquejar tots.

Això és especialment útil si una incidència general provoca un bloqueig simultani.

---

# 30. Mode segur

Un cop carregada la URL, ContrOwl limitarà l’ordinador.

Entre altres elements haurà de restringir:

- Alt + Tab;
- tecla Windows;
- Windows + Tab;
- Windows + D;
- Windows + E;
- Windows + R;
- Ctrl + Esc;
- Alt + F4;
- Ctrl + Shift + Esc;
- Print Screen;
- canvi d’aplicació;
- obertura de nous navegadors;
- accés a aplicacions externes.

---

# 31. Navegació web restringida

La URL definida pel professor determinarà l’entorn autoritzat.

Per exemple:

```text
https://examens.insmollet.cat/prova3
```

ContrOwl podrà permetre:

```text
https://examens.insmollet.cat/*
```

i bloquejar qualsevol domini extern.

---

# 32. Llista blanca

El docent podrà afegir recursos autoritzats addicionals.

Per exemple:

```text
examens.insmollet.cat
geogebra.org
docs.google.com
```

Qualsevol altre domini serà bloquejat.

---

# 33. Control del porta-retalls

ContrOwl haurà de permetre configurar:

### Bloquejat

No es pot:

- copiar;
- tallar;
- enganxar.

### Intern

Es pot copiar i enganxar únicament dins de ContrOwl.

### Permès

Sense restriccions.

Per defecte, es recomana:

## Porta-retalls intern

---

# 34. Neteja inicial del porta-retalls

Abans de començar l’examen, ContrOwl podrà buidar el porta-retalls global.

Això evita que un alumne prepari prèviament contingut per enganxar-lo durant la prova.

---

# 35. Aplicacions externes

Durant una prova segura, ContrOwl haurà d’impedir o detectar l’ús de:

- Chrome;
- Edge;
- Firefox;
- ChatGPT;
- Teams;
- Discord;
- WhatsApp;
- Telegram;
- Word;
- OneNote;
- explorador de fitxers;
- PowerShell;
- CMD;
- altres aplicacions no autoritzades.

---

# 36. Monitorització del focus

ContrOwl detectarà qualsevol situació en què el seu entorn deixi de ser el focus principal.

Per exemple:

```text
10:42:14
Pèrdua de focus
Durada: 1,8 segons
```

Aquesta informació apareixerà al professor en temps real.

---

# 37. Estat dels alumnes

El panell del docent utilitzarà diferents estats.

### Verd

Funcionament normal.

### Groc

Incidència menor.

### Vermell

Alumne bloquejat o incidència greu.

### Gris

Sense connexió.

---

# 38. Vista de classe

La vista principal ha de prioritzar la supervisió visual.

Exemple:

```text
┌────────────┐ ┌────────────┐ ┌────────────┐
│ Anna       │ │ Marc       │ │ Júlia 🔴  │
│ [pantalla] │ │ [pantalla] │ │ [pantalla] │
└────────────┘ └────────────┘ └────────────┘

┌────────────┐ ┌────────────┐ ┌────────────┐
│ Pol        │ │ Biel       │ │ Carla      │
│ [pantalla] │ │ [pantalla] │ │ [pantalla] │
└────────────┘ └────────────┘ └────────────┘
```

Això ha de permetre que un professor pugui supervisar ràpidament una classe completa.

---

# 39. Privacitat per disseny

La monitorització s’activarà exclusivament mentre l’alumne estigui dins d’una sessió ContrOwl activa.

ContrOwl no haurà de permetre al docent veure la pantalla dels dispositius fora d’una sessió.

Quan acaba l’examen:

- deixa d’enviar-se pantalla;
- es desactiva el buffer;
- s’eliminen dades visuals temporals no associades a incidències.

---

# 40. Conservació de captures

S’haurà de diferenciar entre:

### Previsualització

Temporal i no emmagatzemada permanentment.

### Buffer

Temporal i eliminat contínuament.

### Captura explícita

Realitzada pel docent i emmagatzemada.

### Evidència d’incidència

Conservada quan hi ha un bloqueig o un esdeveniment que ho requereixi.

---

# 41. Pèrdua de connexió

Si un alumne perd Internet:

- el mode segur continuarà actiu;
- ContrOwl no es desbloquejarà;
- les incidències es guardaran localment;
- el buffer continuarà funcionant;
- quan es recuperi la connexió, el client es tornarà a sincronitzar.

---

# 42. Protecció davant el tancament del servidor

La caiguda del servidor no pot convertir-se en una via per abandonar ContrOwl.

El client ha de continuar executant les restriccions encara que perdi temporalment contacte amb el servidor.

---

# 43. Recuperació després d’un bloqueig

ContrOwl haurà de separar clarament:

## Bloqueig de seguretat

Provocat per una acció de l’alumne.

## Error tècnic

Provocat per:

- navegador;
- WebView;
- xarxa;
- servidor;
- client;
- sistema operatiu.

En tots dos casos el professor podrà revisar l’estat i recuperar el dispositiu remotament.

---

# 44. Seqüència completa d’ús

## Professor

1. Entra a ContrOwl Admin.
2. Crea sessió.
3. Introdueix URL.
4. ContrOwl genera un codi de 6 caràcters.
5. El professor comparteix el codi.

## Alumne

6. Obre ContrOwl.
7. Introdueix el codi.
8. Escriu nom i cognoms.
9. ContrOwl activa el mode segur.
10. S’obre automàticament la URL.

## Professor

11. Ve aparèixer l’alumne al panell.
12. Ve la seva pantalla.
13. Pot clicar-hi per ampliar.
14. Pot generar una captura.
15. Rep incidències en directe.

## Incidència

16. ContrOwl detecta una acció prohibida.
17. Pot bloquejar el dispositiu.
18. Conserva els segons previs.
19. Mostra al professor la causa exacta.
20. El professor revisa la seqüència.
21. Prem DESBLOQUEJAR.
22. L’alumne continua l’examen.

---

# 45. Requisit central de ContrOwl

L’experiència final ha de ser extremadament senzilla.

Per a l’alumne:

```text
OBRIR CONTROWL
       ↓
INTRODUIR CODI
       ↓
ESCRIURE NOM
       ↓
FER EXAMEN
```

Per al professor:

```text
CREAR SESSIÓ
       ↓
POSAR URL
       ↓
COMPARTIR CODI
       ↓
SUPERVISAR
```

Tota la complexitat de seguretat, monitorització, recuperació i comunicació ha de quedar amagada darrere d’aquest flux simple.


## Decisions de disseny clau

### Desbloqueig remot

El desbloqueig d’un dispositiu no dependrà mai d’una contrasenya introduïda físicament a l’ordinador de l’alumne.

El docent podrà desbloquejar qualsevol dispositiu directament des del seu panell de ContrOwl. L’ordre de desbloqueig serà enviada de manera remota, autenticada i vinculada a la sessió activa del professor.

Això evita que existeixi una contrasenya local que pugui ser vista, compartida o descoberta per l’alumnat i, alhora, simplifica molt la gestió d’una aula amb diversos dispositius.

### Buffer visual circular

ContrOwl no enregistrarà de manera contínua tota la pantalla de l’alumne durant l’examen.

En lloc d’això, el client mantindrà un **buffer visual circular temporal** amb els últims segons d’activitat de pantalla.

Aquest buffer s’anirà sobreescrivint constantment i només es conservarà quan es produeixi una incidència o bloqueig.

D’aquesta manera, el docent podrà revisar què estava passant immediatament abans de l’incident i identificar quina acció ha provocat el bloqueig, sense necessitat d’emmagatzemar tota la sessió.

Aquest sistema redueix:

- el consum de xarxa;
- l’espai d’emmagatzematge;
- la càrrega del servidor;
- els riscos associats a la privacitat.

Alhora, manté la informació realment útil per diagnosticar incidències.
