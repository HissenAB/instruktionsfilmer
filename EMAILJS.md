# EmailJS: nycklar och behörigheter

## Vad repot visar

`i.html` och flera demos använder samma publika EmailJS-nyckel. Index använder
nu den nyckeln och initierar EmailJS först när ett förslag skickas. En blockerad
mejltjänst ska inte hindra filmer eller kategorier från att fungera.

Den publika nyckeln identifierar kontot; den är avsedd för webbläsaren och är
inte ett lösenord. Att flytta den till en JavaScript-fil, obfuskera den eller
lägga den i en byggvariabel/GitHub Secret som skrivs till HTML döljer den inte
för besökare. Ingen privat EmailJS-nyckel behövs i denna statiska sida.

## Kontrollera befintligt skydd i EmailJS

Kontots faktiska inställningar har inte kunnat verifieras från den här sessionen.
De finns inte i repot. Följande är kontrollpunkter, inte bekräftade inställningar:

1. Öppna **Account → Security** i [EmailJS Dashboard](https://dashboard.emailjs.com/).
   Kontrollera tillåtna origins/domäner. Begränsa till den faktiska publicerade
   sidans origin (t.ex. `https://hissenab.github.io` om GitHub Pages används).
   Origin innehåller protokoll och värdnamn, inte sökvägen `/instruktionsfilmer/`.
   Använd inte jokertecken och lämna inte testdomäner tillåtna i produktion.
2. Öppna mallen `template_341abdv` och kontrollera att **To Email**, CC och BCC
   är fasta avsedda mottagare. Låt inte besökaren ange mottagare via mallvariabler.
   Förslaget ska bara vara innehåll i den fördefinierade mallen.
3. Kontrollera mallens **Settings → Enable reCAPTCHA V2 verification**.
   Om detta redan är aktiverat behöver formuläret även en reCAPTCHA v2-widget
   och måste skicka token som `g-recaptcha-response`. Nuvarande formulär gör
   inte det. Aktivera därför inte kravet utan att samtidigt koppla in widgeten.
   reCAPTCHA-hemligheten hör hemma i EmailJS, aldrig i HTML/JavaScript.
4. Kontrollera användning och oväntade utskick i kontot. Sidans gräns på tre
   förslag per dag i `localStorage` är endast en användargräns; besökare kan
   kringgå den. Den bevisar inte att kontot är skyddat.

En origin-lista och CAPTCHA begränsar missbruk, men autentiserar inte anställda.
Alla som kan öppna en offentlig sida kan i princip använda dess formulär.

## Om endast behöriga användare ska kunna skicka

För detta krävs en backend/serverless-endpoint som verifierar inloggning och
behörighet, har serverbaserad hastighetsbegränsning och validerar förslaget.
Backend använder en privat EmailJS-nyckel från serverns secret store, medan
klienten anropar backend. EmailJS måste då också konfigureras att kräva privat
auktorisering och inte lämna den gamla publika sändvägen öppen. Att endast
flytta anropet till en backend utan att stänga direktanrop skyddar inte funktionen.
GitHub Pages kör inte serverkod, så en sådan endpoint behöver separat hosting.

Om en **privat** nyckel någon gång har publicerats ska den återkallas/roteras i
EmailJS. Att radera den från senaste HTML-versionen räcker inte.

## Källor

- [Publika nycklar](https://www.emailjs.com/docs/faq/is-it-okay-to-expose-my-public-key/)
- [Skydd mot missbruk och origin-lista](https://www.emailjs.com/docs/faq/does-emailjs-expose-my-account-to-spam/)
- [reCAPTCHA v2](https://www.emailjs.com/docs/user-guide/adding-captcha-verification/)
- [SDK-alternativ och privat auktorisering](https://www.emailjs.com/docs/sdk/options/)
- [REST API: public key och accessToken](https://www.emailjs.com/docs/rest-api/send/)
