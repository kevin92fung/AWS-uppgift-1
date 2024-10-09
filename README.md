# AWS - Inlämningsuppgift 1

## Skapa en robust, säker och skalbar hosting-miljö för en webbapplikation

För att följa denna guide och skapa en robust, säker och skalbar hosting-miljö för en webbapplikation som innehåller ditt namn behöver följande tjänster och applikationer vara konfigurerade och installerade:

- **[Visual Studio Code](https://code.visualstudio.com/Download)**: Textredigerare för att hantera kod.
- **[Registrera ett AWS-konto](https://aws.amazon.com/free/)**: För att komma åt AWS-tjänster.
- **[AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)**: Kommandoradsverktyg för att interagera med AWS-tjänster (kan kräva installation av **[Python](https://www.python.org/downloads/)** för att fungera korrekt).

Notera att **AWS CLI** kan kräva installation av **Python** för att fungera beroende på ditt operativsystem och den version av CLI du använder.


## Sätta upp ett IAM-konto för AWS CLI

För att arbeta säkert med AWS CLI skapar vi en IAM-användare för att begränsa rättigheterna och minimera riskerna med att använda root-kontot. Följ dessa steg för att sätta upp ett säkert IAM-konto:

1. **Logga in på AWS**: Logga in med ditt root-konto på AWS.
2. **Gå till IAM**:
   - Leta upp **IAM** via sökfältet.
   - Klicka på **IAM** för att komma till IAM Dashboard.
3. **Skapa en användare**:
   - På IAM Dashboard, gå till **Users** i menyn till vänster.
   - Klicka på **Create user**.
   - Ange ett namn för användaren (t.ex. `AWS-CLI`).
4. **Skapa en användargrupp**:
   - Vid skapandet av användaren, välj att skapa en ny grupp (t.ex. `CLI`).
   - Lägg till specifika rättigheter för denna grupp för att begränsa användarens åtkomst:
     - `AdministratorAccess`: För att hantera alla AWS-resurser.
     - `AWSCodeCommitFullAccess`: För att arbeta med CodeCommit.
5. **Skapa användaren**: Slutför skapandet av användaren.
6. **Generera en access key**:
   - Gå till användarens **Security credentials**.
   - Skapa en ny **Access key** med **Use case: Command Line Interface (CLI)**.
   - Lägg till en beskrivning för access keyn, t.ex. "aws cli".
   - Anteckna **Access key** och **Secret access key**, då dessa används i nästa steg.

### Installation och konfiguration av AWS CLI

Efter att du har installerat AWS CLI, behöver du konfigurera det för att kunna arbeta med dina AWS-tjänster:

1. Öppna terminalen i **Visual Studio Code**.
2. Kör kommandot:
   ```bash
   aws configure
   ```
3. Fyll i följande information:
   - **AWS Access Key ID**: Ange **Access_Key** från IAM-kontot.
   - **AWS Secret Access Key**: Ange **Security_Access_Key** från IAM-kontot.
   - **Default region name**: Ange `eu-west-1` (för att hålla sig inom samma Free Tier-region som guiden).
   - **Default output format**: Ange `json` för att få svar i JSON-format.

Genom att använda en IAM-användare med begränsade rättigheter, kan du arbeta säkert och förhindra att obehörig åtkomst ges till ditt AWS-konto.