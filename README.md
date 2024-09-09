
# Active Directory Lab

Ovaj rad detaljno će prikazati korake postavljanja vlastitog lokalnog active directory okruženja te ujedno i testiranja njegove sigurnosti.

## Opis projekta

Ovaj projekt sam započeo u sklopu izrade završnog rada u srednjoj školi, no odlučio sam ga nastaviti nadograđivati kao vlastito okruženje za konfiguraciju i demonstraciju raznih obrana i napada u sklopu active directory okruženja.

Ovaj rad prikazat će detaljan opis postavljanja Active Directory sustava, što uključuje konfiguraciju računala, korisnika, grupa i drugih elemenata. Prilikom konfiguracije sustava, neke značajke bit će namjerno neispravno konfigurirane kako bi lakše i realističnije mogli simulirati stvarni sustav i sigurnosne propuste koji se često događaju korisnicima. Nakon uspostave Active Directory okruženja, rad će se baviti testiranjem njegove sigurnosti. To uključuje eksploataciju tvorničkih postavki, korisničkih postavki i loših sigurnosnih navika

## Postavljanje okruženja

### Potrebne aplikacije i datoteke

- [VMWare Workstation Player ](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion)
- [Windows Server 2022 ISO](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022)
- [Windows 10 ISO](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise)
- [Kali Linux](https://www.kali.org/get-kali/#kali-virtual-machines)

### Instalacija ISO datoteka

Nakon preuzimanja odgovarajućih ISO datoteka, potrebno ih je instalirati u virtualizacijsku platformu. 
1. Otvoriti VMware Workstation Player te izabrati opciju „_Create a New Virtual Machine_“ te zatim odabrati preuzetu ISO datoteku (Prvo ćemo instalirati Windows Server 2022)
2. Promijeniti verziju sustava u Windows Server 2022 Standard (*Za korisnička računala potrebno je promijeniti verziju u Windows 10 Home ili Pro*) 
3. Promijeniti veličinu diska prema performansama vlastitog računala (*u svrhu ovog rada, veličinu diska dovoljno je postaviti na 60 GB*) 
4. Na sljedećem izborniku želimo isključiti „_Power on this virutal machine after creation_“ opciju. 
Sve ostale nespomenute opcije nije potrebno dodatno mijenjati te je dovoljno ostaviti tvornički odabrane postavke.

![image](https://github.com/user-attachments/assets/e0e74921-b4f0-413d-925f-4a77b5132108)

Virtualno računalo je uspješno instalirano, no potrebno je odabrati opciju „Edit virtual machine settings“ te izbrisati „_Floppy_“ ulaz. Završne postavke trebale bi izgledati približno kao na sljedećoj slici. Važno je napomenuti da svako virtualno računalo treba koristiti NAT mrežni adapter kako bi ispravno mogli komunicirati.

![image](https://github.com/user-attachments/assets/7b97e474-e9c9-4bdb-99be-0f6dfb237036)

Prikazane korake potrebno je učini još dva puta, za svako od korisničkih računala u mreži. Jedina razlika prilikom instalacije je odabrati Windows 10 ISO za operacijski sustav umjesto Windows Server 2022.

### Instalacija Windows Server operacijskog sustava

Nakon instalacije računala potrebno je instalirati operacijske sustave. Potrebno je pokrenuti Windows Server 2022 virtualno računalo te pratiti sljedeće korake. 
1. Proizvoljno odabiremo jezik instalacijskog sustava. 
2. Pritiskom na gumb „_Install now_“ pokrećemo sam proces instalacije. 
3. Prilikom odabira verzije operacijskog sustava, potrebno je odabrati „Windows Server 2022 Standard Evaluation (Desktop Experience)“. 
4. Nakon prihvaćanja Microsoft licence odabiremo opciju „_Custom: Install Microsoft Server Operating System only (advanced)_“. Sljedeći izbornik omogućit će nam samostalno dodjeljivanje particija. Potrebno je izabrati prikazanu particiju te promijeniti veličinu na njezinu maksimalnu vrijednost. U ovom slučaju to je 61440 MB.

![image](https://github.com/user-attachments/assets/48c7d35f-e629-4737-b956-f0250e45a570)

5. Zatim pritisnemo na gumb „_Apply now_“ i trebali bismo vidjeti sveukupno 3 stvorene particije.

![image](https://github.com/user-attachments/assets/4e58ee2a-fc12-47ab-8982-438e52ee7be7)

Nakon toga, potrebno je odabrati zaporku za administratorski račun. Kako bi se naglasila važnost snažnih i kompliciranih zaporka, u ovom primjeru koristit ćemo slabu i jednostavnu zaporku „Password1“.

### Instalacija Windows 10 operacijskog sustava

Osim Windows Server operacijskog sustava na upravitelju domene, potrebno je instalirati Windows 10 sustav na dva korisnička računala. **Svi prikazani koraci jednaki su sve do postavljanja administratorske zaporke, umjesto čega se na korisničkim računalima pokreće konfiguracija korisnika**. 
1. Potrebno je odabrati regiju i jezik koji želimo koristiti te zatim odabrati „_Domain join instead_“ opciju koja će nam kasnije omogućiti dodavanje računala u mrežu.
2. Postaviti račun pomoću kojeg će se pristupati računalu. U ovom primjeru, za ime korisnika koristit ćemo „Pero Perić“, a za njegovu zaporku „Password1“. 
3. Proizvoljno odabrati sigurnosna pitanja. 

Iste korake pratimo i za drugo računalo na kojem će se registrirati korisnik „Toni Školić“ sa zaporkom „MYpassword123#“.

### Dodatno postavljanje računala

Nakon instalacije operacijskog sustava, zbog jednostavnost korištenja potrebno je instalirati VMware Tools. To možemo učiniti:
1. Pritiskom na gumb „_Player_“ u navigacijskoj traci VMware programa te pritiskom na gumb „_Manage_“ i „_Install VMware Tools_“. 
2. Nakon toga otvoriti novu DVD particiju te otvoriti aplikaciju „_setup_“. Prilikom instalacije, jedina opcija koju je potrebno mijenjati je postaviti instalacijski tip na „_Complete_“. (*Ukoliko program traži ponovno pokretanje računala, potrebno je pritisnuti opciju da se to izvrši kasnije*) 
3. Nakon instalacije VMware Tools paketa, u Windows tražilicu potrebno je upisati „Prikaz naziva PC-ja“ te promijeniti naziv računala da bi ga jednostavnije mogli prepoznati prilikom daljnjeg postavljanja domene - U ovom primjeru, naziv upravitelja domene promijenjen je u „SKOLA-DC“, dok su nazivi korisničkih računala „Workstation1“ i „Workstation2“. 
4. Nakon toga možemo ponovno pokrenuti računalo. 
5. **Ove korake potrebno je provesti na sva računala u mreži.**

### Postavljanje domene

Nakon uspješnih instalacija i postavljanja svih računala, za konfiguraciju Active Directory sustava neophodno je stvoriti domenu putem koje će računala moći komunicirati i razmjenjivati podatke. 
Prilikom pokretanja upravitelja domene, automatski će se otvoriti početna stranica upravitelja servera. 
1. Za uspostavu domene potrebno je pritisnuti gumb „_Manage_“ te odabrati opciju „_Add Roles and Features_“. To će pokrenuti čarobnjak za stvaranje domene u kojem je jedino potrebno dodati „_Active Directory Domain Services_“ uloge servera.

![image](https://github.com/user-attachments/assets/961cbb04-29f5-41de-8a4c-1200a76bfbc3)

2. Nakon instalacije, na početnom zaslonu automatski će biti ponuđena opcija postavljanja ovog računala kao upravitelja domene, što želimo potvrditi.

![image](https://github.com/user-attachments/assets/69617c8c-01d0-4ff2-9755-1601ece93a81)

3. Zatim će se otvoriti čarobnjak za konfiguraciju domene. Na prvom zaslonu želimo odabrati opciju „_Add a new forest_“ te dodijeliti joj ime „SKOLA.local“.

![image](https://github.com/user-attachments/assets/dd3f2359-a24d-449b-9b3b-e6825acd31d5)

4. Zatim je potrebno stvoriti novu zaporku, no u ovom slučaju koristimo „Password1“ zaporku administratora kako bi naglasak bio na nesigurnom upravljanju zaporkama. 
5. Sljedeća postavka koju je potrebno provjeriti je NetBIOS naziv domene. Ovdje bi sustav trebao prepoznati naziv logičke particije (engl. forest) bez nastavka „.local“. U ovom primjeru to glasi „SKOLA“.

![image](https://github.com/user-attachments/assets/cc96b754-fc3e-4408-866b-8cccb902b43e)

6. Potom, sustav će provjeriti sve preduvjete za ispravan rad domene te tražiti korisnika da provede instalaciju. Nakon toga bit će potrebno ponovno pokrenuti računalo.

### Postavljanje korisnika i grupa

Nakon uspostave domene, potrebno je stvoriti korisnike i njihove dozvole koje će koristiti u domeni. 
1. Otvaranjem gumba „_Tools_“ u navigacijskoj traci windows servera i pritiskom na „_Active Directory Users and Computers_“ opciju, otvara nam se upravitelj korisnika i računala u mreži. 
2. Potom je potrebno pritisnuti desni klik miša na organizacijsku jedinicu domene te izabrati opciju „_New > Organisational Unit_“.

![image](https://github.com/user-attachments/assets/9ff52651-71a7-4917-a4cc-eabcf9538bbb)

3. Zatim stvoriti organizacijsku jedinici naziva „_Groups_“ te u nju premjestiti sve podatke iz mape „_Users_“, osim „_Administrator_“ i „_Guest_“.

![image](https://github.com/user-attachments/assets/06abd7f1-0b78-4790-a4ee-634e36e80346)

4. Potom, pritiskom desnog klika miša, na praznu površinu ispod „_Administrator_“ i „_Guest_“ ulaza u mapi „_Users_“, otvara se novi izbornik na kojem je potrebno stvoriti novog korisnika odabirom „_New > User_“ opcije. 
5. Nakon toga potrebno je unijeti sve podatke za prethodno definirane korisnike. U ovom slučaju, prvo ćemo konfigurirati korisnika „Pero Perić“.

![image](https://github.com/user-attachments/assets/d9e650d4-b3a0-466b-b47e-772995d84a65)

![image](https://github.com/user-attachments/assets/42d9d955-888f-4497-a58a-59c87660c87b)

6. Zatim, tog istog korisnika želimo kopirati, no promijeniti podatke da odgovaraju drugom korisničkom računu „Toni Školić“.

![image](https://github.com/user-attachments/assets/e6faa98b-ad0d-45e0-a1c9-92cea1247bfb)

7. Potom je potrebno kopirati administratorski račun te stvoriti prije spomenuti „SQLService“ uslužni račun prateći prošle korake.

![image](https://github.com/user-attachments/assets/debde440-1b98-4868-957d-0f102c8efc84)

8. Kako bismo „SQLService“ račun konfigurirali kao SVC uslužni račun, potrebno je pokrenuti naredbu prikazanu u sljedećoj slici -> `setspn -a SKOLA-DC/SQLService.SKOLA.local:60111 SKOLA\SQLService`

![image](https://github.com/user-attachments/assets/abd2d6b5-181f-40e3-8488-672e8c877f12)

### Dodavanje računala u domenu

Kako bismo dodali računala u konfiguriranu domenu, potrebno je saznati IP adresu upravitelja domene. 
1. To možemo učiniti odlaskom u naredbeni redak (_engl. command prompt_)  i upisivanjem naredbe „ipconfig“. 
2. Potom, na korisničkim računalima je potrebno otvoriti postavke mreže i interneta te pritisnuti opciju „_Change Adapter Settings_“. 
3. Zatim na Ethernet prilagodniku, desnim klikom miša, otvoriti dodatna svojstva te otvoriti „_Internet Protocol Version 4 (TCP/IP)_“ opciju. 
4. Potrebno odabrati „_Use the following DNS server addresses_“ opciju te upisati IP adresu upravitelja domene.

![image](https://github.com/user-attachments/assets/a56e5b4b-11f8-4866-9c4a-a67f9d3d92cd)

Nakon postavljanja upravitelja domene kao DNS servera, potrebno je pridružiti računala domeni. 
1. To radimo odlaskom na „_Access work or school_“ postavku te pritiskom na gumb „_Connect_“. 
2. Potom pritisnuti „_Join this device to a local Active Directory domain_“ i upisati naziv konfigurirane domene zajedno s nastavkom „.local“.

![image](https://github.com/user-attachments/assets/74fe860d-e89c-4a3d-892e-43d324e4db72)

- Ukoliko se nakon toga otvori izbornik koji traži korisničko ime i zaporku, računalo ispravno komunicira sa ostatkom domene. 
- **U slučaju suprotnog, računalo je neispravno konfigurirano te je potrebno dodatno provjeriti sve prikazane korake kako bi se pronašla greška.** 

Ukoliko sve radi ispravno, potrebno se prijaviti pomoću administratorskog računa. U ovom slučaju, korisničko ime je „Administrator“, a zaporka „Password1“. Potom, na sljedećem izborniku preskočimo postavljanje dodatnog računa te ponovno pokrenemo računalo. **Prikazane korake, potrebno je provesti i za drugo korisničko računalo.** 

Ukoliko sve ispravno radi, na upravitelju domene, pod stavkom „_Active Directory Users and Computers_“ možemo pronaći naziv računala prijavljenih u domenu.

![image](https://github.com/user-attachments/assets/b59827a8-ac5e-47a8-8fd0-b3817e24f3a5)

### Dodatna konfiguracija korisnika

Kako bismo dodatno naglasili važnost ispravne konfiguracije korisnika, postavit ćemo oba korisnika na poziciju lokalnog administratora na svojem računalu. **Važno je naglasiti kako je za ovaj korak potrebno biti prijavljen pomoću administratorskog računa u domeni na korisničkom računalu**. 

1. Kako bismo konfigurirali korisnika kao lokalnog administratora, potrebno je otvoriti „_Computer Management_“ postavke na korisničkom računalu. 
2. Zatim pritisnuti opcije „_Local Users and Groups > Groups > Administrators_“.

![image](https://github.com/user-attachments/assets/6d763033-9da7-42b8-8d6f-ac03851cade7)

3. Potom, u polje „_Enter the object names to select_“ je potrebno unijeti naziv korisnika kojeg želimo konfigurirati kao lokalnog administratora. U ovom slučaju oba korisnika će biti konfigurirani kao lokalni administratori. 

**Postupke je potrebno ponoviti i na drugom korisničkom računalnu.**

![image](https://github.com/user-attachments/assets/7ce625ae-0066-4436-99f5-89505c443e0a)

![image](https://github.com/user-attachments/assets/d718128e-e717-47ec-94b4-48e7abb3917e)

4. Na kraju, potrebno je otići na „_Network_“ karticu u pretraživaču za datoteke te pritisnuti desni klik miša na poruku na vrhu prozora. 
5. Potom, odabrati opciju „_Turn on network discovery and file sharing_“.

![image](https://github.com/user-attachments/assets/b1afaf4e-bc05-4235-b873-11ae235a880c)

## Sigurnosna analiza sustava

### Konfiguracija napadačkog računala

Kako bi se mogle izvesti tehnike prikazane u sljedećim poglavljima, potrebno je konfigurirati računalo koje sadrži Kali Linux operacijski sustav. Kali Linux napravljen je posebno za testiranje sigurnosti računalnih sustava te dolazi s različitim tvornički preuzetim alatima. Proces instalacije jednostavniji je nego prilikom postavljanja Windows računala. Naime, potrebno je preuzeti odgovarajuću instalacijsku datoteku ovisno o korištenoj virtualizacijskoj platformi. U ovom slučaju to je VMware platforma. 
1. Datoteka se može preuzeti s Kali Linux službene stranice (https://www.kali.org/get-kali/#kali-virtual-machines). 
2. Nakon preuzimanja, u programu VMware, potrebno je pritisnuti „_Player > File > Open_“ te otvoriti preuzetu datoteku. 
3. Potom, izbrisati „_Floppy_“ ulaz u konfiguraciji virtualnog računala te pokrenuti ga. 

**Tvornički postavljeni podaci za autentifikaciju su korisničko ime „kali“ te zaporka „kali“.**

### Enumeracija mreže

Prva faza svake sigurnosne analize sustava sastoji se od pasivnog i aktivnog prikupljanja podatka. Pasivno prikupljanje uključuje analizu javno dostupnih podataka poput imena zaposlenika, fizičke lokacije, korištenih tehnologija, analiza web stranice i tako dalje. U ovom primjeru, zbog same virtualne konfiguracije, ne postoji način na koji bi se mogli pasivno prikupiti podaci o mreži. Upravo zbog toga, analiza mreže započinje aktivnim prikupljanjem podataka. To uključuje otkrivanje IP adresa računala, skeniranje portova, te identifikacija potencijalnih ranjivosti. Mreža je konfigurirana tako da su sva računala, uključujući napadačko, u jednoj virtualnoj NAT mreži. 

Nadalje, kako bismo mogli započeti, potrebne su nam same adrese računala. Njih je jednostavno otkriti uz pomoć tvornički preuzetih alata u samom Kali Linux sustavu. Jedan od njih je _netdiscover_. Naime, on funkcionira tako što u mrežu pošalje ARP zahtjeve kako bi identificirao aktivna računala. Alat je potrebno pokrenuti uz administratorske (engl. root) privilegije (_sudo_ naredba) te uz „-r“ opciju navesti adresu mreže i CIDR notaciju. CIDR notacija je skup IP standarda koji služe kako bi IP adresi mreže lakše označili bitove podmreža.

![image](https://github.com/user-attachments/assets/df4faa5e-deb3-47c5-94f1-ceb6ddcdc319)

Alat će ispisati sva aktivna računala u mreži, no procesom eliminacije možemo otkriti koja su ciljana računala u mreži.

![image](https://github.com/user-attachments/assets/b7880880-694f-42b4-86e8-52f10e588c6d)

Naime, rezervirane adrese su one koje završavaju s brojem 1 (engl. _Default gateway_) ili 254 (Adresa usmjernika). Adresa koja završava s brojem 2 je adresa računala koje pokreće sam VMware program te automatski dijeli svoju mrežu zbog NAT konfiguracije. To ostavlja adrese koje završavaju s brojevima 133, 134, 135. 

Kada smo identificirali aktivna računala u mreži potrebno ih je dodatno enumerirati kako bismo otkrili koja od njih su korisnička, a koja upravitelj domene. Najpoznatiji alat za skeniranje portova je _nmap_.
Kako bismo skenirali individualnu IP adresu, potrebno je naredbi nmap dodati IP adresu računala. U ovom primjeru, koriste se dodatne opciju poput „-sC“, „-sV“ i „-Pn“. One zajedno redom omogućavaju alatu da pokrene osnovne ugrađene skripte, provjeri verzije usluga na računalima, te onemogući dodatno provjeravanje aktivnosti računala. 

Nakon što nmap završi skeniranje računala, na zaslon će ispisati sve podatke koje je prikupio o skeniranim portovima i servisima. Prema ovim informacija lako je uočiti kako jedno računalo ima više uključenih portova i vezanih usluga nego druga. Upravo zbog toga i samih naziva usluga, možemo zaključiti kako je to računalo upravitelj domene.

![image](https://github.com/user-attachments/assets/d1bc2ff6-c746-459c-aa79-4e630674d834)

Prema ostalim rezultatima, preostala računala imaju uključenu SMB uslugu (Portovi 135, 139, 445). Osim toga, možemo primijetiti kako su pokrenute skripte otkrile da je dodatno sigurnosno potpisivanje SMB paketa uključeno, ali ne obavezno. Ovo je vrlo bitan podatak na kojem će se temeljiti sljedeći napad.

![image](https://github.com/user-attachments/assets/8a947ce0-c797-4038-8ef9-cb591e72e914)

![image](https://github.com/user-attachments/assets/96774efc-2879-4ccf-a031-0c6e80b135b5)

### SMB Relay

Podaci prikupljeni u prošloj fazi upućuju na moguće izvođenje SMB Relay napada. Međutim, potrebno je zadovoljiti još jedan preduvjet. Korisnik koji će sudjelovati u ovom napadu treba imati administratorski pristup na ciljano računalo. Uz dosadašnje prikupljene podatke, ne zna se koji korisnici postoje u mreži ni koja domenska prava imaju. Unatoč tome, ovaj napad predstavlja mali rizik izvođenja zato što ga je teško raspoznati od stvarnog prometa u mreži. Kako bismo izveli ovaj napad, potrebno je otvoriti dvije kartice terminala na napadačkom računalu. U prvoj kartici potrebno je pokrenuti alat _responder_ uz opcije „-dvw“ i „-I“ koje će program usmjeriti na ispravno mrežno sučelje.

![image](https://github.com/user-attachments/assets/f933f289-a906-4981-bc11-9e88e7b17ac0)

U drugoj kartici pokreće se _ntlmrelayx_ alat uz koji je važno dodati opcije „-smb2support“ i „-tf“ kako bi specificirali adrese ciljanih računala. U ovom slučaju uz opciju „-tf“ postavit će se tekstualna datoteka „targets.txt“ koja sadrži IP adresu Workstation 1 računala.

![image](https://github.com/user-attachments/assets/6d4faf12-0b67-40fb-af6f-7a3ddeba0c67)

Potom je, putem socijalnog inženjeringa ili drugih tehnika, potrebno nagovoriti korisnika da pokuša pristupiti SMB usluzi napadačkog računala. Ovim će putem žrtva poslati svoje autentifikacijske oznake napadaču. Dobivene podatke, napadač može iskoristiti na mnogo načina. Dva moguća načina prikazana su u sljedećim poglavljima.

![image](https://github.com/user-attachments/assets/025d7dd6-afe7-4d2d-b311-10ce42a8616b)

![image](https://github.com/user-attachments/assets/8dfefd4d-5706-4f86-84ef-61b0c5d0c388)

![image](https://github.com/user-attachments/assets/107d8765-1521-41f4-80dc-fb5a076758bd)

Ovaj napad može se jednostavno spriječiti na više načina. Prvi način je uključiti dodatno sigurnosno potpisivanje SMB paketa na svakom računalu. Ovo je vrlo jednostavno i potpuno sprječava napad, no može rezultirati u lošijim performansama prilikom prenošenja datoteka. Drugi način je isključiti NTLM autentifikaciju. Ostali načini mogu uključivati restrikciju lokalnih administratora ili dodatno odvajanje korisničkih računa.

### Pass the Hash

Ova tehnika koristi kompromitirane _hash_ vrijednosti i korisnička imena kako bi otkrila računala kojima ti podaci mogu pristupiti. Kako bismo to ostvarili, koristit ćemo alat _crackmapexec_. Uz njegovu glavnu naredbu potrebno je dodati parametre „-u“ za korisničko ime, „-d“ za ciljanu domenu i „-H“ za _hash_ vrijednost. 
Važno je napomenuti kako će ovaj alat dodatno označiti računala na kojima odgovarajući korisnički računi imaju administratorski pristup. U ovom primjeru, otkriveno je kako korisnik domene „pperic“ može administratorskim pravima pristupiti svim korisničkim računalima u mreži.

![image](https://github.com/user-attachments/assets/3ca73049-b8c6-4f67-a5e7-1f7e82461135)

Potpuno zaustavljanje ovog napada vrlo je jednostavno. Naime, preporučeno je izbjegavati višestruko korištenje istih zaporki ili uključiti PAM (engl. Privilege Access Management) opciju koja će olakšati praćenje administratorskih i korisničkih operacija te ograničavati broj korisnika koji imaju pristup administrativnim funkcijama.

### Probijanje hash vrijednosti

Ukoliko napadač želi saznati koja je zaporka sakrivena iza _hash_ vrijednosti dobivene prilikom SMB Relay napada, koristit će alat za probijanje zaporka kao što su _john_ ili _hashcat_. U ovom primjeru koristit će se alat _hashcat_. Međutim, važno je napomenuti kako virtualna računala nemaju dovoljno grafičke ili procesorske snage kako bi probila zadane vrijednosti. **Zbog toga, alat je potrebno preuzeti i koristiti na vlastitom računalu**. 

Kako bi se probila _hash_ vrijednost, potrebno je identificirati o kojoj se vrsti _hash_-a radi. U ovom slučaju, zato što je vrijednost prikupljena pomoću SMB Relay napada, znamo kako je NTLM tipa. Zbog toga, pomoću _powershell_ programa, potrebno je pokrenuti _hashcat_ alat uz „-m 1000“ oznaku koja, prema službenoj _hashcat_ dokumentaciji, označava NTLM _hash_. Osim toga, prvo je potrebno specificirati datoteku u kojoj se nalazi _hash_ vrijednost (ntlm.txt), a zatim rječnik mogućih zaporka ispisanih u obliku čistog teksta (rockyou.txt). U ovom slučaju, datoteka rockyou.txt jedan je od gotovih rječnika koji dolaze tvornički instalirani sa sustavom Kali Linux. 
U stvarnom slučaju, rječnik možemo stvoriti na temelju informacija prikupljenih u prvoj fazi analize sustava ili preuzeti dodatne gotove rječnike poput SecLists github direktorija. Rječnici se često temelje na osobnim podacima zaposlenika, godišnjim dobima, godinama, nasumičnim kombinacijama znakova, i tako dalje.

![image](https://github.com/user-attachments/assets/a39f709c-da59-4bc3-b0ba-25bc1df6ee03)

Uspješnim izvođenjem ovog napada, _hashcat_ će ispisati probijenu zaporku na zaslon. Dobivena zaporka može se koristiti za udaljeni pristup ciljanom računalu ili prilikom izvođenja drugih napada kao što je prikazano u sljedećem poglavlju.

![image](https://github.com/user-attachments/assets/732d78cc-ccf9-4c9a-8ca9-07d20226f7b3)

Ne postoji siguran način za prevenciju ovog napada, no korisnik može poduzeti mnogo jednostavnih radnji kako bi napadačima znatno otežao probijanje zaporki. Najvažnija je koristiti snažne zaporke, preporučljivo duže od 16 ili 32 znaka. Također važno je izbjegavati jednostavne zaporke poput datuma rođenja, osobnih podataka, godina, godišnjih doba ili mjeseca.

### Kerberoasting

Ova je vjerojatno najpoznatija i najčešće korištena tehnika prilikom sigurnosnog testiranja Active Directory sustava. Naime, ovaj napad iskorištava ranjivost u samom _Kerberos_ sustavu autentifikacije kao što je objašnjeno u teorijskom dijelu ovog rada. Upravo zbog toga, ne postoji jednostavan način sprječavanja ovog napada. Načini na koji se posljedice ovog napada mogu ublažiti su korištenje vrlo jakih zaporka i strogo ograničiti domenska prava uslužnih računa (SVC) u mreži. 

Kako bismo uspješno izvršili ovaj napad, potreban nam je pristup korisničkom imenu i zaporki računa u domeni te IP adresa upravitelja domene. U ovom primjeru, napad se izvršava uz pomoć GetUserSPNs alata koji je instaliran uz tvorničke postavke Kali Linux sustava. Naredbu i argumente možemo vidjeti na sljedećoj slici -> `impacket-GetUserSPNs SKOLA.local/pperic:Password1 -dc-ip 192.168.40.133 -request`

![image](https://github.com/user-attachments/assets/e8da15eb-dee8-4fd3-8d2e-43b56e510327)

Dobivenu autentifikacijsku oznaku (engl. _Kerberos ticket_) možemo probiti istim postupkom kao što je prikazano u prošlom poglavlju, no korišteni modul potrebno je promijeniti na 13100 kako bi alat mogao prepoznati Kerberos _hash_ oblik.

![image](https://github.com/user-attachments/assets/468234ac-ca2e-496d-b052-3d8974f2ea92)

![image](https://github.com/user-attachments/assets/644a7bdb-d53a-4fce-9e1d-9289f3442101)

### Narušavanje sigurnosti administratora i domene

Kako bismo potpuno kompromitirali domenu, potreban nam je administratorski račun pomoću kojeg se možemo prijaviti na upravitelj domene. To se može ostvariti na mnogo različitih načina, no u svrhu jednostavnosti ovog rada koriti će se _Pass the Password_ tehnika. Naime, ona funkcionira na isti način poput _Pass the Hash_ tehnike, no umjesto _hash_ vrijednosti, kroz mrežu šalje stvarnu zaporku. Kako bi se naglasak dodatno postavio na nesigurnosti višestrukog korištenja zaporka, u ovom primjeru koristit ćemo zaporku računa pperic te vidjeti odgovara li ona administratorskom računu.

![image](https://github.com/user-attachments/assets/0319908f-2db4-40d2-aaf3-1bba7af49903)

Ovim primjerom naglašavamo važnost korištenja snažnih zaporka te nesigurnost višestrukog korištenja istih. Dovoljno je da jedna osoba s administratorskim pristupom domeni neozbiljno shvati sigurnost sustava i omogući zlonamjernim korisnicima potpuno narušavanje sigurnosti domene. Kako bi napadač ostvario trajni pristup domeni, pomoću administratorskog računa može dohvatiti sve zaporke i autentifikacijske vrijednosti pohranjene na upravitelju domene. To može ostvariti pomoću alata kao što je _secretsdump_. Sve što je potrebno je administratorski račun na kontroleru domene te IP adresa istog.

![image](https://github.com/user-attachments/assets/b9f22101-61fc-4acb-87e1-d0a54830c95d)

![image](https://github.com/user-attachments/assets/164ca925-a4a2-480a-b22b-39827f7548e9)


