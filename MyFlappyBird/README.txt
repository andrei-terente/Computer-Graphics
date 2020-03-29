TERENTE ANDREI-ALEXANDRU
335 CA 2019
EGC - Tema 1 - My Flappy Bird
README


---------------------------------------------------------------------------------------------------
0. Mentiuni prealabile
	- in continuarea README-ului voi face referire la dreptunghiurile de care pasarea
	se poate lovi prin termenul de "pipes" / "tevi", deoarece am inceput implementarea cu gandul
	catre jocul originial, unde acestea (dreptunghiurile) sunt reprezentate prin tevi.
	Aceeasi denumire a fost utilizata si in cod.

	- in scopul facilitatii de testare, am inclus urmatoarele (initial oprite la rulare):
		> Pause/Unpause - tasta P
		> Debug Mode 	- tasta D - afisare date in consola (pozitii, viteza etc.) 
					    + desenare hitbox pe ecran.
		> AutoPlay	- tasta B - pasarea este controlata in mod automat
		> NoClip	- tasta C - dezactiveaza evaluarea coliziunilor

	- dupa o coliziune, jocul este repornit in modul Pauza. 
---------------------------------------------------------------------------------------------------
1. Structura / Organizare
	- baza implementarii o constituie scheletul laboratorului 3;

	- cu exceptia a 2 auxiliare suplimentare (pt. crearea cercului, respectiv a trunghiului)
	in fisierul Object2D, fisierele Transform2D si Object2D au continutul neschimbat

	- logica a fost impartita astfel (in proportie majoritara, intrucat anumite portiuni
	sunt organizate amestecat):
		> PipeManager.cpp 	- gestionarea dreptunghiurilor (a tevilor)
		> Bird.cpp 		- gestionarea pasarii si evaluarea coliziunilor
		> Tema1.cpp		- driver program

	- configurarea valorilor utilizate in program (dimensiuni scena / pasare / tevi etc.)
	se poate realiza din fisierul Constants.h
---------------------------------------------------------------------------------------------------
2. Scena
	- scena este abstractizata intr-un LogicSpace, ale carui dimensiuni sunt raportate la
	dimensiunea "primitiva" (GATE_OFFSET, dimensiunea portii dintre 2 tevi) si la un
	aspect ratio de 16:9
	
	- dimensiunile obiectelor sunt stabilite in domeniul spatiului logic.	

	- transformarea spatiului logic in spatiu de vizionare se realizeaza printr-o transformare
	neuniforma (fapt ce va determina o deformare a obiectelor desenate in urma utilizarii unei
	rezolutii cu un alt aspect ratio).

---------------------------------------------------------------------------------------------------
3. Pipe-urile
	- in scopul utilizarii eficiente a resurselor, toate pipe-urile sunt desenate folosind
	acelasi Mesh, incarcat o singura data in memorie la inceperea jocului.
	
	- in aceasta ordine a ideilor, este suficient sa se cunoasca anumite informatii despre tevi
	pentru desenarea lor. Aceste informatii sunt structurate in PipeData, pe baza careia se
	deseneaza atat pipe-ul de jos, cat si cel de sus.

	- tevile sunt generate la x = SPACE_WIDTH

	- generarea primei tevi se realizeaza la un delay de cateva frame-uri (GEN_DELAY)
	dupa pornirea jocului. Generarea urmatoarelor tevi se realizeaza atunci cand
	distanta dintre cea mai din dreapta teava si limita din dreapta a spatiului
	depaseste o anumita limita. (pipeDistance, initial PIPE_START_D).
	
	- procedeul de generare consta in alegerea aleatoare a inatlimii la care 
	se regaseste poarta intr-un interval stabilit, apoi calcularea dimensiunilor
	tevilor ce incadreaza poarta. Intervalul implicit nu determina situatii imposibile
	de trecut peste (testat folosind AutoPlay).

	- dupa fiecare frame tevile sunt miscate catre stanga ecranului pentru a crea iluzia
	deplasarii catre dreapta

	- cand o teava paraseste spatiul de desenare, aceasta este stearsa din lista tevilor
	si drept urmare nu va mai fi desenata in contiuare
---------------------------------------------------------------------------------------------------
4. Pasarea
	- pasarea este reprezentata prin urmatoarele forme geometrice alipite (in ordine):
	dreptunghi/patrat + cerc + triunghi echilateral.
	
	- pozitia pasarii pe ecran este modificata prin translatii si rotatii aplicate
	formelor ce o compun.

	- in fiecare frame, se verifica daca s-a primit input pentru ca aceasta sa dea din aripi, se
	calculeaza viteza verticala a pasarii si se modifica pozitia corespunzator
	
	- inclinarea pasarii se realizeaza prin rotatia dreptunghiului si a triunghiului in jurul
	cercului din compunerea pasarii. Unghiul de rotatie este determinat in functie de viteza
	si limitat intr-un interval stabilit (pentru a mentine animatiile consistente).

---------------------------------------------------------------------------------------------------
5. Coliziuni
	- pentru evaluarea coliziunilor se utilizeaza un procedeu de aproximare a modelului pasarii
	ce consta in circumscriearea formelor componente ale pasarii cu un cerc (mai putin a cercului,
	pentru care hitbox-ul coincide cu modelul). Acest procedeu are ca scop facilitarea procesului
	efectiv de evaluare a intersectiilor dintre poligoane.

	- pentru fiecare hitbox, se verifica intersectia dintre el si cele doua dreptunghiuri ce reprezinta
	perechea de tevi cea mai apropiata de pasare[1].

	- de asemenea, se verifica si daca pasarea se afla in spatiul de desenare (zburat peste limita
	superioara, cazut sub limita inferioara)

	- in urma unei coliziuni, jocul este pus pe pauza si este afisat scorul final.

---------------------------------------------------------------------------------------------------
Referinte:
[1]: https://learnopengl.com/In-Practice/2D-Game/Collisions/Collision-detection
