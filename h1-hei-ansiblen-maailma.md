# Hei Ansiblen maailma
## x) Lue ja tiivistä
Ensimmäinen [artikkeli](https://terokarvinen.com/ssh-public-key-login-without-password/) käy läpi SSH:n käyttöönoton ja julkisen avaimen käyttöönoton.
Asennus:
> sudo apt-get install -y ssh

> sudo systemctl enable --now ssh

Toiminnan voi testata ssh kayttaja@localhost, sulkeminen exit komennolla.

Julkisen avainparin luonti:
> ssh-keygen # enter enter enter niin saat oletusasetukset

> ssh-copy-id localhost

Kokeile kirjautumalla, pitäisi mennä ilman salasanaa. Lopuksi artikkelissa on myös muutama troubleshooting-komento.

Toinen [artikkeli](https://terokarvinen.com/hello-ansible/) kertoo ansiblen asennuksesta ja käytöstä. Ansible on konfiguraationhallintatyökalu, jolla voi hallita useita palvelimia yhdeltä koneelta. Käytössä kuvaillaan haluttu lopputulos ohjatuille palvelimille, ja ansible tekee muutoksia ainoastaan jos ne ovat tarpeellisia.
Artikkeli käy läpi ansiblen peruskonfiguraatiot:
> hosts.ini <- mihin konfiguraatiot tallennetaan 
> sites.yml <- mihin kohteisiin tehdään ja mitkä roolit tekevät
> main.yml <- lisätään /roles/hello/tasks/ mitä tehdään kohteissa (esimerkissä lisätään tekstitiedosto /tmp kansioon)

Lisäksi käydään läpi muutamia konfiguraatioita helpottavia asioita kuten micro-editori ja tree-työkalu. Lopuksi vielä yleisimpiä vianmäärityksiä, kuten välilyöntien määrä. YAML vaatii aina 2 välilyöntiä, jotta se toimii. Myös salasanan kysyminen voidaan lisätä --ask-become-pass.

## (a Sshecrets. Asenna SSH-demoni ja testaa se kirjautumalla SSH:lla.
> sudo apt-get update
> sudo apt-get install -y ssh
> ssh eerom@localhost
Kysyy salasanaa ja kirjautuminen onnistuu. Poistutaan exit ja tehdään seuraavaksi public key kirjautumisen nopeuttamiseksi.

## b) Pubkey. Automatisoi ssh-kirjautuminen julkisella avaimella
> ssh-keygen # tallennetaan oletusasetukset enterillä
> ssh-copy-id eerom@localhost
Nyt kirjautuminen onnistuu ilman salasanaa.

## c) Hei Ansible. Tee hei maailma ansiblella ja kokeile sitä SSH:n yli.

Luodaan jo kohdassa (x mainittu ympäristö. Tree-komennolla nähdään tämä.

<img width="329" height="236" alt="image" src="https://github.com/user-attachments/assets/d04c9498-6e6d-4df9-b4c5-d2552dbe17d3" />

Tiedostoon main.yml lisätään kohdekansio ja mitä tehdään. Tässä tapauksessa lisätään tiedosto hello-ansible.

<img width="564" height="93" alt="image" src="https://github.com/user-attachments/assets/239d734c-a449-4ee6-bc5f-e8e647eaa14c" />

Ajetaan
> ansible-playbook -i hosts.ini site.yml --ask-become-pass (kysyy sudo-salasanan)

<img width="1159" height="278" alt="image" src="https://github.com/user-attachments/assets/ac4f4adf-25db-4be9-99ba-91786adc33df" />

Kuvasta näkee, että komento meni läpi ja changed: 1. Käydään vielä katsomassa, että tiedosto meni perille.

<img width="620" height="46" alt="image" src="https://github.com/user-attachments/assets/6d1dd5ad-0717-4901-acae-5f8d84725062" />

Ohjeissa mainittiin myös, että voi luoda tiedoston ansible.cfg, johon saa laitettua halutut hostit, ettei joka kerta playbook-komentoon tarvitse lisätä -i hosts.ini. Luodaan tiedosto ja lisätään sinne halutut asetukset.

<img width="429" height="99" alt="image" src="https://github.com/user-attachments/assets/a822f2e9-5b0b-4ff0-99c3-954290a16187" />

Inventory käyttää nyt aina hosts.ini ja alin rivi näyttää playbookia ajettaessa mitä se tekee.

## d)  Vapaaehtoinen bonus, vaikea: kokeile Ansiblella jokin näista asetuksista: paketin asennus, asetustiedosto /etc/ alle, käynnistä jokin demoni, tee uusi käyttäjä. Tarvitset todennäköisesti sudoa, become: true.

Lähdin etsimään tietoa miten tällaiset playbookit tehdään. Löysin ansiblen community documentation sivuilta ensiksi [demonin käynnistyksen](https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/service_module.html), joten ajattelin kokeilla sitä. Valitsin apache2 uudelleenkäynnistettäväksi demoniksi.
Loin uuden roolin apache2 ja sen alle tehtävän, joka käynnistää apache2:n uudelleen.

<img width="630" height="118" alt="image" src="https://github.com/user-attachments/assets/51f1e2ac-3f88-4cb4-99e7-140c201a2c31" />

<img width="431" height="121" alt="image" src="https://github.com/user-attachments/assets/a8fc5433-9927-4554-bd0e-5c0f119efcc0" />

Tämän jälkeen kokeiltiin ajaa playbook apache2.yml. Komento ei toiminut ilman kohteen asettamista aiemmasta konfiguraatiotiedoston muokkaamisesta huolimatta, joten lisäsin komentoon -i hosts.ini.
>ansible-playbook -i hosts.ini apache2.yml --ask-become-pass

<img width="1166" height="472" alt="image" src="https://github.com/user-attachments/assets/693d6d1a-3587-4bea-93cc-9783a92958a1" />

Kuvasta nähdään, mikä tehtävä suoritettiin ja missä kohteessa.

<img width="865" height="326" alt="image" src="https://github.com/user-attachments/assets/9e4e9bb5-ba19-4be3-bf19-2dcf87874b2b" />

Tässä vielä apache2 status, josta näkee että se on ollut käynnissä 12 sekuntia.

## Lähteet

Tehtävänanto ja kurssisivu: https://terokarvinen.com/palvelinten-hallinta/

SSH Public Key - Login without  password: https://terokarvinen.com/ssh-public-key-login-without-password/

Hello Ansible: https://terokarvinen.com/hello-ansible/

ansible.builtin.service module – Manage services: https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/service_module.html

Kaikki lähteet luettu 30.3.2026.
