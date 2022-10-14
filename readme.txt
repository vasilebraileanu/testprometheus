Articolul general este la:
https://codersociety.com/blog/articles/nodejs-application-monitoring-with-prometheus-and-grafana

Ca sa puteti rula aveti nevoie sa instalati programul node. Pentru Windows il puteti lua de aici (pentru 64 bit)
https://nodejs.org/dist/v16.18.0/node-v16.18.0-x64.msi

Aveti nevoie de Docker (pentru Windows)
https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe?utm_source=docker&utm_medium=webreferral&utm_campaign=dd-smartbutton&utm_location=header

Probabil va trebui sa modificati portul pentru serverul node. Eu am pus 8777(vedeti fisierul "server.js").

Teoretic serverul node trebuie pornit inainte de prometheus.
Din directorul curent executati comanda de descarcare fisisere necesare (va descarca fisiere in directorul node_modules)
$ npm install

Pornire server node (din directorul curent)
$ node server.js

pentru rularea prometheus, trebuie sa accesati fisierul
prometheus.yml din calculatorul gazda (din afara imaginii docker). In mod normal, ar trebui sa modificati
fisierul direct in docker dar daca faceti asta si stergeti imaginea pierdeti si aceste modificari.
Ceea ce se face deobicei pentru testare este sa folositi imaginea docker pentru Prometheus dar setarile
pentru prometheus sa le puneti in afara Docker. Mai jos vedeti unde am eu fisierul (trebuie sa adaugati
tot directorul iar in Windows trebuie sa puneti "/" in loc de "\" ca delimitator de cale.
In cazul meu a fost "c:/work/bmw/new/testprometheus"

docker run --rm -p 9090:9090 \
  -v c:/work/bmw/new/testprometheus/prometheus.yml:/etc/prometheus/prometheus.yml \
  prom/prometheus



Daca nu puteti opri procesul /prom/prometheus (cu Ctrl+C) deschideti o noua consola, apoi(ignorati semnul $ din fata comenzii):
$ docker ps
Observati in lista coloana NAMES pentru IMAGE=prom/prometheus (de ex. "friendly_euclid")

$ docker stop friendly_euclid

Daca totul a functionat, ar trebui ca prom/prometheus sa dispara din lista proceselor din docker

$ docker ps

Accesare prometheus
-------------------
http://localhost:9090
In dreapta casetei "Expression" se afla un fel de glob, cu mesajul "Open metrics explorer"
Alegeti "scrape_duration_seconds" (cat a durat sa culegem datele de la serverul node) dupa care apasam butonul "Execute".
Puteti apasa de mai multe ori butonul execute (dar dupa 5 secunde, bineinteles). Puteti apasa si butonul "Graph" de langa Table.
In fisierul server.js o sa gasiti "metrica" in linia 18: "http_request_duration_seconds"
Ca sa o folositi, introduceti la "Expression"
http_request_duration_seconds_bucket
Bucket fiind o chestie din statistica, care spune unde punem anumite procentaje (cum "desenam" graf-ul statistic).
Din pacate se pare ca request-ul "http_request_duration_seconds_bucket" nu functioneaza, va trebui citita documentatia.


Uzual ne intereseaza alti parametri de ex. durata cererilor HTTP (HTTP GET)
Pentru testare putem folosi un browser si un URL (vezi fisierul "server.js")
http://localhost:8777/testvasile
dupa care apasam de mai multe ori Enter in URL din browser ori apasam butonul "refresh page" din browser.


Daca modificati ceva, nu uitati sa opriti aplicatia node (Ctrl+C) si prometheus (din alta consola, "docker stop ...")
apoi le reporniti in aceeasi ordine



