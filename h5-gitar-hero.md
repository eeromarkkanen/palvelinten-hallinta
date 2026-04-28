# H5 - Gitar Hero

## X) Lue ja tiivistä

### Chacon and Straub 2014: Pro Git, 2ed: 1.3 Getting Started - What is Git?

- Git on versionhallintatyökalu, joka toimii myös paikallisesti
- Toisin kuin muut vastaavat työkalut, git tallentaa aina snapshotin koko projektista, jotta siihen on helppo palata aina tallennettuun kohtaan
- Git:ssä on 3 tilaa, modified, staged ja committed. Modified tilassa editoidaan, add komento lisää ne staged tilaan ja commit tallentaa ne.
- Kaikesta, mitä git:ssä tehdään jää jälki checksum:n muodossa. Tämä voidaan nähdä lokeista.

Git - komennot
- git add --all: lisää kaikki tiedostot staged-tilaan ne voidaan tallentaa commit-komennolla
- git commit: tallentaa snapshotin tilasta paikallisesti
- git push: siirtää tiedostot repositorioon
- git pull: hakee tiedostot repositoriosta ja yhdistää ne paikalliseen repositorioon

  [Siteground: Basic Git Commands](https://www.siteground.com/kb/use-common-git-commands/)
  [Chacon and Straub 2014: Pro Git, 2ed: 1.3 Getting Started - What is Git?](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F)

## A)  Online. Tee uusi varasto GitHubiin (tai Gitlabiin tai mihin vain vastaavaan palveluun). Varaston nimessä ja lyhyessä kuvauksessa tulee olla sana "sunshine".

Loin uuden repositorion GitHubiin ja lisäsin siihen README-tiedoston ja GNUv3 lisenssin.

<img width="1352" height="520" alt="Näyttökuva 2026-04-28 104245" src="https://github.com/user-attachments/assets/66bcb201-8d86-4643-8132-db8e3b439aeb" />


## B) Dolly. Kloonaa edellisessä kohdassa tehty uusi varasto itsellesi, tee muutoksia omalla koneella, puske ne palvelimelle, ja näytä, että ne ilmestyvät weppiliittymään.

Kopioin repositorion virtuaalikoneelle valitsemalla repositorion etusivulta Code -> SSH ja kopioin linkin. SSH-avain luotiin jo tunnilla, enkä käy sitä uudestaan tässä läpi.

<img width="595" height="440" alt="Näyttökuva 2026-04-28 104334" src="https://github.com/user-attachments/assets/a977de72-bce4-4875-bedc-8b2ec1d2c3e8" />

Loin uuden kansion ja kloonasin repositorion sinne.

<img width="891" height="512" alt="Näyttökuva 2026-04-28 104836" src="https://github.com/user-attachments/assets/a43afcc5-0c22-48f6-a780-70b992c4ee67" />

Sitten tekemään muutoksia. Luon vain yksinkertaisen helloworld-tiedoston, jotta muutokset voi nähdä.

<img width="724" height="72" alt="image" src="https://github.com/user-attachments/assets/57117851-b18c-4ab0-b092-dccd8eeabed8" />

Seuraava vaihe on puskea kaikki tiedostot takaisin, jotta muutokset on näkyvillä.

git add --all

git commit

git pull

git push

<img width="611" height="350" alt="image" src="https://github.com/user-attachments/assets/d292dceb-62c4-4fc1-b192-fe44f4e52ce2" />

Tässä muutos myös GitHubin puolella:

<img width="835" height="282" alt="image" src="https://github.com/user-attachments/assets/a990c8d0-4e60-445d-a882-1f90a6e92d3c" />


## C) Doh! Tee tyhmä muutos gittiin, älä tee commit:tia. Tuhoa huonot muutokset ‘git reset --hard’.

Muokkasin äsken luotua tiedostoa, jotta voin poistaa tehdyt muutokset.

<img width="737" height="80" alt="image" src="https://github.com/user-attachments/assets/ab61cf31-cedd-403b-8920-baa9879291f9" />

Git status -komennolla näkee onko tallentamattomia muutoksia.

<img width="755" height="237" alt="image" src="https://github.com/user-attachments/assets/7b2ddc96-9c09-4520-85bc-18a445b2a4a6" />

Ajetaan 'git reset --hard' ja tarkistetaan uudelleen. Kuva kertoo, että repositorio palasi viimeiseen tallennettuun kohtaan eli 'Add Hello World.'

<img width="626" height="170" alt="image" src="https://github.com/user-attachments/assets/f6716dde-a4d8-4228-b8c3-3207044e7969" />

Ja myös tekstitiedosto oli palannut alkuperäiseen:

<img width="715" height="67" alt="image" src="https://github.com/user-attachments/assets/eda11f1a-9c0d-46ed-b620-f9ff6d6488c3" />


## D) Tukki. Tarkastele ja selitä varastosi lokia. Näytä myös, mitä muutoksia tiedostoihin on tehty.

Katselin lokia komennoilla git log ja git log --patch. Lokeista näkyy tehdyt commitit, milloin ne on tehty ja kuka ne on tehnyt. Jokaisella on commitilla on myös oma checksum.

Git log näyttää lyhyesti tehdyt commitit:

<img width="909" height="286" alt="image" src="https://github.com/user-attachments/assets/24426405-03b1-49aa-8e7b-139888052448" />

Git log --patch (tai -p) näyttää lisätietoja:

<img width="920" height="788" alt="image" src="https://github.com/user-attachments/assets/ddfcfb6b-920d-4b91-9e8b-a8c261e5b788" />

Vihrellä tekstillä näkyy, mitä tiedostoihin on lisätty. Jos tiedostosta olisi poistettu jotain, se näkyisi punaisella.


## E) Gitanbile. Laita Ansible-kansio versionhallintaan. Tee jokin muutos, aja ansiblella, tallenna versio (commit).

Kopioidaan ansibles-kansio tähän samaan kansioon:

<img width="722" height="73" alt="image" src="https://github.com/user-attachments/assets/5c67d501-d95b-4691-8b76-334c9adb6f48" />

cp (copy) -r (kaikki kansiot) /polku/ . (kohdekansio tämä kansio)

Sitten tehtiin muutoksia ansibles-kansion alle. Muokkasin site.yml tiedostoa ottamalla näistä kohdista # pois:

<img width="261" height="279" alt="image" src="https://github.com/user-attachments/assets/521951fd-dcfe-4ba0-8f70-4e8a2ea29f69" />

Ajoin playbookin ansible-playbook site.yml -K. Tässä ei ihan hirveästi tapahtunut muutoksia, mutta jotain edes:

<img width="1704" height="688" alt="image" src="https://github.com/user-attachments/assets/f1b71e4e-b588-496c-a109-a3c8eeab84e5" />

Katsotaan mitä git status sanoo:

<img width="775" height="643" alt="image" src="https://github.com/user-attachments/assets/e9bee524-8eb4-47d3-b998-b9edbb04aa30" />

Löytyy yksi muokattu tiedosto, joka täytyy lisätä git add komennolla valmiiksi committiin.

<img width="662" height="467" alt="image" src="https://github.com/user-attachments/assets/d58b80be-ddd6-4fb5-b1a0-e0c14c1727b3" />

Tässä kohtaa unohtui ottaa screenshot asiasta, mutta yllä näkyy että myös muokattu site.yml -tiedosto on commit-listalla.

Tarkistin vielä lopuksi git status:

<img width="555" height="148" alt="image" src="https://github.com/user-attachments/assets/00bd1f18-11ce-45fd-a1ae-0d317f0b88f5" />


## F) Hae pari projektiin Moodlen keskustelusta.

Viesti lähetetty Moodlessa, odotetaan vahvistusta.


## Lähteet

Tehtävänanto ja kurssisivu. https://terokarvinen.com/palvelinten-hallinta/

Chacon and Straub 2014: Pro Git, 2ed: 1.3 Getting Started - What is Git?. Luettavissa: https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F Luettu 28.4.2026.

Siteground. Common Git Commands. Luettavissa: https://www.siteground.com/kb/use-common-git-commands/ Luettu 28.4.
