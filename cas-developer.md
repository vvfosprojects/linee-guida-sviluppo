# CAS - DEVELOPER REFERENCE

# Indice

[1 Introduzione](#1)

[2 Federazione dei Servizi](#2)

[3 Develop](#3)

[4 Document Info](#4)

---


# 1 
## Introduzione
Il presente Developer Reference Document fornisce indicazioni agli sviluppatori che intendono federare un applicativo web integrando il sistema CAS Enterprise Single Sign-On, rilasciato all’indirizzo https://sso.vigilfuoco.it/cas-test/.

## 1.1 
### Scopo del documento
Lo scopo del documento è quello di illustrare agli sviluppatori, dopo una breve introduzione su come i servizi vengono aggiunti a CAS,  alcuni esempi di integrazione in diversi linguaggi di programmazione e diverse architetture software.

## 1.2 
### Abbreviazioni e acronimi

| Nome | Descrizione |
| --- | ----- |
| C.A.S | Central Authentication Service  |
| JSON | JavaScript Object Notation |
| JWT | JSON Web Token |

## 1.3 
### Riferimenti
I file presenti tra i seguenti riferimenti sono allegati a questo documento.

| # | Identificativo documento | Titolo |
| --- | ----- | ------ |
| 1 | CAS | https://apereo.github.io/cas |
| 2 | cas_flow_diagram | https://apereo.github.io/cas/6.1.x/protocol/CAS-Protocol.html#web-flow-diagram |
| 3 | cas_flow_jwt_diagram | https://apereo.github.io/cas/6.1.x/installation/Configure-ServiceTicket-JWT.html#flow-diagram |
| 4 | cas_proxy_flow_diagram | https://apereo.github.io/cas/6.1.x/protocol/CAS-Protocol.html#proxy-web-flow-diagram
| 5 | Central Authentication Service | https://en.wikipedia.org/wiki/Central_Authentication_Service |
| 6 | SAML2 protocol | https://apereo.github.io/cas/5.3.x/installation/Configuring-SAML2-Authentication.html |


## 1.4 
### Glossario

| Nome | Descrizione |
| --- | ----- |
| Servizio | Applicativo, Web Application o RESTful Service federato nel SSO
| CAS-Server | Applicativo CAS che si occupa di autenticare le richeste dei CAS-Client
| CAS-Client | Servizi federati al CAS-Server

---

# 2 
## Federazione dei Servizi

## 2.1 
### Attuale configurazione Server CAS-Test
Di seguito le caratteristiche principali dell’applicativo CAS installato in ambiente di test
- versione : 5.2.5
- url : https://sso.vigilfuoco.it/cas-test/
- port : 443
- protocollo : CAS 3



## 2.2 
### Federazione di un servizio 
CAS mette a disposizione diverse modalità di configurazione dei servizi federati. Quella più rapida consiste nella creazione di un file JSON che contiene una serie di proprietà che servono a definire dettagli sul servizio che deve essere federato, le politiche di sicurezza, l’abilitazione o meno del servizio e così via. Vedi la lista completa nella documentazione ufficiale  
Di seguito un esempio di file di configurazione per un applicativo:

```JSON
{ 
    "@class" : "org.apereo.cas.services.RegexRegisteredService", 
    "serviceId" : "^https://gifwf-test.dipvvf.it/.*", 
    "name" : "GIFTest", 
    "id" : 100000061, 
    "description" : "GIF WF Test Applications", 
    "evaluationOrder" : 6,
    "accessStrategy" : { 
        "@class" : "org.apereo.cas.services.DefaultRegisteredServiceAccessStrategy", 
        "enabled" : true, 
        "ssoEnabled" : true, 
    }, 
    "logoutType" : "BACK_CHANNEL", 
    "logoutUrl" : "https://gifwf-test.dipvvf.it/GIF/logout"
}
```

La proprietà serviceId definita come una regex è riferita allo URL del Servizio che si vuole abilitare. Il nome del file deve essere costituito dalla proprietà [name]-[id].json quindi il file JSON per l’esempio sopra descritto sarà GIFTest-10000061.json.
I file JSON di configurazione vanno inseriti by default nella cartella src/main/resources/services o in un’altra cartella alias definita a priori nel file di configurazione di CAS.


### 2.2.1 
#### Dati richiesti per la federazione di un servizio

Per utilizzare CAS-TEST è necessario fornire al team di assistenza SSO:
- Esempi URL applicazione 
    - \[http|https]://your.domain.it/callback; 
    - \[http|https]://localhost:[port]/yourappname/callback
    - \[http|https]://localhost:[port]/



## 2.3 
### Endpoints di login, validazione e logout

Per effettuare il login, in accordo con il CAS Browser Sequence Diagram, l’applicativo deve redirigere l’utente al seguente link (sempre prendendo in considerazione il servizio definito al paragrafo 2.2):
- https://sso-vigilfuoco.it/cas-test/login?service=https://gifwf-test.dipvvf.it/callback 

Il path callback è una pagina del Servizio che si occuperà di validare il ticket che l’utente presenterà attraverso il browser dopo aver effettuato l’operazione di login.

L’endpoint di validazione:
- https://sso-vigilfuoco.it/cas-test/p3/serviceValidate?service=https://gifwf-test.dipvvf.it/callback&format=JSON&ticket=ST-001-xxxxxxxxxxxxxxxxxxxxxxxxxxxxx-vfcas-test-e01
- https://sso-vigilfuoco.it/cas-test/validate?service=https://gifwf-test.dipvvf.it/callback&format=JSON&ticket=ST-001-xxxxxxxxxxxxxxxxxxxxxxxxxxxxx-vfcas-test-e01

L’endpoint di logout:
- https://sso-vigilfuoco.it/cas-test/logout?service=https://gifwf-test.dipvvf.it/


## 2.4 
### Utenti
Gli utenti possono effettuare il login utilizzando [nome].[cognome]@dipvvf.it oppure in alternativa utilizzare SPID.
Le aziende esterne possono creare degli utenti al seguente link https://external-users-test.vigilfuoco.it/user/register


## 2.5 
### Dati validazione
A seguito di validazione positiva il server CAS ritorna, in base all’autenticazione utilizzata, i seguenti dati (un esempio):

### Ticket ST 

Autenticazione DIPVVF

```js
{
id: nome.cognome@dipvvf.it, 
attributes: { 
	credentialType=UsernamePasswordCredential, 
	isFromNewLogin=true, 
	authenticationDate=2019-11-19T12:22:51.133+01:00[Europe/Rome], 
	authenticationMethod=LdapAuthenticationHandler, 
	successfulAuthenticationHandlers=LdapAuthenticationHandler, 
	UserPrincipalName=nome.cognome@dipvvf.it, 
	longTermAuthenticationRequestTokenUsed=false, 
	sn=COGNOME, 
	sAMAccountName=FISCALCODE,
	cn=COGNOME NOME
	}
}
```


Autenticazione SPID

```js
{
id: FISCALCODE, 
attributes: {
	ivaCode=-, 
	placeOfBirth=CODE, 
	address=VIA NUMERO CAP CITTA PROV, 
	isFromNewLogin=true, 
	authenticationDate=2019-11-19T12:28:26.481+01:00[Europe/Rome], 
	clientName=SAML2Client, companyName=-, 
	successfulAuthenticationHandlers=ClientAuthenticationHandler, 
	spidCode=SPIDCODE, 
	dateOfBirth=DATE, 
	credentialType=ClientCredential, 
	mobilePhone=+390000000000, 
	uthenticationMethod=ClientAuthenticationHandler, 
	familyName=COGNOME, 
	digitalAddress=-, 
	name=NOME, 
	longTermAuthenticationRequestTokenUsed=false, 
	countyOfBirth=PROV, 
	fiscalNumber=TINIT-FISCALCODE, 
	sessionindex=2ae0a229-be20-4e38-967b-44706adb9270, 
	email=EMAIL
	}
}
```

---

# 3 
## Develop

## 3.1 
### Concetti preliminari
Nell’architettura CAS il Client è una applicazione web che interagirà con il Server CAS attraverso uno dei protocolli che il Server CAS implementa. Il protocollo attualmente implementato su CAS-TEST è CAS3. 
Attenzione, CAS è un servizio di autenticazione. La profilazione autorizzativa degli utenti è, e rimane un processo interno all'applicazione sviluppata.

## 3.2 
### Architetture software
In base al tipo di architettura software utilizzata per sviluppare l’applicativo, MVC o di tipo App (frontend e backend disaccoppiati) è possibile utilizzare modalità differenti di validazione.

Nelle applicazione MVC classiche il Client non appena ricevuto dal browser il Service Ticket (ST), che il Server CAS ha fornito a seguito del login, lo verifica sul Server CAS stesso per verificare l’attendibilità e riceve in caso di validazione positiva un XML (o JSON) contenente le informazioni su Principal/Attributes dell’utente loggato.

 1.	Un utente tenta di accedere all’area riservata un una web application.
 2.	La web application effettua un redirect al login CAS.
 3.	CAS, utilizzando cookies e sessions standard, determina se l’utente è già loggato.
 4.	Se l’utente non è loggato, CAS mostra il form di login. Se l’utente è già loggato salta al punto 7.
 5.	CAS interagendo con l’identity provider verifica la validità delle credenziali inserite.
 6.	Se le credenziali sono valide CAS autentica l’utente.
 7.	A questo punto CAS effettua un redirect alla web application, fornento un ticket ST.
 8.	La web application effettua una chiamata diretta a CAS per validate il ticket ricevuto.
 9.	Se il ticket è valido, CAS ritorna in risposta alla richiesta le informazioni dell’utente inserite nel formato richiesto.
 10. La web application a questo punto crea una sessione autenticata per l’utente, fornendo eventuale autorizzazione sfruttando le info sull’utente autenticato fornite da CAS, quindi redirige verso la pagina appropriata.


Nelle architetture di tipo App o SPA (mobile, Angular, react-native ecc) il flow è come segue.

 1.	Un utente tenta di accedere all’area riservata un una SPA.
 2.	La SPA effettua un redirect al login CAS.
 3.	CAS, utilizzando cookies e sessions standard, determina se l’utente è già loggato.
 4.	Se l’utente non è loggato, CAS mostra il form di login. Se l’utente è già loggato salta al punto 7.
 5.	CAS interagendo con l’identity provider verifica la validità delle credenziali inserite.
 6.	Se le credenziali sono valide CAS autentica l’utente.
 7.	A questo punto CAS effettua un redirect alla SPA, fornento un ticket ST.
 8.	L’applicazione ANGULAR SPA effettua una chiamata all’endpoint di autenticazione del Backend  fornendo il ticket ST
 9.	Il Backend effettua una chiamata diretta a CAS per validate il ticket ricevuto.
 10.	Se il ticket è valido, CAS ritorna in risposta alla richiesta le informazioni dell’utente inserite nel formato richiesto.
 11. Il Backend a questo punto genera un JWT di sessione che fornisce alla SPA dell’utente autenticato.
 12. La SPA inserirà il JWT ricevuto dal Backend nelle richieste successive.



## 3.3 
### Linee guida per gli sviluppatori
Per l’utilizzo del SSO dipartimentale una best practice è quella di inserire l’indirizzo URL del CAS, che viene utilizzato come prefisso nelle chiamate di login/validazione/logout, in una proprietà contenuta in un file di configurazione accessibile dai sistemisti, ad esempio all’interno del file application.properties in una applicazione JAVA.

## 3.4 
### Esempi codice
Di seguito alcune librerie e snippets in vari linguaggi per l’integrazione dell’autenticazione SSO con CAS.

### 3.4.1 
#### Java
Libreria PAC4J reperibile con esempi e casi d’uso su https://github.com/pac4j

### 3.4.2 
#### dot.net core
Snippet per la decodifica del JWS fornito da CAS-TEST

```csharp
using System;
using System.Text;

// packages required
// joes-jwt-signed
// BCrypt-Core

public class Program
{
	public static void Main()
	{
		
		string token ="";
		string password = "";

		byte[] secretKey = Encoding.ASCII.GetBytes(password);

		string payload = Jose.JWT.Decode(token, secretKey);
		
		Console.WriteLine("---------------------------");
		Console.WriteLine("ticket:" + token);
		Console.WriteLine("password:" + password);
		Console.WriteLine("---------------------------");
		Console.WriteLine("Payload:" + payload);
		Console.WriteLine("---------------------------");
	}
}
```

### 3.4.3
#### C#
Di seguito uno snippet fornito da II Claudino Di Ciocco
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Xml;
 
namespace PoaApp
{
    /// <summary>
    /// Descrizione di riepilogo per Accesso
    /// </summary>
    public class Accesso : IHttpHandler
    {
 
        public void ProcessRequest(HttpContext context)
        {
            context.Response.ContentType = "text/plain";
            context.Response.Write("Accesso");
 
            using (System.Net.WebClient client = new System.Net.WebClient())
            {
 
                string url = System.Web.Security.FormsAuthentication.LoginUrl.Replace ("/login?", "/serviceValidate?") + "&ticket=" + context.Request.QueryString["ticket"];
 
 
                string t = client.DownloadString(url);
                XmlDocument document = new XmlDocument();
                document.LoadXml(t);
 
                XmlNamespaceManager nsmgr = new XmlNamespaceManager(document.NameTable);
                nsmgr.AddNamespace("cas", "http://www.yale.edu/tp/cas");
 
                XmlNode auth = document.DocumentElement.SelectSingleNode("//cas:authenticationSuccess", nsmgr);
                if (auth != null)
                {
                    XmlNode User = document.DocumentElement.SelectSingleNode("//cas:user", nsmgr);
                   if (User != null)
                    {
                        System.Web.Security.FormsAuthentication.RedirectFromLoginPage(User.InnerText, false);
                    }
                }
                else
                    context.Response.Redirect(System.Web.Security.FormsAuthentication.LoginUrl);
            }
        }
 
        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

Web.config
```xml
<?xml version="1.0" encoding="utf-8"?>
 
<!--
  Per altre informazioni su come configurare l'applicazione ASP.NET, vedere
https://go.microsoft.com/fwlink/?LinkId=169433
  -->
<configuration>
  <system.web>
    <compilation debug="true" targetFramework="4.7.2"/>
    <httpRuntime targetFramework="4.7.2"/>
    <authentication mode="Forms">
      <!--<forms  loginUrl="https://sso.vigilfuoco.it/cas/login?service=http%3A%2F%2Fsvil.rn.dipvvf.it%2FAccesso.ashx"></forms>-->
    <forms  loginUrl="https://sso.vigilfuoco.it/cas-test/login?service=http://svil.rn.dipvvf.it/%2FAccesso.ashx"></forms>
    </authentication>
  </system.web>
  <system.codedom>
    <compilers>
      <compiler language="c#;cs;csharp" extension=".cs"
        type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"
        warningLevel="4" compilerOptions="/langversion:default /nowarn:1659;1699;1701"/>
      <compiler language="vb;vbs;visualbasic;vbscript" extension=".vb"
        type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.VBCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"
        warningLevel="4" compilerOptions="/langversion:default /nowarn:41008 /define:_MYTYPE=\&quot;Web\&quot; /optionInfer+"/>
    </compilers>
  </system.codedom>
 
</configuration>
```

### 3.4.4 
#### PHP
Utilizzare la libreria jasig/phpcas disponibile su https://github.com/apereo/phpCAS.git

[<<- Torna al menù](#Indice)

---

# 4 
## Document Info
**Isp. Inf. Giuseppe Gambino** [*giuseppe.gambino [@] vigilfuoco. it*]

**Versione**  1.2.0


[<<- Torna al menù](#Indice)