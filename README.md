# Sikker MySQL

![enter image description here](http://technotif.com/wp-content/uploads/2014/02/Improve-MySQL-security.jpg)

Som en af de mest brugte open-source Relational Database Management System er MySQL en god færdighed at have, samt giver fordelagtig karriere muligheder. På grund af det konsekvente niveau af hurtig performance og nemt at bruge er den i dag brugt mange steder, både af webudvikler, individuelle og store organisationer, såsom Youtube, Google og Yahoo. Som de fleste produkter så er sikkerhed ikke en vigtig overvejelse. Ofte vil man gerne have det hurtig op at køre, således at organisationen kan hurtig drage fordel af det. Det er derfor vigtigt at man sikre MySQL databasen, da det kan have voldsomme konsekvenser, såsom tabt data eller ransomware, som var på omløb i 2017.

## Best Practices for increasing security
I denne blog vil vi se på nogle af de mest populære sikkerhedsproblemer i MySQL, og hermed løsninger til en mere sikker MySQL, dog er det yderst vigtigt, samt antaget at man har sikret serveren, inden man sikre MySQL databasen.

Ofte kan man øge sikkerheden for en MySQL database med små konfigurationer, som kan gøre en stor forskel. 

---

#### Fjern unødvendige users
Det er vigtig at man ikke har overflødige users, som ikke bliver brugt. Ofte kan det være test users uden specificeret hostname og password, derfor burde disse users straks slettes fra databasen.

```bash
mysql> select User,Host,Password FROM mysql.user;

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

```
mysql> select User,Host,Password FROM mysql.user;

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

## Kilder
- http://www.hexatier.com/mysql-database-security-best-practices-2/
- https://www.mysql.com/why-mysql/presentations/mysql-security-best-practices/
- https://www.techrepublic.com/article/the-top-10-worst-ransomware-attacks-of-2017-so-far/
- https://www.percona.com/blog/2017/02/27/mysql-ransomware-open-source-database-security-part-3/
- http://uk.businessinsider.com/most-popular-passwords-incredibly-insecure-easy-to-guess-123456-hacking-2017-1?r=US&IR=T
- https://www.w3resource.com/mysql/mysql-security.php
- https://www.digitalocean.com/community/tutorials/how-to-secure-mysql-and-mariadb-databases-in-a-linux-vps