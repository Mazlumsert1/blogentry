# Sikker MySQL

![enter image description here](http://technotif.com/wp-content/uploads/2014/02/Improve-MySQL-security.jpg)

Som en af de mest brugte open-source Relational Database Management System er MySQL en god færdighed at have, samt giver fordelagtig karriere muligheder. På grund af det konsekvente niveau af hurtig performance og nemt at bruge er den i dag brugt mange steder, både af webudvikler, individuelle og store organisationer, såsom Youtube, Google og Yahoo. Som de fleste produkter så er sikkerhed ikke en vigtig overvejelse. Ofte vil man gerne have det hurtig op at køre, således at organisationen kan hurtig drage fordel af det. Det er derfor vigtigt at man sikre MySQL databasen, da det kan have voldsomme konsekvenser, såsom tabt data eller ransomware, som var på omløb i 2017.

## Best Practices for increasing security
I denne blog vil vi se på nogle af de mest populære sikkerhedsproblemer i MySQL, og hermed løsninger til en mere sikker MySQL, dog er det yderst vigtigt, samt antaget at man har sikret serveren, inden man sikre MySQL databasen.

Ofte kan man øge sikkerheden for en MySQL database med små konfigurationer, som kan gøre en stor forskel. 

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

#### Fjern anonyme og ubrugte users
```bash
mysql> DROP USER "";
```

## Kilder
- http://www.hexatier.com/mysql-database-security-best-practices-2/
- https://www.mysql.com/why-mysql/presentations/mysql-security-best-practices/
- https://www.techrepublic.com/article/the-top-10-worst-ransomware-attacks-of-2017-so-far/
- https://www.percona.com/blog/2017/02/27/mysql-ransomware-open-source-database-security-part-3/
- http://uk.businessinsider.com/most-popular-passwords-incredibly-insecure-easy-to-guess-123456-hacking-2017-1?r=US&IR=T
- https://www.w3resource.com/mysql/mysql-security.php