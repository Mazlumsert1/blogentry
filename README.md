# blogentry


Continous Deployment & Integration Med Laravel


Abstract:
Da vi lever i en verden hvor tekniske revolutioner forekommer ofte og menneskets behov vokser eksponentielt, er det derfor relevant at vi som individer der arbejder indenfor IT branchen kan regere ligeså hurtigt, eftersom kundens efterspørgsel stiger.  

Et vigtigt element til at følge kundernes efterspørgsel, er ved at man kan skabe det såkaldte ”deployment chain”, dette vil fremhæve hastigheden for processen. Hermed vil dette medføre en automatisering af vores deployment proces. Med Laravel Forge(CD) og Travis(CI) kan vi skabe en ”deployment chain” og dermed få det op på produktion. 
Nedstående illustreres ”deployment chain” processen. 




Trinene for vores deployment:

1.	Push til master branch i github.
2.	Travis CI udløser vores push og køre testene. 
3.	Forge bliver udløst af Travis CI og deployer ændringerne til remote server. 


The problem
En ulempe ved at anvende Laravel Forge, er f.eks. at det koster penge så i længden kan det gå hen og blive dyrt. 
Et andet problem kunne være at fordi der sker så meget i baggrunden kan det være svært at konfigurere.  

Forge er ikke designet til at forsyne sikkerhed, så det er op til udviklerne at tilføje ekstra sikkerhed. 

En af løsningerne kunne være at anvende NGINX webserver som har en feature kaldet rate limiting, som giver den mulighed for at begrænse mængden af HTTP-requests i et specifikt tidsinterval. 
Hertil forstås der både GET og POST request.
Hvis man observere i sine server logs, vil man finde frem til fejl koden 429(To many requests).  	

Rate limiting bliver brugt af sikkerhedsformål, f.eks. for at beskytter os imod DDoS angreb ved at begrænse indkommende requests. Generelt bliver rate limiting anvendt til at beskytte NGINX server fra overvældene mange requests på samme tid.  

Man kan gå ind konfigurere rate limit således:

limit_req_zone $binary_remote_addr zone=mylimit:10mrate=10r/s;

server {
    location /login/ {
        limit_req zone=mylimit;

        proxy_pass http://my_upstream;
    }
}

Ved at ændre på de forskellige parameter kan man ændre antal requests man gerne vil indenfor et tidsinterval. 
