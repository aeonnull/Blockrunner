# BYGGPLAN: Blockheads Runner
Basfil: Code_runner_v27.html. Detta dokument är den enda sanningen. Du (byggmodellen) utför EN etapp per prompt, exakt som specad, sedan STOPP.

## ABSOLUTA REGLER
1. Utför ENDAST den etapp användaren pekar på. Inga förbättringar, ingen städning, inga egna idéer, ingen refaktorering.
2. Gör ENDAST de SÖK/ERSÄTT-byten som står i etappen. Om en SÖK-sträng inte hittas exakt: STOPPA och rapportera, gissa aldrig ett liknande ställe.
3. Läs inte igenom övrig kod. Du behöver inte förstå spelet, bara byta angivna rader.
4. Rör aldrig: startskärmens layout, hoppfysik (gravitation, hoppkraft, coyote, buffer), grafik, karaktärsbilder, variablerna hamHintShown, hamHintT, mtnHintShown, mtnHintT.
5. Efter varje etapp: git commit med etappnamnet, visa git diff, skriv rapport enligt RAPPORTFORMAT längst ner, sedan STOPP tills användaren sagt OK.
6. Oklart eller SÖK-sträng saknas? FRÅGA. Bygg aldrig på gissning.

## STATUS: REDAN KLART (rör ej)
- ETAPP 0 (drönare av + testläge nivåval): klar i v26.
- Tempohöjning nivå 1 (LVL_SPD 189 till 205): klar i v27.
- Basfilen v27 innehåller: DRONE_ENABLED=false som spärrar drönarens tre spawnvägar, testknapprad 1-11 på startskärmen, nivå 1-fart 205.

## ÅTERSTÅENDE ETAPPER

---

### ETAPP 1: Nivå 1 kortas till 4500
Exakt tre byten, inget annat.

Byte 1:
```
SÖK:    const LVL_LEN=[0,9000,15000,15000,18000,16000,20000,16000,18000,999999,23000,999999];
ERSÄTT: const LVL_LEN=[0,4500,15000,15000,18000,16000,20000,16000,18000,999999,23000,999999];
```

Byte 2:
```
SÖK:    const L1REWARDS=[ {at:3000,k:'coins'}, {at:4200,k:'heart'}, {at:5600,k:'rune'}, {at:7000,k:'heart'}, {at:8200,k:'rune'} ];
ERSÄTT: const L1REWARDS=[ {at:1500,k:'coins'}, {at:2100,k:'heart'}, {at:2800,k:'rune'}, {at:3500,k:'heart'}, {at:4100,k:'rune'} ];
```

Byte 3 (handmönstret kortas, gluggvärden orörda så inget hopp blir svårare):
```
SÖK:    const seq=[ [g1,420],[g1,450],                     // S1: tva laga, jamnt
      [g1,430],[g2,480],                               // S2: forsta dubbeln
      [g1,420],[g2,310],[g2,480],                      // S3: tva dubblar
      [g1,390],[ah,430],[g1,440],                      // S4: hogt block + duck
      [g2,350],[g1,310],[g2,500],                      // S5: rytmkombo
      [g1,430],[rp,520],[g1,430],[rp,520] ];           // S6: introducera repet - skjut igenom
ERSÄTT: const seq=[ [g1,420],[g1,450],                     // S1: tva laga, jamnt
      [g1,430],[g2,480],                               // S2: forsta dubbeln
      [g1,390],[ah,430],[g1,440],                      // S4: hogt block + duck
      [g1,430],[rp,520] ];                             // S6: introducera repet - skjut igenom
```

Testkrav (användaren testar): nivå 1 klarbar, alla fem belöningsmoment nås (coins, hjärta, runa, hjärta, runa), rep-hindret kommer nära slutet, inget hinder omöjligt.

---

### ETAPP 2: Nivå 2 kortas till 7500, rep från nivå 2, hint utan drönare
Exakt tre byten, inget annat. Kräver att ETAPP 1 är godkänd.

Byte 1:
```
SÖK:    const LVL_LEN=[0,4500,15000,15000,18000,16000,20000,16000,18000,999999,23000,999999];
ERSÄTT: const LVL_LEN=[0,4500,7500,15000,18000,16000,20000,16000,18000,999999,23000,999999];
```

Byte 2 (rep spawnas från nivå 2 och uppåt):
```
SÖK:    else if((level===5?lvlDist>1500:(level===6||level>=10)) && r<0.30){ type='rope'; }
ERSÄTT: else if(level>=2 && r<0.30){ type='rope'; }
```
OBS: raden ovanför med type='mtn' ska INTE röras.

Byte 3 (hint för nivå 2, drönartexten bort):
```
SÖK:    'NEW: DRONE \u2014 duck the beam \u00b7 find the \u2665'
ERSÄTT: 'NEW: ROPES \u2014 SHOOT \u2726 \u00b7 find the \u2665'
```

Testkrav: nivå 2 klarbar, rep dyker upp och går att skjuta, ingen drönartext syns, hjärtat dyker upp ungefär halvvägs.

---

### ETAPP 3: Nivå 3 kortas till 7500, fallande block säkra
Exakt tre byten, inget annat. Kräver att ETAPP 2 är godkänd.

Byte 1:
```
SÖK:    const LVL_LEN=[0,4500,7500,15000,18000,16000,20000,16000,18000,999999,23000,999999];
ERSÄTT: const LVL_LEN=[0,4500,7500,7500,18000,16000,20000,16000,18000,999999,23000,999999];
```

Byte 2 (varningstid före nedslag höjs från 0,65 s till 1,05 s):
```
SÖK:    if(fb.state==='warn'){ if(fb.t>0.65){fb.state='fall';} }
ERSÄTT: if(fb.state==='warn'){ if(fb.t>1.05){fb.state='fall';} }
```

Byte 3 (större skyddszon så hinder inte spawnas i landningszonen, 220 px till 300 px):
```
SÖK:    if(fallers.some(fb=>fb.state!=='landed' && Math.abs((fb.landX!=null?fb.landX:fb.x)-spawnX)<220)) gap+=200;   // hall undan fran en fallande kloss landningszon
ERSÄTT: if(fallers.some(fb=>fb.state!=='landed' && Math.abs((fb.landX!=null?fb.landX:fb.x)-spawnX)<300)) gap+=260;   // hall undan fran en fallande kloss landningszon
```

Testkrav: nivå 3 klarbar flera gånger i rad, varningsmarkering alltid synlig minst 1 sekund före nedslag, inga dödsfall som inte gick att undvika.

---

### ETAPP 4: Nivå 4 kortas till 9000
Exakt ett byte, inget annat. Kräver att ETAPP 3 är godkänd.

```
SÖK:    const LVL_LEN=[0,4500,7500,7500,18000,16000,20000,16000,18000,999999,23000,999999];
ERSÄTT: const LVL_LEN=[0,4500,7500,7500,9000,16000,20000,16000,18000,999999,23000,999999];
```

Testkrav: nivå 4 klarbar, kombinationerna fungerar utan drönare och med rep, inga omöjliga kombinationer. Om testet visar en omöjlig kombination: rapportera exakt vad som hände (hindertyper och ungefärlig sträcka), föreslå INGET, vänta på nytt byte från arkitekten.

---

### ETAPP 5: Gyllene nyckel (extraliv). Byggs först när 1 till 4 är godkända.
Detta är ny kod. Håll dig till exakt denna spec, max cirka 40 nya rader totalt.

Spec:
- Ny konstant vid övriga konstanter: `const KEY_LEVELS=[2,5,8,11], KEY_MAX=3;`
- Ny variabel i den stora let-raden: `keys=0, keySpawned=false, keyPickup=null` (keys ska INTE nollställas i reset vid död, den är beständig under sessionen).
- I reset: `keySpawned=false; keyPickup=null;`
- Spawn: i samma uppdateringsfunktion som STREET HEART-blocket (sök på kommentaren `---------- STREET HEART ----------` och lägg nytt block direkt efter): om `KEY_LEVELS.includes(level) && !keySpawned && keys<KEY_MAX && zone==='street' && lvlDist>LVL_LEN[Math.min(level,10)]*0.35` sätt `keySpawned=true` och skapa `keyPickup={x:W+80,y:GROUND_Y-232,bob:0}`.
- Uppdatering/plock: flytta keyPickup med `keyPickup.x-=speed*dt`, bob som hjärtat, kollision mot spelaren med samma rektangelkontroll som hjärtan använder; vid plock: `keys++`, ljudet playCoin om funktionen finns, keyPickup=null.
- Rit: gyllene rektangel 18x30 px med bob, samma stil som hjärtritningen, färg '#f7c948'.
- HUD: under hjärtraden, om keys>0 skriv `'\u26b7 '+keys` i färg '#f7c948', font '700 13px monospace'.
- Död med nyckel: hitta funktionen som körs vid död (sök `function die(`). Där spelaren annars går till dead-läget: om `keys>0 && !demo` istället `keys--` och starta om SAMMA nivå från början genom att sätta startLevel till aktuell nivå tillfälligt, anropa start(), och återställ startLevel till vad spelaren valt. Ingen ändring av poäng-/rekordlogik.
- Död utan nyckel: exakt som idag.

Testkrav: nyckel dyker upp på nivå 2, syns i HUD, död med nyckel startar om nivå 2 från början och HUD visar en nyckel mindre, död utan nyckel funkar som förut, max 3 i lager.

---

## SENARE (bygg INTE, finns bara så du vet att de är medvetet uteslutna)
Nivå 5 till 11, drönar-ombyggnad, vallgrav/brygga, slottsmur, surf-plattformar, himmelsfärg per nivå, distrikt-teman, nivå 11-text, poängsystem.

## RAPPORTFORMAT (efter varje etapp)
- Etapp och nytt versionsnummer
- Utförda byten: lista, med radnummer
- Regelkontroll: bekräfta regel 1 till 4 punkt för punkt
- git diff --stat
- Öppna frågor (helst: inga)
