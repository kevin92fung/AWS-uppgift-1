# AWS - Inlämningsuppgift 1

## Innehållsförteckning
- [Skapa en robust, säker och skalbar hosting-miljö för en webbapplikation](#skapa-en-robust-säker-och-skalbar-hosting-miljö-för-en-webbapplikation)
- [Sätta upp ett IAM-konto för AWS CLI](#sätta-upp-ett-iam-konto-för-aws-cli)
- [Installation och konfiguration av AWS CLI](#installation-och-konfiguration-av-aws-cli)
- [CloudFormation Template](#cloudformation-template)

## Skapa en robust, säker och skalbar hosting-miljö för en webbapplikation

För att följa denna guide och skapa en robust, säker och skalbar hosting-miljö för en webbapplikation som innehåller ditt namn behöver följande tjänster och applikationer vara konfigurerade och installerade:

- **[Visual Studio Code](https://code.visualstudio.com/Download)**: Textredigerare för att hantera kod.
- **[Registrera ett AWS-konto](https://aws.amazon.com/free/)**: För att komma åt AWS-tjänster.
- **[AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)**: Kommandoradsverktyg för att interagera med AWS-tjänster (kan kräva installation av **[Python](https://www.python.org/downloads/)** för att fungera korrekt).

Notera att **AWS CLI** kan kräva installation av **Python** för att fungera beroende på ditt operativsystem och den version av CLI du använder.

### Verifiera installationen av AWS CLI

För att kontrollera att AWS CLI har installerats korrekt, kan du köra följande kommando i terminalen:

```bash
aws --version
```
[Till toppen](#aws---inlämningsuppgift-1)

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

[Till toppen](#aws---inlämningsuppgift-1)

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

Genom att använda en IAM-användare med begränsade rättigheter kan du arbeta säkert och förhindra att obehörig åtkomst ges till ditt AWS-konto.

[Till toppen](#aws---inlämningsuppgift-1)
## CloudFormation Template

I denna guide kommer vi också att använda oss av en **CloudFormation Template** där vi beskriver de resurser som krävs för att skapa vår hosting-miljö. En **CloudFormation Template** är skriven i **YAML**, vilket står för "YAML Ain't Markup Language". YAML är ett lättläst format för att skriva konfigurationer, vilket gör det enkelt att definiera strukturer och hierarkier.

En **CloudFormation Template** består av olika delar, såsom parametrar, resurser och utdata:

- **Parametrar**: Definierar ingångsvärden som kan anpassas när templaten körs.
- **Resurser**: Specifierar de AWS-resurser som ska skapas, såsom EC2-instanser, S3-buckets och databaser.
- **Utdata**: Anger vad som ska visas som resultat efter att templaten har körts, t.ex. IP-adresser och andra viktiga informationer om skapade resurser.

Nedan är en tom CloudFormation-template som vi kommer fylla i steg för steg under guiden. Varje del av templaten beskriver olika aspekter av infrastrukturen som vi skapar. Du kommer att fylla i parametrar och resurser allt eftersom vi bygger upp din hosting-miljö.

Börja med att skapa en ny fil för CloudFormation-templaten. Du kan namnge filen `CloudFormation.yaml`.

I denna fil kommer vi att definiera en CloudFormation-template som skapar en säker och skalbar hosting-miljö för en webbapplikation. Denna template kommer att inkludera följande resurser:

- **Security Group**: För att hantera säkerhetsregler för instanserna.
- **Launch Template**: För att definiera konfigurationen av instanserna som ska lanseras.
- **Target Group**: För att gruppera instanser som hanteras av load balancer.
- **Application Load Balancer**: För att hantera och fördela trafiken mellan instanser.
- **Auto Scaling Group**: För att automatiskt skala upp och ner antalet instanser baserat på belastning.

Nedan är innehållet för filen:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Denna template skapar en säker och skalbar hosting-miljö för en webbapplikation.

Parameters:
  # Definiera ingångsparametrar som kan anpassas vid körning av templaten

Resources:
  # Security Group
  # Launch Template
  # Target Group
  # Application Load Balancer
  # Auto Scaling Group

Outputs:
  # Anger vad som ska visas som utdata efter att templaten körts
```
[Till toppen](#aws---inlämningsuppgift-1)