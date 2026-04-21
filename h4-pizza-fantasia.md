# h4) Pizza Fantasia

## x) Lue ja tiivistä

### 4.12.1

 - vertailtiin kahden eri konfiguraationhallintaohjelman omaa kieltä
 - Salt:ssa on 510 eri funktiota ja Puppet:ssa 113 erilaista

### 4.12.2

- vertailtiin julkisia kolmannen osapuolen konfiguraatioita
- näistä nähtiin, että suuri osa komennoista on package-file serviceen liittyviä
- eli vaikka molemmissa ohjelmissa on suuri määrä erilaisia funktioita, pienellä määrälllä pääsee jo pitkälle

### 4.12.3.1

- on tärkeää rakentaa konfiguraatio niin, että järjestelmä on idempotentti
- jotkut palvelut, kuten apt-get ovat jo valmiiksi idempotentteja
- konfiguraatiot tulisi ajaa vain, jos on tehty muutoksia

[Lähde](https://westminsterresearch.westminster.ac.uk/item/w7vvz/configuration-management-of-distributed-systems-over-unreliable-and-hostile-networks)

## a) Räpylä. Asenna itse valitsemasi demoni käsin

Valitsin Teron antamasta listasta ensimmäisen demonin. 

> sudo apt-get install postgresql 

Tässä kohtaa tuli ilmoitus että pitäisi ajaa sudo dpkg --configure -a, eli piti manuaalisesti käskeä järjestelmän ajamaan kaikki paketit asennetuksi. (ChatGPT)

<img width="880" height="149" alt="image" src="https://github.com/user-attachments/assets/0477f624-1873-4d90-b6c4-1863ac43222c" />

## b) Automaatti. Automatisoi valitsemasi demonin asennus Ansiblella.

Lähdin automatisoimaan postgresql:n asennuksen ansiblella luomalla sille omat kansiot kuten aikaisemmissakin tehtävissä. Eli /ansibles/roles/ -kansion alle lisäsin uuden roolin postgresql, jonka alle loin alikansiot files, tasks, ja handlers.

Ensin loin tasks -> main.yml alle tarvittavat tehtävät. Otin mallia aikaisemmasta tehtävästä ja poistin ylimääräiset rivit.

<img width="674" height="253" alt="image" src="https://github.com/user-attachments/assets/e3b8d5b9-0110-43f9-8734-ff8c5493dbb7" />

Sitten handlers -> main.yml. Tämä siis reagoi siinä vaiheessa, kun jotakin asetusta muutetaan ja uudelleenkäynnistää demonin.

<img width="650" height="114" alt="image" src="https://github.com/user-attachments/assets/f8821fd2-3fc9-4559-a4ed-c92eaf523f54" />

Kokeilin ajaa playbookin ja tuloksena oli OK.

<img width="934" height="413" alt="image" src="https://github.com/user-attachments/assets/8ec42119-f6ff-491a-aa12-d8f61387c26e" />

Taskit menivät siis läpi, mutta koska postgresql oli jo asennettuna niin tuloksena oli vain OK.

## c)  Asetus. Muuta asetustiedostoa herralla (master, "control node") ja aja ansible uudestaan.

Ensimmäiseksi etsin oikean polun, että voin muuttaa jotain asetusta.

> /etc/postgresql/15/main/conf.d/

Lisäsin tiedostoon lokien keräyksen: 

> logging_collector = on

> log_destination = 'stderr'

[Postgresql](https://www.postgresql.org/docs/current/runtime-config-logging.html)

Sitten piti muuttaa tasks/main.yml kopioimaan tiedosto oikeaan paikkaan:

<img width="616" height="188" alt="image" src="https://github.com/user-attachments/assets/c260310f-673a-4cd9-a9cc-58b4bcee96f4" />

Sitten ajamaan playbook:

<img width="938" height="581" alt="image" src="https://github.com/user-attachments/assets/80f16808-6700-4064-8938-fd219cdf7957" />

Kuvasta nähdään, että tiedosto kopioitiin uuteen paikkaan. Ja koska muutoksia tuli, postgresql käynnistyi uudelleen. Nämä kaksi asiaa ovat siis Changed = 2.

## d) Paikka remonttiin. Riko jotain asetuksia. Voit esimerkiksi poistaa demonin 'sudo apt-get purge foobar' 

Lähdin poistamaan postgresql

> sudo apt-get purge postgresql

Mutta tämä ei auttanut, vaan koneelle jäi vielä paketteja. 

<img width="771" height="211" alt="image" src="https://github.com/user-attachments/assets/08e82c69-0e40-48ea-ac8d-7a22803c6b05" />

Muutin komentoa

> sudo apt-get purge -y 'postgresql*'

jotta se sisältää kaikki paketit jotka sisältävät postgresql.

<img width="453" height="58" alt="image" src="https://github.com/user-attachments/assets/6c91c2fd-dd60-4ded-a281-132bc71223d0" />

Sitten ajetaan playbook uudestaan ja toivotaan että tilanne palaa ennalleen:

<img width="944" height="499" alt="image" src="https://github.com/user-attachments/assets/fd2b1f4b-1bef-4db8-99c1-481e1cb8c29d" />

Toimii:

<img width="877" height="213" alt="image" src="https://github.com/user-attachments/assets/3a456593-edfa-46e3-90ee-14127647f6f8" />

## e) Idempotentti. Osoita, että tilasi on idempotentti.

Ajetaan playbook uudestaan:

<img width="950" height="509" alt="image" src="https://github.com/user-attachments/assets/9f62b565-383a-40af-a3ad-55c2d7c58d5f" />

Ei muutoksia, eli tila on idempotentti.

## Lähteet:

Kurssisivu ja tehtävänanto: https://terokarvinen.com/palvelinten-hallinta/

Postgresql. Runtime Config Logging. Luettavissa: https://www.postgresql.org/docs/current/runtime-config-logging.html Luettu 21.4.2026.

Karvinen, Tero. 2024. Configuration Management of Distributed Systems over Unreliable and Hostile Networks. Luettavissa https://westminsterresearch.westminster.ac.uk/item/w7vvz/configuration-management-of-distributed-systems-over-unreliable-and-hostile-networks Luettu 20.4.2026.



