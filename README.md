<img src="https://static.digit.in/default/c14bbe3f8f8ac93b289bd23c8442423f9052c1fe.jpeg" width="100%">

# Sikker MySQL
_af Ismail Cam og Mazlum D. Sert_

Som en af de mest brugte open-source Relational Database Management System er MySQL en god færdighed at have, samt giver fordelagtig karriere muligheder. På grund af det konsekvente niveau af hurtig performance og nemt at bruge er den i dag brugt mange steder, både af webudvikler, individuelle og store organisationer, såsom Youtube, Google og Yahoo. Som de fleste produkter så er sikkerhed ikke en vigtig overvejelse. Ofte vil man gerne have det hurtig op at køre, således at organisationen kan hurtig drage fordel af det. Det er derfor vigtigt at man sikre MySQL databasen, da det kan have voldsomme konsekvenser, såsom tabt data eller ransomware, som var på omløb i 2017.

## 7 gode råd til at forbedre MySQL sikkerheden
I denne blog vil vi se på nogle af de mest populære sikkerhedsfaktorer i MySQL, og hermed løsninger til en mere sikker MySQL, dog er det yderst vigtigt, samt antaget at man har sikret serveren, inden man sikre MySQL databasen.

Ofte kan man øge sikkerheden for en MySQL database med små konfigurationer, som kan gøre en stor forskel. Vi vil nedenfor komme eksempler på hvordan man kan øge sikkerheden i en MySQL database.

#### Fjern unødvendige users
Det er vigtig at man ikke har overflødige users, som ikke bliver brugt. Ofte kan det være test users uden specificeret hostname og password, derfor burde disse users straks slettes fra databasen.

```bash
mysql> select User,Host,Password FROM mysql.user;
```
```
+------------------+-----------+-------------------------------------------+
| user             | host      | password                                  |
+------------------+-----------+-------------------------------------------+
| root             | localhost | *DE06E242B88EFB1FE4B5083587C260BACB2A6158 |
| demo-user        | %         |                                           |
| root             | 127.0.0.1 | *DE06E242B88EFB1FE4B5083587C260BACB2A6158 |
| test-user        | localhost |                                           |
+------------------+-----------+-------------------------------------------+
```

```bash
mysql> delete from mysql.user where User="demo-user";
mysql> delete from mysql.user where User="test-user";
```

```bash
mysql> select User,Host,Password FROM mysql.user;
```

```
+------------------+-----------+-------------------------------------------+
| user             | host      | password                                  |
+------------------+-----------+-------------------------------------------+
| root             | localhost | *DE06E242B88EFB1FE4B5083587C260BACB2A6158 |
| root             | 127.0.0.1 | *DE06E242B88EFB1FE4B5083587C260BACB2A6158 |
+------------------+-----------+-------------------------------------------+
```

```bash
mysql> flush privileges;
```

---

#### Ændre default user
Alene i 2016 var den mest brugte username “root” og password “123456”, så det er rimeligt at antage dette kan være en realitet. Ofte forsøger hackerne at få adgang til rettighederne til denne bruger, derfor er det vigtigt at denne bruger ændres. Det anbefales at adgangskoden ændres til en kompleks alfanumerisk adgangskode. Dette kan gøres hurtigt og nemt på to step.

```bash
# Change username
mysql> RENAME USER root TO secure_user;

# Change password
mysql> SET PASSWORD FOR 'secure_user'@'hostname' = PASSWORD('01MoreSecurePass02');
```

---

#### Begrænse user tilladelser
Det er vigtigt at man har applikation-specifikke users, altså users som kun har de nødvendige tilladelser til databasen. For at sikre at der ikke kan slettes data via en web-applikation, kan man oprette en user, som f.eks. kun har SELECT, INSERT, UPDATE rettigheder.

```bash
mysql> create user 'webapp'@'localhost' identified by '01SecurePassword02';
```
```bash
mysql> grant select, insert, update on hackernewsdb.* to 'webapp'@'localhost';
```
```bash
mysql> flush privileges;
```

---

#### Skift standard port mappings
MySQL kører som standard på port 3306. Dette bør ændres efter installationen for at forvirre, hvilke tjenester der kører på hvilke porte, da angriberne i første omgang vil forsøge at udnytte standardværdier. Nedenfor viser vi et eksempel på hvordan vi ændre porten fra 3306 til 3360.

```bash
# Open file my.cnf
nano /etc/mysql/my.cnf
```
```bash
# Find the following section, and change the port number
[mysqld]
#
# * Basic Settings
#
#user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
......
```

---

#### Slå "LOAD DATA LOCAL INFILE" kommando fra
Kommandoen "LOAD DATA LOCAL INFILE" gør det muligt for brugere at læse lokale filer og endda få adgang til andre filer på operativsystemet. Dette kan være meget beskadig, hvis det bliver udnyttet af ondsindede bruger ved hjælp af metoder som SQL injection. Det anbefales derfor, at deaktivere kommandoen. Nedenfor viser vi et eksempel på hvordan vi deaktivere kommandoen.

```bash
# Open file my.cnf
nano /etc/mysql/my.cnf
```
```bash
# Find the following section
[mysqld]
#
# * Basic Settings
#
......
```
```bash
# Insert this in end of the section
local-infile=0
```

---

#### Deaktiver fjernadgang til databasen
Skal databasen kun bruges af lokale applikationer, anbefales det at fjernadgang til databasen deaktiveres. Denne konfiguration sikre, at MySQL ikke acceptere forbindelser fra hvor som helst, men kun localhost. Altså ved at bruge denne konfiguration forhindres MySQL i at lytte til TCP/IP forbindelser, så det kun er lokale users der vil have tilladelse til at oprette forbindelse til MySQL databasen. Nedenfor viser vi et eksempel på hvordan vi deaktivere fjernadgang.

```bash
# Find the following section
[mysqld]
#
# * Basic Settings
#
......
```
```bash
# Insert this in end of the section
skip-networking
```

---

#### Læse/skrive rettighederne til my.cnf filen
Det er vigtigt at beskytte my.cnf filen, så andre end admin ikke kan ændre den. Derfor anbefales det at gøre filen skrivebeskyttet. Nedenfor viser vi et eksempel på hvordan vi skrivebeskytter my.cnf filen.

```bash
sudo chmod 0644 /etc/mysql/my.cnf
```

## Konklusion
Der skal ikke være nogen tvivl om, at man skal priotere sikkerheden højt, samt tage højder for en fejlfri opsætning. 
Da database ofte er udsat for angreb, er det vigtigt at man sætter sig ind i de forskellige konfigurationer, for hvordan man beskytter databasen så godt som muligt. 
Det er en god ide at vægte sikkerheden højt, når man skal vælge hvilken database der skal bruges. Det kan ikke blive sagt nok, at enhver udvikler skal have det fundamentale på plads, når der snakkes om sikkerhed. Det kunne f.eks. være hashing af passwords eller andre følsomme data. Vi har gang på gang set, at dårlig sikkerhed kan have livsfarende konsekvenser, og koste virksomheder millioner. 

Er MySQL en del af udviklingen, så er der vise standarder der burde blive overholdt. Nogle af dem kunne være konfigurationer, som vi har gennemgået i denne blog. Vi har dog i denne blog kun rørt på overfladen, hvor meget sikkerhed der burde være, og håber at det kan være motivation, for forskellige individer og organisationer. 

## Kilder
- http://www.hexatier.com/mysql-database-security-best-practices-2/
- https://www.mysql.com/why-mysql/presentations/mysql-security-best-practices/
- https://www.techrepublic.com/article/the-top-10-worst-ransomware-attacks-of-2017-so-far/
- https://www.percona.com/blog/2017/02/27/mysql-ransomware-open-source-database-security-part-3/
- http://uk.businessinsider.com/most-popular-passwords-incredibly-insecure-easy-to-guess-123456-hacking-2017-1?r=US&IR=T
- https://www.w3resource.com/mysql/mysql-security.php
- https://www.digitalocean.com/community/tutorials/how-to-secure-mysql-and-mariadb-databases-in-a-linux-vps
- https://www.upguard.com/articles/top-11-ways-to-improve-mysql-security
