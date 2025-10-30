---
title: "Voice Chains"
summary: "Mobilní aplikace k posílání hlasových zpráv a vytváření řetězů hlasových zpráv" 
date: "Jan 19 2025"
draft: false
tags:
- React
- Expo
- Typescript
- Tailwind
- Python
- AWS
demoUrl: https://astro-sphere-demo.vercel.app
repoUrl: https://github.com/markhorn-dev/astro-sphere
---
### Použité nástroje: 
- Expo, React Native, Typescript, AWS S3 buckets, Python, FFMPEG, Websockets

### Overview
Frontend: mobilní aplikace napsaná v Typescriptu, využívající React Native a Expo.  
Backend: hostovaný python backend  
Storage: AWS S3 Bucket

### O projektu

Měl jsem nápad na aplikaci, ve které si budou moci náhodní lidé posílat hlasové zprávy a zároveň vytvářet řetězce těchto hlasových zpráv.
Nápad se mi tak zalíbil, že jsem se rozhodl ho "stvořit.

Jako platformu jsem vybral mobilní telefon, protože mi to přišla jako intuitivně nejlepší platforma pro tento typ projektu - každý telefon má mikrofon a reproduktor.  
Na frontend aplikaci jsem použil [Expo](https://expo.dev/) protože jsem s ním již měl pozitivní zkušenosti.  
Frontend aplikace je napsaná v TypeScriptu a backend v Pythonu.

Nemám rád aplikace, které vyžadují přihlašování, místo přihlašování tedy používám náhodná UUID, která jsou uživateli přidělena po otevření aplikace.  
1. Uživatel nahraje hlasovou zprávu, backend zkontroluje formát, očistí zprávu o metadata a uloží do S3 bucketu.  
2. Backend potom emituje websocket notifikaci náhodně vybranému UUID připojeného uživatele a ten obdrží tuto zprávu.  

<img src="/new-message.png" alt="Project Image" width="250" />




### Hlasové chains
Koncept je jednoduchý, každý může vytvořit řetězec hlasových zpráv. Ostatní do něj mohou přispět svojí zprávou.  
Vzniká tak konverzace mezi náhodnými lidmi.

<img src="/chains1.png" alt="Project Image" width="250" />