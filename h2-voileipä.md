# Voileipä

## x) Lue ja tiivistä

- [Sudo without Password](https://terokarvinen.com/passwordless-sudo/) (Karvinen, 2026)
- Artikkelissa käydään läpi sudo-käyttäjän tai ryhmän lisääminen ilman salasanaa toimivaksi.
- Muista avata toiseen ikkunaan #root shell, jotta sudon voi korjata virhetilanteissa.

- [xkcd 149](https://xkcd.com/149/) (Munroe, 2006)
- Sarjakuva, joka demonstroi hauskasti sudo-ominaisuuksia.

- [Passwordless sudo with Ansible](https://terokarvinen.com/passwordless-sudo-with-ansible/) (Karvinen, 2026)
- Ohjeet salasanattoman sudokäyttäjän luomiseen Ansiblella
- Lopuksi hieman vinkkejä, jotka auttavat varmasti kotitehtävän myöhemmissä vaiheissa.

- Ansiblen omat dokumentaatiot

- ansible-doc copy
- Kopioi tiedoston joko paikalliselta tai etäkoneelta etäkoneelle.
- content = Voidaan käyttää src sijasta kirjoittamaan sisällön itse ilman erillistä lähdetiedostoa. Luo myös kohdetiedoston, jos sellaista ei ole olemassa.
- dest ja src = lähde ja kohde. Voivat olla absoluuttisia tai suhteellisia polkuja.
- owner ja group = tiedoston omistaja, käyttäjä tai ryhmä. Jos tätä ei erikseen lisätä, Ansible käyttää nykyistä käyttäjää tai ryhmää.
- mode = mode-komennon lisäämällä voidaan varmistaa, että tiedoston käyttöoikeudet ovat ainoastaan halutuilla henkilöillä. Esimerkiksi mode: '0644'.
- numerot tulevat 4 = read, 2 = write, 1 = execute ja näitä lisätään yhteen. ([McBrien, 2023)](https://www.redhat.com/en/blog/linux-file-permissions-explained)
  <img width="471" height="163" alt="image" src="https://github.com/user-attachments/assets/9ab80c40-2be2-43e1-aff3-7f8fb1a4a3e2" />


- ansible-doc apt
- Yksinkertaisesti paketinhallinta
- name = paketin nimi ja mahdollisesti versio
- state = haluttu paketin tila. oletuksena present. esim. latest varmistaa, että asennettuna on viimeisin versio.
- update_cache = ajaa käytännössä 'apt-get update'.
  <img width="365" height="93" alt="image" src="https://github.com/user-attachments/assets/96f2fcd7-5bd7-4abd-829f-53ac3b47f3e0" />


- ansible-doc file
- käytetään asettamaan tiedostoille ominaisuuksia
- path = käsiteltävän tiedoston polku
- recurse = määritellyt asetukset tehdään myös rekursiivisesti kaikille sen alla oleville tiedostoille. Toimii vain jos state: directory.
- src = polku, johon tiedosto linkittyy. Voidaan luoda joko symbolinen 'link' tai kova 'hard'.
- state = millainen kohde halutaan luoda (esim. tiedosto/hakemisto/linkki)
- owner, group, mode = selitetty yllä 'copy'-kohdassa.
  <img width="389" height="194" alt="image" src="https://github.com/user-attachments/assets/7ecb3d96-3561-4183-8d85-4c87f8480cce" />


- ansible-doc user
- käyttäjien hallintaan ja oikeuksiin
- name = käyttäjänimi, joka halutaan luoda/muokata/poistaa
- create_home = luo kotihakemiston käyttäjälle
- comment = voidaan lisätä kuvailemaan käyttäjää
- groups = mihin ryhmiin käyttäjä lisätään
- shell = tällä komennolla voidaan määrittää erillinen shell käyttäjälle. 
- state = kertoo luodaanko (present) vai poistetaanko (absent) käyttäjä
- system = luo järjestelmäkäyttäjän jos asetetaan true
  <img width="890" height="153" alt="image" src="https://github.com/user-attachments/assets/a8c00eff-84b5-4ef1-950a-66c191d6e204" />


- ansible-doc authorized_key
- lisää tai poistaa SSH-avaimet halutuille käyttäjille
- user = käyttäjä, jonka avaimia halutaan hallita etäkoneella
- key = julkinen SSH-avain
  <img width="675" height="123" alt="image" src="https://github.com/user-attachments/assets/b25b13d6-dcfe-4844-a88f-d8ae38f463c8" />

  ## a)  Sudoless. Tee ansiblea varten tunnus, jolla voi käyttää sudoa ilman salasanaa. Sekä ssh-kirjautuminen että sudon käyttö tulee olla ansbilea varten automatisoitu.

  Lähdetään luomaan uusi käyttäjä tehtävää varten.

  >> sudo adduser sudonopw
  >> sudo groupadd sudoless
  >> sudo adduser sudonopw sudoless

  Käyttäjän luotuoani avasin root-shellin toiseen ikkunaan komennolla sudo -i. Tämä varmistaa että jos sudo menee rikki, se on mahdollista korjata. <sup><sub>Älä kysy miten, mutta ohjeessa luki näin</sub></sup>

  Lisäsin myös salasanattoman kirjautumisen tässä vaiheessa.

  >> ssh-copy-id sudonopw@localhost
  >> ssh sudonopw@localhost

  <img width="812" height="254" alt="image" src="https://github.com/user-attachments/assets/b23e2976-4458-40a8-8589-eac19439b55c" />

  Lisätään sudo visudo -tiedostoon seuraava rivi, jotta sudoless-ryhmä saa salasanattoman kirjautumisen.

  %sudoless ALL = (ALL) NOPASSWD: ALL

  <img width="361" height="60" alt="image" src="https://github.com/user-attachments/assets/b16a68a8-a212-42ff-8b5e-8242c75b6570" />

  ## b) Antero. Tee salasanaton, automaattisesti ssh:lla kirjautuva tunnus Ansiblella.

  Aloitin luomalla uuden roolin "eero", jonka alle tulee main.yml tiedosto jolla luodaan uusi käyttäjä.

  <img width="282" height="90" alt="image" src="https://github.com/user-attachments/assets/48186ff7-33e2-4ef9-996e-13d35e57390d" />

  main.yml tiedosto näyttää tältä:

  <img width="537" height="372" alt="image" src="https://github.com/user-attachments/assets/683ef5b5-dc68-4afb-97d1-7d661e843b0d" />
  Mallina on käytetty tunnilla Teron tekemää esimerkkiä asiasta.

  Muutos näytti tapahtuvan, ja sen jälkeen kokeilemaan uutta käyttjäjää:

  <img width="789" height="529" alt="image" src="https://github.com/user-attachments/assets/4fae2957-07b6-4d2a-aa7e-f08e4ec01125" />

  Lopputuloksena luotu uusi käyttäjä "eero" joka voi käyttää sudoa ilman salasanaa.

  <img width="823" height="389" alt="image" src="https://github.com/user-attachments/assets/eec5ba42-a345-4f80-baa0-b6cf7edeb436" />

  ## c) Package. Asenna kaksi pakettia ansiblella.

  <img width="379" height="201" alt="image" src="https://github.com/user-attachments/assets/028b0360-6c5c-4ba7-a305-8a0a4cc76544" />

  Ensimmäinen virheilmoitus:

  <img width="803" height="85" alt="image" src="https://github.com/user-attachments/assets/055cac0f-ae90-43b0-82c4-ab597ca8eb31" />

  Tässä vaiheessa tarkistin, että python3 käytetään oikein, ja ongelma johtuu jostain muusta. 

  <img width="1910" height="765" alt="Näyttökuva 2026-04-07 132838" src="https://github.com/user-attachments/assets/24f1d56b-b7fe-44a2-9571-5ae4bd39a81c" />

  Jouduin kysymään tekoälyltä apua, eli lähetin saman screenshotin ja kysyin missä vika. ChatGPT kertoi, että ongelma ei ole playbookissa vaan päällekkäisyyksissä kohteessa.
  Apt yritti käyttää /etc/apt/sources.list kohteesta useampaa lähdettä, joten kommentoin yhden rivin pois käyttämällä #. Lisäksi siirsin toisen debian-live-media.list disabled tilaan.

  Lopputulos:

  <img width="1205" height="318" alt="image" src="https://github.com/user-attachments/assets/08c0e1d0-b0a0-4a3d-b19b-a680e550a0f5" />

  ## d) File. Kirjoita orjalle useamman rivin mittainen tiedosto Ansiblella. Määrittele sen omistaja, omistava ryhmä ja oikeudet.

  Loin uuden roolin 'addfile' ansibles-kansioon. Main.yml tiedosto näyttää tältä:

  <img width="444" height="239" alt="image" src="https://github.com/user-attachments/assets/a4f4ee0c-c828-4aa5-ac3b-13181099c995" />

  En tiedä, miksi | putki on käytössä, mutta se oli [esimerkissä](https://phoenixnap.com/kb/ansible-create-file)

  Sitten ajoin playbookin ja lopputulos näkyy samassa kuvassa:

  <img width="1229" height="535" alt="image" src="https://github.com/user-attachments/assets/cda68f8d-cc8e-4863-8924-f24f4d362397" />

  Annoin tiedostolle mode : '0644', eli tiedoston omistaja (eerom) saa lukea ja kirjoittaa tiedostoon. Ryhmä (sudo) saa lukea tiedostoa ja muut käyttäjät saavat lukea tiedostoa.

  Toisessa muodossa siis: -rw-r--r--

  ## e) Jotain muuta

  Loin ensimmäisessä kotitehtävässä playbookin, joka uudelleenkäynnistää apache2-demonin. Ansible-doc --list tai sen tiedosto on hyvin sekava minulle, niin en keksi mitään uutta kokeiltavaa.


  ## Lähteet

  Kurssisivu ja tehtävänanto: https://terokarvinen.com/palvelinten-hallinta/

  Karvinen, T. 2026. Sudo without password. Luettavissa: https://terokarvinen.com/passwordless-sudo/ Luettu 7.4.2026

  Munroe, R. 2006. xkcd 149: Sandwitch. Luettavissa: https://xkcd.com/149/ Luettu 7.4.2026

  Karvinen, T. 2026. Passwordless sudo with Ansible. Luettavissa: https://terokarvinen.com/passwordless-sudo-with-ansible/ Luettu 7.4.2026

  Kaplarevic, V. 10.12.2025. How to Create a File in Ansible. Phoenix Nap. Luettavissa: https://phoenixnap.com/kb/ansible-create-file Luettu: 7.4.2026.

  McBrien, S. 10.1.2023. Linux file permissions explained. Red Hat. Luettavissa: https://www.redhat.com/en/blog/linux-file-permissions-explained Luettu: 7.4.2026

  ChatGPT. Chat.openai.com Käytetty paketinhallinnan asennusongelmien kanssa.
  














