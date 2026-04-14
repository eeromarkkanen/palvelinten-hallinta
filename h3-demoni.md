# H3 - Demoni

## x) Lue ja tiivistä

### [Apache installed with Ansible - quick notes](https://terokarvinen.com/apache-ansible/)
- käy läpi vaadittavat kansiot ja tiedostot apachen asennukseen ansiblella
- roles, tasks, files, handlers
- olen huomannut, että monilla unohtuu muutosten jälkeen tehdä demonille restart, eli on hyvä että ohje muistuttaa siitä

### [Handlers: running operations on change](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_handlers.html)
- handlers on hyödyllinen, jos halutaan ajaa task ainoastaan jos siinä on tapahtunut muutos
- voidaan käyttää joko yhtä tai useampaa kerralla
- handlers täytyy nimetä, jotta se voidaan käynnistää sanalla *notify*
- kukin handlers ajetaan vain kerran, vaikka useampi tehtävä viittaisi siihen

### ansible-doc service 

- Johdanto: hallitsee palveluite etäkoneilla. Toimii proxyna varsinaiselle palvelunhallintamodulille. Käy läpi perusasiat
- enabled: käynnistyykö palvelu kun kone käynnistetään
- name: palvelun nimi
- state: vaaditaan yksi tila + enabled. started/stopped ajetaan vain tarvittaessa, restarted ja reloaded ajetaan aina
- examples: esimerkit yllä mainituista, lisäksi pattern ja args

## a) Apassi. Asenna Apache 2 käsin. Weppisivun tulee näkyä palvelimen etusivulla. Sivun tulee olla tavallisen käyttäjän muokattavissa, ilman root- tai sudo-oikeuksia.

Aloitin poistamalla apache2, jotta voin tehdä tämän alusta.

sudo apt-get install apache2

(välissä piti pysäyttää nginx kun sitä olin tunnilla asentanut)

sudo systemctl start apache2

curl localhost

<img width="818" height="359" alt="image" src="https://github.com/user-attachments/assets/1f58e989-83b7-4fd3-8ff4-0294d7b16314" />

Kuvassa näkyy vanhat konfiguraatiot, koska en poistanut kaikkea apache2 liittyvää purge-komennolla.

Tässä kohtaa ihmettelin, miksi vanha sivu näkyi, vaikka mikään ei osoittanut /public_html/ kansioon, jossa html-tiedosto on. Selvittämisen jälkeen selvisi, että olin joskus kirjoittanut /var/www/html/ tiedoston päälle saman tekstin. Älä tee näin :)

Eli default.conf osoitti var/www/html josta tämä sivu löytyi. Vaihdoin apache2 käyttämään uutta newsite.conf tiedostoa, joka osoittaa kotihakemiston public_html kansioon.

sudo a2ensite newsite.conf

sudo a2dissite 000-default.conf [lähde](https://manpages.ubuntu.com/manpages/focal/man8/a2ensite.8.html)

sudo systemctl reload apache2

Lisäsin oikeudet kansioihin ja tiedostoon:

<img width="658" height="74" alt="image" src="https://github.com/user-attachments/assets/d1c8764b-0db7-4e84-957c-dd405c0cd17a" />

Tulos:

<img width="543" height="122" alt="image" src="https://github.com/user-attachments/assets/5266e379-3817-4067-85ec-6eaf0861c942" />

## b) Moottorix. Asenna Nginx käsin. Weppisivun tulee näkyä palvelimen etusivulla. Sivun tulee olla tavallisen käyttäjän muokattavissa, ilman root- tai sudo-oikeuksia. (Muista sammuttaa Apache ensin.)

Asensin nginx [tämän ohjeen](https://nginx.org/en/linux_packages.html) mukaan. Tämä sujui ilman ongelmia.

Seuraavaksi kopioin /etc/nginx/sites-available/default tiedoston perästä konfiguraation ja loin tiedoston newsite.

<img width="426" height="321" alt="image" src="https://github.com/user-attachments/assets/69fb4da3-fb2f-4544-8675-b596d320591a" />

Sitten piti siirtää luotu sivu sites-enabled kansioon: ln -s /etc/nginx/sites-available/newsite /etc/nginx/sites-enabled/

Tässä vaiheessa localhost näytti kuitenkin nginx-oletussivun. Ensimmäinen virhe löytyi tiedostosta, se osoitti home/eero/publicsite/ eikä /home/eerom/publicsite/.

Käynnistelin nginx uudestaan mutta muutoksia ei tapahtunut. Lähdin etsimään syitä tälle, ja löysin [tämän](https://www.youtube.com/watch?v=Hfg7_y0fGTg&list=PL2We04F3Y_40fFklIDYcdA9B_nUK9_c6a) videon avulla ongelman.

nginx.conf tiedostossa ei ollut mukana include /etc/nginx/sites-enabled/*; joten se ei lukenut tiedostoa oikeasta paikasta. Kommentoin samalla pois ylemmän rivin, joka haki *conf tiedostoja. Tämän jälkeen käynnistin taas nginx:n uudelleen ja localhost näytti tältä.

<img width="587" height="190" alt="image" src="https://github.com/user-attachments/assets/3032eecb-91c6-4e58-9952-4e1481a810f3" />

## c) Automoottorix. Automatisoi Nginx asennus Ansiblella. Ylläpitäjän osuus Ansiblella riittää, itse HTML-weppisivut voi tehdä käsin.

Lähdin automatisoimaan nginx asennusta ottamalla oppia Teron apache2 -asennusohjeista. Loin ensimmäiseksi /roles/nginx/tasks/main.yml tiedoston, johon vaihdoin vain oikeat nimet. Kirjoitin polkuun tiedoston, jota ei ole olemassa että saan varmasti virheilmoituksen:

<img width="1113" height="260" alt="image" src="https://github.com/user-attachments/assets/336448f4-e4d4-45dc-885b-bc725d24af01" />

Kuvasta näkyy selkeästi, että kotisivu.conf ei ole olemassa. Luodaan sellainen /nginx/files/ alle:

<img width="686" height="357" alt="image" src="https://github.com/user-attachments/assets/038c0160-f9c8-4308-8f8b-6152d2949469" />

Seuraava virheilmoitus oli väärä polku, tässä kohtaa oli vain puuttuva "/" etc edestä. Tämän jälkeen ajoin uudestaan ja saatiin uusi virheilmoitus:

<img width="1119" height="110" alt="image" src="https://github.com/user-attachments/assets/cb19a2bf-0b20-46c9-8c2f-793fb9189d95" />

Eli puuttuvat handlers. Tämä on varmasti ihan toivottu virheilmoitus, koska näitä ei ole vielä luotu. Lisätään siis handlers kansioon main.yml:

<img width="602" height="117" alt="image" src="https://github.com/user-attachments/assets/9244ac63-f810-4383-9496-e5e5fe41998c" />

Sitten taas kokeilemaan ansible-playbook site.yml -K.

<img width="1097" height="252" alt="image" src="https://github.com/user-attachments/assets/1fbd693c-da6b-4311-a6ac-f5f045da4a84" />

Tällä kertaa ei tullut virheilmoitusta vaan tuli changed. Yllätyin vähän itsekin, että tämä toimi jo tässä vaiheessa.

<img width="506" height="288" alt="image" src="https://github.com/user-attachments/assets/2cc9a61d-efe5-47be-9816-53d791923971" />

Lopuksi halusin kokeilla toimintaa muuttamalla kotisivua /public_html/index.html tiedostosta. Koska en osaa HTML, pyysin ChatGPT luomaan testisivun.

Muutin tiedostoa ja kokeilin ajaa playbookin uudestaan. Testistä tuli ainoastaan OK eikä Changed, mutta kotisivu muuttui.

Huomasin vielä palautuksen jälkeen, että kotisivu.conf ei ollut oikeassa files-kansiossa niinkuin halusin. Siirsin sen sinne ja ajoin playbookin uudestaan ilman virheitä. (14.4.2026 13:23) 


<img width="1919" height="895" alt="image" src="https://github.com/user-attachments/assets/9b6a8e13-3279-4099-b4a0-7f1daa768b87" />

tasks/main.yml:

<img width="611" height="465" alt="image" src="https://github.com/user-attachments/assets/fef11201-f28d-4dcc-9db2-0d995fa39e0a" />

handlers/main.yml:

<img width="611" height="114" alt="image" src="https://github.com/user-attachments/assets/2965510f-741c-4d2e-b24f-21082f020e28" />

kotisivu.conf:

<img width="571" height="327" alt="image" src="https://github.com/user-attachments/assets/d0bfc714-1d68-4774-b763-748b851ab39d" />

playbook site.yml:

<img width="414" height="259" alt="image" src="https://github.com/user-attachments/assets/b83fdec5-90cd-4865-bd34-038fb0d6cf2f" />






## Lähteet

Ansible Community Documentation. Handlers: running operations on change. URL: https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_handlers.html Luettu 14.4.2026

Karvinen, T. 2026. Apache installed with Ansible - quick notes. URL: https://terokarvinen.com/apache-ansible/ Luettu 14.4.2026

Ubuntu Manual. URL: https://manpages.ubuntu.com/manpages/focal/man8/a2ensite.8.html Luettu 14.4.2026

KodeKloud. NGINX for Beginners. URL: https://www.youtube.com/watch?v=Hfg7_y0fGTg&list=PL2We04F3Y_40fFklIDYcdA9B_nUK9_c6a (50:40->) Katsottu 14.4.2026

ChatGPT kotisivun luomisessa. chat.openai.com

### edits

14.4.2026 13:26 muokattu muotoilua luettavammaksi
