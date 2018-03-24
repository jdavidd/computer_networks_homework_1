# computer_networks_homework_1
Cerinte tema (1 p. oficiu)
1. Creati o noua imagine (3p.)

La acest pas veti crea o noua imagine care va avea tag-ul tema1. Cel mai simplu e sa refolositi fisierul Dockerfile pe care sa-l modificati pentru a instala tcpdump (vezi sectiunea cu tcpdump din laborator 1) apoi selectati userul default ca fiind root, nu rtuser. Pentru asta, trebuie sa adaugati la sfarsit de tot linia USER root, in felul acest toate containerele vor rula default cu root. Dati build la imagine folosind tag-ul tema1. Comanda de build este explicata si in primul readme.
2. Creati o retea tnet2 si definiti patru servicii rt1, rt2, rt3 si rt4 (3p.)

Modificati docker-compose.yml si creati reteaua tnet2 cu urmatorul gateway: 1.2.3.4. Configurati un subnet potrivit (care sa mearga) pentru acest gateway. Atentie cand modificati fisierul sa va asigurati ca formatarea este corecta si ca nu aveti caractere '\t' tab inserate. Creati 4 containere/servicii pe aceasta retea cu numele rt1, rt2, rt3 si rt4 care sa ruleze imaginea tema1 construita la pasul anterior. Pentru configurarea unui serviciu, pe langa image, network, volumes, tty, puteti utiliza:

    command: care sa defineasca o comanda ce se va executa cand porneste containerul.
    sysctls: prin care sa se specifice configuratii de kernel de linux.
    depends_on: prin care sa se specifice o ordine de lansare a containerelor. Este posibil ca un container (rt2) sa depinda de un alt container (rt1) pentru ca rt2 sa porneasca cu succes.

Adaugati command si depends_on astfel incat urmatoarele comenzi sa se execute cu succes:

    rt1 sa ruleze tcpdump -nSt icmp (scaneaza doar pachete de tipul ICMP/ping)
    rt2 sa ruleze ping -s 4000 rt1 (rt2 trimite pachete ICMP de dimensiune 4000 octeti lui rt1)
    rt3 sa ruleze ping rt1 (rt3 trimite pachete ICMP de dimensiune 64 octeti lui rt1)
    rt4 sa ruleze ping -c 1 rt3 (rt4 trimite un pachet ICMP lui rt3)

In plus, modificati containerul rt3 pentru a bloca ping-urile prin adaugarea unei configurari sysclts. In laborator1, sectiunea cu ping, aveti un astfel de exemplu care defineste sysctls pentru a bloca ping-urile.
3. Creati reteaua tnet1 si definiti un serviciu tn1 (3p.)

Tot in fisierul docker-compose.yml adaugati o retea noua cu numele tnet1. Reteaua trebuie sa aiba definit un subnet restrictiv (prin notatia cu slash) care sa permita lansarea unui singur container. Folositi adrese cat mai diverse pentru gateway si subnet, incercati ca doua rezolvari ale temei sa nu aiba acealsi IP. Definiti si un nou serviciu cu numele tn1 care sa utilizeze imaginea baseimage si care sa fie conectat doar la reteaua tnet1. Acest serviciu trebuie sa execute: "bash -c 'echo \"- interfetele de retea:\" && ip addr && echo \"- gateway:\" && ip route show'" care deschide un nou shell (bash) si afiseaza configuratiile de retea si gateway-ul folosind comanda ip.

Comanda anterioara este echivalenta cu urmatorul script de bash, unde && este utilizat pentru a executa comenzile una dupa alta:

echo "- interfetele de retea:"
ip addr
echo "- gateway:"
ip route show

Porniti toate containerele

Folosind docker-compose up -d porniti toate containerele pe care le-ati definit in fisierul docker-compose.yml. Observati ca daca porniti toate containerele si rulati docker ps, serviciul tn1 nu apare in lista. Asta pentru ca acele comenzi de shell si-au terminat executia iar container-ul s-a oprit. Incercati sa rulati docker-compose ps pentru a vedea si care a fost exit statusul acelor comenzi. Vedem ca pentru containerul tn1 avem Exit 0, adica comenzile au fost executate cu succes. De asemenea se poate vedea ca dupa un timp si containerul rt4 apare ca fiind oprit cu statusul Exit 1 ceea ce inseamna ca acel ping cu un singur pachet catre container-ul rt3 a esuat si a returnat statusul 1.

Pentru a testa comenzile sau pentru a vedea log-urile si output-ul containerelor, utilizati:

docker-compose logs tn1
docker-compose logs rt1
docker-compose logs rt2
docker-compose logs rt3 rt4

Sau puteti redirecta continutul log-urilor in fisiere separate pe care le puteti deschide ulterior:

docker-compose logs rt1 > logs_rt1.log
docker-compose logs rt2 > logs_rt2.log
docker-compose logs rt3 > logs_rt3.log
docker-compose logs rt4 > logs_rt4.log
docker-compose logs tn1 > logs_tn1.log
