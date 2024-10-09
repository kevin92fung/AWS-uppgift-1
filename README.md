<a name="top"></a>
# AWS - Inlämningsuppgift 1

## Innehållsförteckning
- [Skapa en robust, säker och skalbar hosting-miljö för en webbapplikation](#skapa-en-robust-säker-och-skalbar-hosting-miljö-för-en-webbapplikation)
- [Sätta upp ett IAM-konto för AWS CLI](#sätta-upp-ett-iam-konto-för-aws-cli)
- [Installation och konfiguration av AWS CLI](#installation-och-konfiguration-av-aws-cli)
- [CloudFormation Template](#cloudformation-template)
- [Skapa en VPC för host-miljön](#skapa-en-vpc-för-host-miljön)

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


## Installation och konfiguration av AWS CLI

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

## CloudFormation Template
I denna guide kommer vi också att använda oss av en **CloudFormation Template** där vi beskriver de resurser som krävs för att skapa vår hosting-miljö. En **CloudFormation Template** är skriven i **YAML**, vilket står för "YAML Ain't Markup Language". YAML är ett lättläst format för att skriva konfigurationer, vilket gör det enkelt att definiera strukturer och hierarkier.

En **CloudFormation Template** består av olika delar, såsom parametrar, resurser och utdata:

- **Parametrar**: Definierar ingångsvärden som kan anpassas när templaten körs.
- **Resurser**: Specifierar de AWS-resurser som ska skapas, såsom EC2-instanser, S3-buckets och databaser.
- **Utdata**: Anger vad som ska visas som resultat efter att templaten har körts, t.ex. IP-adresser och andra viktiga informationer om skapade resurser.

Nedan är en tom CloudFormation-template som vi kommer fylla i steg för steg under guiden. Varje del av templaten beskriver olika aspekter av infrastrukturen som vi skapar. Du kommer att fylla i parametrar och resurser allt eftersom vi bygger upp din hosting-miljö.

Börja med att skapa en ny fil för CloudFormation-templaten. Du kan namnge filen `CloudFormation.yaml`.

I denna fil kommer vi att definiera en CloudFormation-template som skapar en säker och skalbar hosting-miljö för en webbapplikation. Följande resurser är några av de resurser som ingår i templaten:

- **VPC**: En Virtual Private Cloud för att isolera resurser.
- **Subnet**: En subnet inom VPC för att placera resurser.
- **Security Group**: För att hantera säkerhetsregler för instanserna.
- **Launch Template**: För att definiera konfigurationen av instanserna som ska lanseras.
- **Target Group**: För att gruppera instanser som hanteras av load balancer.
- **Application Load Balancer**: För att hantera och fördela trafiken mellan instanser.
- **Auto Scaling Group**: För att automatiskt skala upp och ner antalet instanser baserat på belastning.

Nedan är innehållet för filen:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Denna template skapar en säker och skalbar hosting-miljö för en webbapplikation.

#Parameters: "Parametrar behövs inte i början av guiden. Om de inte innehåller något kan de skapa problem."
  # Definiera ingångsparametrar som kan anpassas vid körning av templaten

Resources:
  # Resurser som CloudFormation ska skapa:
  
#Outputs: "Utdata behövs inte i början av guiden."
  # Anger vad som ska visas som utdata efter att templaten körts
```


# Skapa en VPC för host-miljön

### Innehållsförteckning för VPC-sektionen

- [Skapa en VPC](#skapa-en-vpc)
- [Skapa en Internet Gateway för VPC:n](#skapa-en-internet-gateway-för-vpcn)
- [Koppla Internet Gateway till VPC](#koppla-internet-gateway-till-vpc)
- [Skapa subnets för VPC](#skapa-subnets-för-vpc)

## Skapa en VPC

Det vi ska börja med är att skapa en ny VPC för vår hosting-miljö. Detta gör vi genom att beskriva resursen under sektionen för resurser i vår CloudFormation-template.

Lägg till följande kod under `Resources:` i din `CloudFormation.yaml`-fil:

```yaml
Resources:
  # Skapa VPC
  Uppgift1VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      Tags:
        - Key: Name
          Value: Uppgift-1-VPC
```

### Förklaring av VPC-resursen:

- **CidrBlock**: Definierar IP-adressintervallet för VPC:n. I det här fallet används `10.0.0.0/16`, vilket ger oss ett stort antal tillgängliga IP-adresser inom VPC:n.
- **EnableDnsSupport**: Anger om DNS-stöd ska vara aktiverat för VPC:n. Detta är viktigt för att kunna använda DNS-namn för instanser i VPC:n.
- **Tags**: Används för att namnge VPC:n, vilket gör det enklare att identifiera den i AWS-konsolen.

### Provisionera VPC med AWS CLI

När du har lagt till VPC-resursen i din CloudFormation-template är det dags att provisionera den. Du kan göra detta med följande AWS CLI-kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

Detta kommando kommer att använda din `CloudFormation.yaml`-fil för att skapa en stack med namnet `uppgift-1`, som kommer att innehålla den VPC vi just har definierat.

### Verifiera att VPC har skapats

**Via portalen:**

1. Logga in på AWS Management Console.
2. Navigera till VPC-tjänsten.
3. Välj "Your VPCs" i sidomenyn.
4. Kontrollera att `Uppgift-1-VPC` finns listad där.

**Via terminal:**

Kör följande kommando för att lista alla VPC:er i ditt konto:

```bash
aws ec2 describe-vpcs --filters "Name=tag:Name,Values=Uppgift-1-VPC"
```

Om VPC:n har skapats framgångsrikt bör du se detaljer om `Uppgift-1-VPC` i svaret.

### Ta bort VPC och resurser

När du är klar med att arbeta med din VPC är det viktigt att ta bort den för att minimera kostnader. Du kan ta bort stacken med följande kommando:

```bash
aws cloudformation delete-stack --stack-name uppgift-1
```

Detta kommando kommer att ta bort alla resurser som skapats i samband med stacken, inklusive VPC:n. 

[Till toppen av denna sektion](#skapa-en-vpc-för-host-miljön)

---

## Skapa en Internet Gateway för VPC:n

När VPC:n är skapad behöver vi också skapa en Internet Gateway för att kunna nå resurser inom vår VPC från internet. En Internet Gateway möjliggör att instanser inom VPC:n kan kommunicera med internet och vice versa. 

Lägg till följande kod under `Resources:` i din `CloudFormation.yaml`-fil, direkt efter VPC-resursen:

```yaml
Resources:
  # Tidigare definierade resurser här...
  ... 

  # Skapa Internet Gateway
  Uppgift1InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: Uppgift-1-Internet-Gateway
```

### Förklaring av Internet Gateway-resursen:

- **Type**: `AWS::EC2::InternetGateway` anger att vi skapar en Internet Gateway.
- **Tags**: Används för att namnge Internet Gateway:n, vilket gör det enklare att identifiera den i AWS-konsolen.

### Provisionera Internet Gateway med AWS CLI

När du har lagt till Internet Gateway-resursen i din CloudFormation-template, kan du återigen använda AWS CLI-kommandot för att provisionera ändringarna:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

### Verifiera att Internet Gateway har skapats

**Via portalen:**

1. Logga in på AWS Management Console.
2. Navigera till VPC-tjänsten.
3. Välj "Internet Gateways" i sidomenyn.
4. Kontrollera att `Uppgift-1-Internet-Gateway` finns listad där.

**Via terminal:**

Kör följande kommando för att lista alla Internet Gateways i ditt konto:

```bash
aws ec2 describe-internet-gateways --filters "Name=tag:Name,Values=Uppgift-1-Internet-Gateway"
```

Om Internet Gateway:n har skapats framgångsrikt bör du se detaljer om `Uppgift-1-Internet-Gateway` i svaret.

### Ta bort Internet Gateway och resurser

För att minimera kostnader är det också viktigt att ta bort Internet Gateway:n när den inte längre behövs. Använd följande kommando för att ta bort stacken:

```bash
aws cloudformation delete-stack --stack-name uppgift-1
```

Detta kommer att ta bort alla resurser som skapades, inklusive Internet Gateway:n.

[Till toppen av denna sektion](#skapa-en-vpc-för-host-miljön)

## Koppla Internet Gateway till VPC

När både VPC:n och Internet Gateway:n är skapade, behöver vi koppla ihop dem för att möjliggöra kommunikation mellan resurser i VPC:n och internet. Detta görs genom att använda resursen `AWS::EC2::VPCGatewayAttachment`.

Lägg till följande kod under `Resources:` i din `CloudFormation.yaml`-fil, direkt efter Internet Gateway-resursen:

```yaml
Resources:
  # Tidigare definierade resurser här...
  ...

  # Koppla Internet Gateway till VPC
  Uppgift1AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref Uppgift1VPC
      InternetGatewayId: !Ref Uppgift1InternetGateway
```

### Förklaring av VPCGatewayAttachment-resursen:

- **Type**: `AWS::EC2::VPCGatewayAttachment` anger att vi skapar en koppling mellan Internet Gateway:n och VPC:n.
- **VpcId**: `!Ref Uppgift1VPC` refererar till ID:t för den VPC som vi vill koppla Internet Gateway:n till. `!Ref` används för att hämta värdet av den resurs vi tidigare definierat.
- **InternetGatewayId**: `!Ref Uppgift1InternetGateway` refererar till ID:t för den Internet Gateway som ska kopplas till VPC:n.

### Provisionera Gateway Attachment med AWS CLI

När du har lagt till `VPCGatewayAttachment`-resursen i din CloudFormation-template, kan du provisionera ändringarna med följande AWS CLI-kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

Detta kommando kommer att uppdatera stacken och koppla Internet Gateway:n till VPC:n.

### Verifiera att Gateway Attachment har skapats

**Via portalen:**

1. Logga in på AWS Management Console.
2. Navigera till VPC-tjänsten.
3. Välj "Your VPCs" i sidomenyn.
4. Välj din VPC (`Uppgift-1-VPC`) och kontrollera fliken "Resource map" för att se att `Uppgift-1-Internet-Gateway` är kopplad under Network connections.

### Ta bort Gateway Attachment och resurser

När du är klar med att arbeta med din VPC och Internet Gateway, kom ihåg att ta bort stacken för att minimera kostnader. Använd följande kommando för att ta bort stacken:

```bash
aws cloudformation delete-stack --stack-name uppgift-1
```

[Till toppen av denna sektion](#innehållsförteckning-för-vpc-sektionen)

## Skapa subnets för VPC

För att uppnå hög tillgänglighet i vår hosting-miljö behöver vi skapa minst två subnät, men vi ska skapa tre subnät, ett i varje Availability Zone i `eu-west-1`. Dessa subnät kommer att konfigureras som offentliga subnät, vilket betyder att resurser som lanseras i dessa subnät kommer att få publika IP-adresser vid start.

Lägg till följande resurser under `Resources:` i din `CloudFormation.yaml`-fil:

```yaml
Resources:
  # Tidigare definierade resurser...
  ...

  # Skapa Public Subnets
  Uppgift1PublicSubnetA:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref Uppgift1VPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: eu-west-1a
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: Uppgift-1-Public-Subnet-A

  Uppgift1PublicSubnetB:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref Uppgift1VPC
      CidrBlock: 10.0.2.0/24
      AvailabilityZone: eu-west-1b
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: Uppgift-1-Public-Subnet-B

  Uppgift1PublicSubnetC:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref Uppgift1VPC
      CidrBlock: 10.0.3.0/24
      AvailabilityZone: eu-west-1c
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: Uppgift-1-Public-Subnet-C
```

### Förklaring av Subnet-resurserna:

- **VpcId**: Refererar till VPC:n som subnätet ska tillhöra, här `Uppgift-1-VPC`.
- **CidrBlock**: Varje subnät har ett unikt IP-adressintervall. Vi har valt `10.0.1.0/24`, `10.0.2.0/24`, och `10.0.3.0/24` för våra tre subnät.
- **AvailabilityZone**: Vi distribuerar subnäten över olika Availability Zones i regionen `eu-west-1` för att säkerställa hög tillgänglighet.
- **MapPublicIpOnLaunch**: Detta säkerställer att alla instanser som startas i subnätet får en offentlig IP-adress.
- **Tags**: Vi ger varje subnät ett unikt namn för enklare identifiering.

### Provisionera Subnets med AWS CLI

När du har lagt till subnät-resurserna kan du provisionera dem genom att köra följande kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

### Verifiera att subnät har skapats

**Via portalen:**

1. Logga in på AWS Management Console.
2. Navigera till VPC-tjänsten.
3. Välj "Subnets" i sidomenyn.
4. Kontrollera att dina subnät (`Uppgift-1-Public-Subnet-A`, `B`, och `C`) finns listade där och är associerade med rätt Availability Zones.


### Ta bort subnät och resurser

Kom ihåg att ta bort stacken när du inte längre behöver subnäten:

```bash
aws cloudformation delete-stack --stack-name uppgift-1
```

[Till toppen av denna sektion](#innehållsförteckning-för-vpc-sektionen)


<a href="#top" style="position:fixed;bottom:10px;right:10px;font-size:20px;">⬆️</a>
