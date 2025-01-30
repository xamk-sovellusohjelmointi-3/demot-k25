# Demo 1: EJS-perusteet

Demossa on toteutettu Ostoslista-sovelluksen pohja käyttäen palvelinpään renderöintiä ja EJS-templaattimoottoria. Sovelluksen Prisma ORM -tietokanta on toteutettu Sovellusohjelmointi 2 -opintojakson vastaavan Ostoslista-demon pohjalle ja sen avulla havainnollistetaan tietojen hakemista palvelimen kautta tietokannasta ja dynaamista liittämistä EJS-templaten HTML-rakenteeseen ennen vastauksen lähettämistä käyttäjän selaimelle.

Sovellus ei vielä sisällä reitityksiä tai toiminnallisuutta Ostoslistan käyttämiselle. Toiminnallisuudet on rakennettu Demo 2:n alle.

Voit lukea tämän README-tiedoston GitHubissa tai Visual Studio Code:ssa painamalla `Ctrl+Shift+V` tai painamalla tiedostoa hiiren kakkospainikkeella ja valitsemalla "Open Preview". Tämä voi tehdä ohjeiden lukemisesta helpompaa.

## Selityksiä Demo 1 -sovelluksessa olevista koodeista

### index.ts

EJS on otettu käyttöön asentamalla se `npm install ejs` -komennolla normaalina riippuvuutena. Lisäksi on asennettu EJS:n TypeScript tyypit kehitysriippuvuutena komennolla `npm install @types/ejs -D`. EJS on asetettu Express-sovelluksen näkymämoottoriksi `index.ts` -tiedoston rivillä 10 komennolla:

```typescript
app.set("view engine", "ejs");
```
EJS-templatet on otettu käyttöön lisäämällä palvelimen juureen `views`-kansio, johon `.ejs`-päätteiset EJS-template -tiedostot tallennetaan. Express osaa automaattisesti hakea EJS-templateja `views`-kansiosta, niitä kutsuttaessa.

EJS-template voidaan palauttaa REST API -pyynnön vastauksena Expressin `res.render`-metodilla, kuten `index.ts`-tiedoston riveillä 12-19:

```typescript
app.get("/", async (req : express.Request, res : express.Response) => {

    let ostokset = await prisma.ostos.findMany();

    res.render("index", { ostokset, ostokset});
});
```

Prisma-tietokanta on toteutettu paikallisena tietokantatiedostona ja se on myös populoitu valmiiksi alustavilla tiedoilla, eli sinun ei tarvitse itse luoda tietokantaa. Voit tarkastella ja muokata tietokantaa Prisma Studiolla avaamalla uuden komentokehotteen (Terminal) ja suorittamalla komennon `npx prisma studio`. Prisma Studio avautuu omaan porttiinsa ja siellä on valmiiksi tietokantataulu Ostos-mallille. Voit lisätä, poistaa ja muokata rivejä ja nähdä muutokset selaimessa Ostoslista-sovelluksessa.

### index.ejs

index.ejs on EJS-template, jota käytetään sovelluksen ostoslistan renderöintiin. Koska sovelluksessa käytetään EJS-templateja, voidaan HTML-sisältöä muodostaa dynaamisesti JavaScriptin avulla. Tässä esimerkissä Prisma-tietokannan sisältämät ostokset haetaan ohjelmallisesti `index.ts` -tiedoston juuren REST API GET -pyynnössä ja lähetetään parametrina renderöitävälle `index.ejs` -templatelle.

Varsinainen tietokannasta saatujen tietojen käsittely toteutetaan osana EJS-templaten koodia. Parametrina saadut `ostokset` tiedot käydään ohjelmallisesti läpi forEach-loopin avulla ja jokaisesta ostos-alkiosta luodaan oma listaelementti. Tuotteen nimi haetaan JavaScriptin avulla käsiteltävän alkion tiedoista ja tulostetaan osana HTML-elementtiä. (Rivit 59 - 74)

```html
 <ul class="mdl-list">

    <% ostokset.forEach((ostos) => { %>

        <li class="mdl-list__item">
            <span class="mdl-list__item-primary-content">
                <%= ostos.tuote %>
            </span>
            <button class="mdl-button mdl-js-button mdl-button--icon mdl-list__item-secondary-action">
                <i class="material-icons">delete</i>
            </button>
        </li>

    <% }) %> 

</ul>
```

EJS-templatessa JavaScript-koodi on upotettu `<% %>` -tagien sisälle. `<% %>` -tageilla voidaan luoda sekä yhden rivin, että useamman rivin mittaisia JavaScript-koodeja.

```html
<% let viesti = "Yhden rivin JavaScript-koodi, jossa määritellään muuttuja viesti."; %>

<%

    let toinenViesti = "Useamman rivin JavaScript-koodi, jossa määritellään kaksi muuttujaa ja console.log-tulostus";
    let luku = 2;

    console.log(`Ensimmäinen viesti: ${viesti}`);
    console.log(`Toinen viesti: ${toinenViesti}`);
    console.log(`Luku: ${luku}`);

%>
```

EJS-templatessa voidaan käyttää myös ohjausrakenteita tulostamaan HTML-sisältöä riippuen jostain ehdosta:

```html
<% 

let ehto = true;

if (ehto) {

%>

<h1>Ehto on tosi</h1>

<% } else { %>

<h1>Ehto on epätosi</h1>

<% } %>
```

JavaScript-koodia voidaan myös tulostaa HTML-sisältöihin käyttämällä `<%= %>` -tageja. Voidaan esimerkiksi tulostaa jokin muuttuja osana HTML-elementtiä:

```html
<% let viesti = "Heippa!"; %>

<h1><%= viesti%></h1>

<!-- Näytetään "Heippa!" h1-elementissä. -->
```

EJS-templaten muotoiluun käytetään Googlen Material Design Lite -kirjastoa ja ikonit saadaan Googlen Material Icons -kirjastosta. Fonttina käytetään Googlen Roboto-fonttia.

Muotoiluihin löydät ohjeistuksen kirjastojen dokumentaatioista, eikä niiden käyttöä käydä tässä erikseen. Voit EJS-template -koodia tarkastelemalla verrata, miten kirjastojen komponentteja käytetään muotoilemaan template-sivujen sisältöä.

Tuotujen muotoilujen lisäksi sovellusta on muotoiltu omilla tyyleillä, jotka määritellään `index.ejs` -tiedoston `head`-elementin sisällä heti tuotujen muotoilujen jälkeen.
