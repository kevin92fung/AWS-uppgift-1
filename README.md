<a name="top"></a>
# AWS - Inlämningsuppgift 1

## Innehållsförteckning
- [Skapa en robust, säker och skalbar hosting-miljö för en webbapplikation](#skapa-en-robust-säker-och-skalbar-hosting-miljö-för-en-webbapplikation)
- [Sätta upp ett IAM-konto för AWS CLI](#sätta-upp-ett-iam-konto-för-aws-cli)
- [Installation och konfiguration av AWS CLI](#installation-och-konfiguration-av-aws-cli)
- [CloudFormation Template](#cloudformation-template)
- [Skapa en VPC för host-miljön](#skapa-en-vpc-för-host-miljön)
  - [Skapa en VPC](#skapa-en-vpc)
  - [Skapa en Internet Gateway för VPC:n](#skapa-en-internet-gateway-för-vpcn)
  - [Koppla Internet Gateway till VPC](#koppla-internet-gateway-till-vpc)
  - [Skapa subnets för VPC](#skapa-subnets-för-vpc)
  - [Skapa och Konfigurera Route Tables för VPC](#skapa-och-konfigurera-route-tables-för-vpc)
- [Skapa Security Group](#skapa-security-group)
- [Skapa SSH Key Pair](#skapa-ssh-key-pair)
- [Skapa Launch Template](#skapa-launch-template)
- [Skapa Load Balancer](#skapa-load-balancer)
- [Skapa Target Group](#skapa-target-group)
- [Skapa Listener för Load Balancer](#skapa-listener-för-load-balancer)
- [Skapa Auto Scaling Group](#skapa-auto-scaling-group)
- [Skapa Scaling Policy för Auto Scaling Group](#skapa-auto-scaling-group)
- [Rensa Upp Efter Dig](#rensa-upp-efter-dig)

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
[⬆️ Till toppen](#top)
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



[⬆️ Till toppen](#top)
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

[⬆️ Till toppen](#top)

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

[⬆️ Till toppen](#top)
# Skapa en VPC för host-miljön

### Innehållsförteckning för VPC-sektionen

- [Skapa en VPC](#skapa-en-vpc)
- [Skapa en Internet Gateway för VPC:n](#skapa-en-internet-gateway-för-vpcn)
- [Koppla Internet Gateway till VPC](#koppla-internet-gateway-till-vpc)
- [Skapa subnets för VPC](#skapa-subnets-för-vpc)
- [Skapa och Konfigurera Route Tables för VPC](#skapa-och-konfigurera-route-tables-för-vpc)

## Skapa en VPC

Det vi ska börja med är att skapa en ny VPC för vår hosting-miljö. En VPC (Virtual Private Cloud) ger en isolerad miljö där du kan köra dina resurser i AWS. Genom att definiera en VPC kan du styra nätverkskonfigurationer, inklusive IP-adressering, subnät, routing och säkerhet.

Lägg till följande kod under Resources: i din CloudFormation.yaml-fil:

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

1. Logga in på AWS Management Console.
2. Navigera till VPC-tjänsten.
3. Välj "Your VPCs" i sidomenyn.
4. Kontrollera att `Uppgift-1-VPC` finns listad där.

```bash
aws cloudformation delete-stack --stack-name uppgift-1
```

Detta kommando kommer att ta bort alla resurser som skapats i samband med stacken, inklusive VPC:n. 

[Till toppen av denna sektion](#skapa-en-vpc-för-host-miljön)

---

## Skapa en Internet Gateway för VPC:n

När VPC:n är skapad behöver vi också skapa en Internet Gateway för att kunna nå resurser inom vår VPC från internet. En Internet Gateway möjliggör att instanser inom VPC
kan kommunicera med internet och vice versa. Utan en Internet Gateway kan inte resurser inom din VPC ta emot trafik från internet, vilket gör det avgörande för att möjliggöra publik åtkomst till dina applikationer och tjänster.

Lägg till följande resurser under Resources: i din CloudFormation.yaml-fil:

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

1. Logga in på AWS Management Console.
2. Navigera till VPC-tjänsten.
3. Välj "Internet Gateways" i sidomenyn.
4. Kontrollera att `Uppgift-1-Internet-Gateway` finns listad där.

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

1. Logga in på AWS Management Console.
2. Navigera till VPC-tjänsten.
3. Välj "Your VPCs" i sidomenyn.
4. Välj din VPC (`Uppgift-1-VPC`) och kontrollera fliken "Resource map" för att se att `Uppgift-1-Internet-Gateway` är kopplad under Network connections.

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

1. Logga in på AWS Management Console.
2. Navigera till VPC-tjänsten.
3. Välj "Subnets" i sidomenyn.
4. Kontrollera att dina subnät (`Uppgift-1-Public-Subnet-A`, `B`, och `C`) finns listade där och är associerade med rätt Availability Zones.

[Till toppen av denna sektion](#innehållsförteckning-för-vpc-sektionen)

## Skapa och Konfigurera Route Tables för VPC

För att säkerställa att trafiken inom vår VPC dirigeras korrekt, behöver vi skapa och konfigurera route tables. Route tables bestämmer hur nätverkstrafik flödar mellan olika subnät och ut till internet. Genom att definiera routes i dessa tabeller kan vi optimera kommunikationen mellan resurser i olika subnät och ge extern åtkomst till våra offentliga resurser. Detta är särskilt viktigt för att uppnå hög tillgänglighet och redundans i vår hosting-miljö.

När dina subnät är skapade, behöver vi skapa en Route Table för att dirigera trafik mellan internet och dina publicerade subnät. Här skapar vi en route table som kommer att användas för alla dina publika subnät.

### Lägg till följande kod under `Resources:` i din CloudFormation-template:

```yaml
Resources:
    # Tidigare definierade resurser...
  ...

  # Skapa Route Table för Public Subnets
  PublicRouteTableUppgift1:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref Uppgift-1-VPC
      Tags:
        - Key: Name
          Value: PublicRouteTableUppgift1
```

### Förklaring av Route Table-resursen:

- **Type**: `AWS::EC2::RouteTable` anger att vi skapar en route table för VPC:n.
- **VpcId**: `!Ref Uppgift-1-VPC` refererar till ID:t för VPC:n där route table ska skapas.
- **Tags**: Används för att namnge route table, vilket gör det enklare att identifiera den i AWS-konsolen.

När route table är skapad, behöver vi också skapa en route i den som tillåter trafik till och från internet genom Internet Gateway:n. Det gör vi i nästa steg.

### Skapa en Route för Public Subnets

För att möjliggöra internetåtkomst för resurser i våra offentliga subnät, behöver vi skapa en route i vår route table som dirigerar trafik till och från internet. Detta görs genom att definiera en route med destinationen `0.0.0.0/0`, vilket innebär att all trafik som inte är specifikt riktad till andra subnät i vår VPC kommer att skickas till internet.

### Lägg till följande kod under `Resources:` i din CloudFormation-template:

```yaml
Resources:
    # Tidigare definierade resurser...
  ...

  # Skapa en route för Public Subnets
  PublicRouteUppgift1:
    Type: AWS::EC2::Route
    DependsOn: AttachGatewayUppgift1
    Properties:
      RouteTableId: !Ref PublicRouteTableUppgift1
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGatewayUppgift1
```

### Förklaring av Route-resursen:

- **Type**: `AWS::EC2::Route` anger att vi skapar en route för vår route table.
- **DependsOn**: Anger att denna resurs är beroende av att Internet Gateway:n är kopplad innan den kan skapas.
- **RouteTableId**: `!Ref PublicRouteTableUppgift1` refererar till ID:t för route table:n där denna route kommer att läggas till.
- **DestinationCidrBlock**: `0.0.0.0/0` definierar att all trafik som inte matchar andra routes ska skickas till internet.
- **GatewayId**: `!Ref InternetGatewayUppgift1` refererar till ID:t för den Internet Gateway som möjliggör internetåtkomst.

### Koppla Public Subnets till Route Table

För att säkerställa att våra offentliga subnät kan dirigera trafik enligt de regler som vi har definierat i vår route table, behöver vi skapa associationer mellan våra offentliga subnät och den route table vi just har konfigurerat. Genom att koppla subnäten till route table:n kommer vi att möjliggöra trafikflöde från och till internet, vilket är avgörande för att våra resurser ska kunna kommunicera effektivt.

### Lägg till följande kod under `Resources:` i din CloudFormation-template:

```yaml
Resources:
    # Tidigare definierade resurser...
  ...

  # Koppla Public Subnets till Route Table
  PublicSubnetARouteTableAssociationUppgift1:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnetAUppgift1
      RouteTableId: !Ref PublicRouteTableUppgift1

  PublicSubnetBRouteTableAssociationUppgift1:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnetBUppgift1
      RouteTableId: !Ref PublicRouteTableUppgift1

  PublicSubnetCRouteTableAssociationUppgift1:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnetCUppgift1
      RouteTableId: !Ref PublicRouteTableUppgift1
```

### Förklaring av Subnet Route Table Association-resurserna:

- **Type**: `AWS::EC2::SubnetRouteTableAssociation` anger att vi skapar en association mellan ett subnet och en route table.
- **SubnetId**: `!Ref PublicSubnetAUppgift1`, `!Ref PublicSubnetBUppgift1`, och `!Ref PublicSubnetCUppgift1` refererar till ID:t för respektive offentligt subnet som ska kopplas till route table:n.
- **RouteTableId**: `!Ref PublicRouteTableUppgift1` refererar till ID:t för den route table som vi har skapat för de offentliga subnäten.

Genom att lägga till dessa associationer kommer alla trafikflöden från de offentliga subnäten att använda den definierade route table:n, vilket möjliggör internetåtkomst och förbättrad kommunikation mellan resurser.

### Provisionera CloudFormation Template

När all kod är inlagd och du är redo att provisionera ändringarna kan du göra det med följande kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

### Verifiering av Samtliga Resurser

1. Logga in på AWS Management Console.
2. Navigera till VPC-tjänsten.
3. Kontrollera Route Tables:
   - Välj "Route Tables" i sidomenyn.
   - Bekräfta att `PublicRouteTableUppgift1` finns med en route till `0.0.0.0/0`.
4. Kontrollera Subnet Associationer:
   - Under "Route Tables", välj `PublicRouteTableUppgift1` och kontrollera att de offentliga subnäten (`PublicSubnetAUppgift1`, `PublicSubnetBUppgift1`, och `PublicSubnetCUppgift1`) är korrekt kopplade.

[Till toppen av denna sektion](#innehållsförteckning-för-vpc-sektionen)

## Skapa Security Group

För att säkerställa att resurser i din VPC har rätt säkerhetsinställningar kan du skapa en säkerhetsgrupp som tillåter HTTP- och SSH-trafik. En säkerhetsgrupp fungerar som en virtuell brandvägg som kontrollerar inkommande och utgående trafik till och från dina resurser. Genom att tillåta HTTP- och SSH-trafik kan du säkerställa att användare kan få åtkomst till dina webbapplikationer och logga in på instanserna på ett säkert sätt.

Lägg till följande resurser under Resources: i din CloudFormation.yaml-fil:

```yaml
Resources:
  # Tidigare definierade resurser...
  ...

  # Skapa Security Group
  SecurityGroupUppgift1:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow HTTP and SSH access
      VpcId: !Ref VPCUppgift1  # Hänvisa till VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0  # Tillåt SSH från alla IP-adresser (kan begränsas)
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0  # Tillåt HTTP från alla IP-adresser (kan begränsas)
      Tags:
        - Key: Name
          Value: SecurityGroupUppgift1
```

### Förklaring av Security Group-resursen:

- **Type**: `AWS::EC2::SecurityGroup` anger att vi skapar en säkerhetsgrupp.
- **GroupDescription**: En beskrivning av vad säkerhetsgruppen gör.
- **VpcId**: `!Ref VPCUppgift1` refererar till ID:t för den VPC där säkerhetsgruppen ska skapas.
- **SecurityGroupIngress**: En lista över regler för inkommmande trafik:
  - För SSH (port 22) tillåts trafik från alla IP-adresser.
  - För HTTP (port 80) tillåts också trafik från alla IP-adresser.
- **Tags**: Används för att namnge säkerhetsgruppen, vilket gör det enklare att identifiera den i AWS-konsolen.

### Provisionera Security Group med AWS CLI

När du har lagt till säkerhetsgruppens resurser kan du provisionera dem genom att köra följande kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

### Verifiera att säkerhetsgruppen har skapats

1. Logga in på AWS Management Console.
2. Navigera till EC2-tjänsten.
3. Kontrollera Security Groups:
   - Välj "Security Groups" i sidomenyn.
   - Bekräfta att `SecurityGroupUppgift1` finns med regler för HTTP och SSH.

[⬆️ Till toppen](#top)


## Skapa SSH Key Pair
Innan du skapar din Launch Template behöver du skapa en SSH-key pair för att kunna logga in på dina instanser. En SSH-key pair består av en offentlig och en privat nyckel, där den offentliga nyckeln lagras i AWS och den privata nyckeln sparas lokalt på din dator. Detta gör att du kan autentisera och få åtkomst till dina EC2-instanser på ett säkert sätt.

Använd följande kommando för att skapa en SSH-key pair:

```bash
aws ec2 create-key-pair --key-name sshkey --query KeyMaterial --output text > C:/adress/till/nyckel/sshkey.pem
```

Detta kommando gör följande:
- Skapar en SSH-key pair med namnet `sshkey`.
- Hämtar den privata nyckeln och sparar den på din lokala dator i den angivna sökvägen (`C:/adress/till/nyckel/sshkey.pem`).

### Verifiera SSH Key Pair på AWS

1. Logga in på AWS Management Console.
2. Navigera till EC2-tjänsten.
3. Kontrollera Key Pairs:
   - Välj "Key Pairs" i sidomenyn under "Network & Security".
   - Bekräfta att `sshkey` finns i listan över nyckelpar.

[⬆️ Till toppen](#top)

## Skapa Launch Template

Efter att du har skapat SSH-key pair kan du skapa en Launch Template. En Launch Template innehåller all information som behövs för att starta EC2-instanser, inklusive instanstyp, AMI, säkerhetsgrupp och andra inställningar. Genom att använda en Launch Template kan du enkelt starta nya instanser med konsekventa konfigurationer, vilket underlättar hanteringen av din infrastruktur.

Lägg till följande resurser under Resources: i din CloudFormation.yaml-fil:

```yaml
Resources:
  # Tidigare definierade resurser...
  ...

  # Skapa Launch Template
  LaunchTemplateUppgift1:
    Type: 'AWS::EC2::LaunchTemplate'
    Properties:
      LaunchTemplateName: !Sub '${AWS::StackName}-LaunchTemplate'  # Dynamiskt namn baserat på stacknamn
      LaunchTemplateData:
        InstanceType: t2.micro  # Typ av instans
        ImageId: !Ref LatestAmiId  # Referens till senaste AMI-ID
        KeyName: sshkey  # Namnet på ditt SSH-key pair
        SecurityGroupIds:
          - !Ref SecurityGroupUppgift1  # Hänvisa till säkerhetsgruppen
        UserData:
          Fn::Base64: !Sub |
            #!/bin/bash
            dnf update && dnf install nginx -y
            systemctl start nginx
            systemctl enable nginx
            dnf install stress -y && dnf install htop -y
```

### Förklaring av Launch Template-resursen:

- **Type**: `AWS::EC2::LaunchTemplate` anger att vi skapar en launch template.
- **LaunchTemplateName**: Dynamiskt namn baserat på stacknamnet.
- **LaunchTemplateData**: Definierar instansens konfiguration:
  - **InstanceType**: Typen av instans, här `t2.micro`.
  - **ImageId**: Referensen till den senaste AMI-ID:t.
  - **KeyName**: Namnet på SSH-key pair som skapades tidigare.
  - **SecurityGroupIds**: Referens till den tidigare skapade säkerhetsgruppen.
  - **UserData**: Skript som körs vid start av instansen för att installera och konfigurera Nginx samt andra verktyg.

### Provisionera Launch Template med AWS CLI

När du har lagt till Launch Template-resurserna kan du provisionera dem genom att köra följande kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

### Verifiera Launch Template

1. Logga in på AWS Management Console.
2. Navigera till EC2-tjänsten.
3. Kontrollera Launch Templates:
   - Välj "Launch Templates" i sidomenyn under "Instances".
   - Bekräfta att Launch Template med namnet `${AWS::StackName}-LaunchTemplate` finns i listan.

[⬆️ Till toppen](#top)

## Skapa Load Balancer
För att distribuera trafik till dina instanser kan du skapa en Load Balancer. En Load Balancer fungerar som en fördelare av inkommande trafik till flera instanser, vilket förbättrar tillgängligheten och gör att du kan hantera hög trafikbelastning. Genom att använda en Load Balancer kan du också säkerställa att trafik dirigeras till friska instanser, vilket ökar systemets motståndskraft.

Lägg till följande kod under Resources: i din CloudFormation.yaml-fil:

```yaml
Resources:
  # Tidigare definierade resurser...
  ...

  # Skapa Load Balancer
  LoadBalancerUppgift1:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Name: LoadBalancerUppgift1
      Subnets:
        - !Ref PublicSubnetAUppgift1
        - !Ref PublicSubnetBUppgift1
        - !Ref PublicSubnetCUppgift1
      SecurityGroups:
        - !Ref SecurityGroupUppgift1
      Scheme: internet-facing
      LoadBalancerAttributes:
        - Key: idle_timeout.timeout_seconds
          Value: '60'
      Tags:
        - Key: Name
          Value: LoadBalancerUppgift1
```

### Förklaring av Load Balancer-resursen:

- **Type**: `AWS::ElasticLoadBalancingV2::LoadBalancer` anger att vi skapar en load balancer.
- **Name**: Namnet på load balancer.
- **Subnets**: Referenser till de offentliga subnäten där load balancer ska distribueras.
- **SecurityGroups**: Referens till säkerhetsgruppen som tillåter trafik.
- **Scheme**: Anger att load balancer är "internet-facing".
- **LoadBalancerAttributes**: Definierar attribut för load balancer, som t.ex. idle timeout.
- **Tags**: Används för att namnge load balancer, vilket gör det enklare att identifiera den.

### Provisionera Load Balancer med AWS CLI

När du har lagt till Load Balancer-resurserna kan du provisionera dem genom att köra följande kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

### Verifiera Load Balancer

1. Logga in på AWS Management Console.
2. Navigera till EC2-tjänsten.
3. Kontrollera Load Balancers:
   - Välj "Load Balancers" i sidomenyn under "Load Balancing".
   - Bekräfta att `LoadBalancerUppgift1` finns i listan över load balancers.

[⬆️ Till toppen](#top)

## Skapa Target Group
För att dirigera trafik till dina instanser via load balancern kan du skapa en Target Group. En Target Group är en resurs som definierar en grupp av instanser (eller andra resurser) som tar emot trafik från en load balancer. Genom att använda Target Groups kan du specificera hälsokontroller, portnummer och protokoll för att säkerställa att trafiken alltid dirigeras till friska och aktiva instanser.

Lägg till följande kod under Resources: i din CloudFormation.yaml-fil:

```yaml
Resources:
  # Tidigare definierade resurser...
  ...

  # Skapa Target Group
  TargetGroupUppgift1:
    Type: AWS::ElasticLoadBalancingV2::TargetGroup
    Properties:
      Name: TargetGroupUppgift1
      Port: 80
      Protocol: HTTP
      VpcId: !Ref VPCUppgift1
      TargetType: instance
      HealthCheckIntervalSeconds: 30
      HealthCheckProtocol: HTTP
      HealthCheckTimeoutSeconds: 5
      HealthyThresholdCount: 5
      UnhealthyThresholdCount: 2
      Tags:
        - Key: Name
          Value: TargetGroupUppgift1
```

### Förklaring av Target Group-resursen:

- **Type**: `AWS::ElasticLoadBalancingV2::TargetGroup` anger att vi skapar en target group.
- **Name**: Namnet på target group.
- **Port**: Den port som target group kommer att lyssna på (port 80 för HTTP).
- **Protocol**: Protokollet som används för trafik (HTTP).
- **VpcId**: Referens till VPC:n där target group ska skapas.
- **TargetType**: Anger att vi riktar trafik till instanser.
- **HealthCheckIntervalSeconds**: Intervallet i sekunder mellan hälsokontroller.
- **HealthCheckProtocol**: Protokollet som används för hälsokontroller (HTTP).
- **HealthCheckTimeoutSeconds**: Timeout i sekunder för hälsokontroller.
- **HealthyThresholdCount**: Antal framgångsrika hälsokontroller som krävs för att en instans ska anses vara frisk.
- **UnhealthyThresholdCount**: Antal misslyckade hälsokontroller som krävs för att en instans ska anses vara ohälsosam.
- **Tags**: Används för att namnge target group, vilket gör det enklare att identifiera den.

### Provisionera Target Group med AWS CLI

När du har lagt till Target Group-resurserna kan du provisionera dem genom att köra följande kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

### Verifiera Target Group

1. Logga in på AWS Management Console.
2. Navigera till EC2-tjänsten.
3. Kontrollera Target Groups:
   - Välj "Target Groups" i sidomenyn under "Load Balancing".
   - Bekräfta att `TargetGroupUppgift1` finns i listan över target groups.

[⬆️ Till toppen](#top)

## Skapa Listener för Load Balancer

För att dirigera inkommande trafik till din Target Group via Load Balancern kan du skapa en Listener. En Listener är en process som kontrollerar för inkommande trafik på en specifik port och protokoll och vidarebefordrar denna trafik till en specifik Target Group baserat på angivna regler. Detta möjliggör lastbalansering av trafik över flera instanser som är registrerade i Target Group.

Lägg till följande kod under Resources: i din CloudFormation.yaml-fil:

```yaml
Resources:
  # Tidigare definierade resurser...
  ...

  # Skapa Listener för Load Balancer
  LoadBalancerListenerUppgift1:
    Type: AWS::ElasticLoadBalancingV2::Listener
    Properties:
      DefaultActions:
        - Type: forward
          TargetGroupArn: !Ref TargetGroupUppgift1
      LoadBalancerArn: !Ref LoadBalancerUppgift1
      Port: 80
      Protocol: HTTP
```

### Förklaring av Listener-resursen:

- **Type**: `AWS::ElasticLoadBalancingV2::Listener` anger att vi skapar en listener för load balancern.
- **DefaultActions**: Anger åtgärder som ska utföras av listenern. Här dirigerar vi trafik till `TargetGroupUppgift1`.
- **LoadBalancerArn**: Referens till ARN för den load balancer som listenern ska kopplas till.
- **Port**: Den port som listenern kommer att lyssna på (port 80 för HTTP).
- **Protocol**: Protokollet som används för inkommande trafik (HTTP).

### Provisionera Listener med AWS CLI

När du har lagt till Listener-resurserna kan du provisionera dem genom att köra följande kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

### Verifiera Listener

1. Logga in på AWS Management Console.
2. Navigera till EC2-tjänsten.
3. Kontrollera Listener:
   - Välj "Listeners" i sidomenyn under "Load Balancing" och sedan "Load Balancers".
   - Bekräfta att `LoadBalancerListenerUppgift1` finns listad under den relevanta load balancern.

[⬆️ Till toppen](#top)

## Skapa Auto Scaling Group

För att automatiskt hantera instanser i din miljö kan du skapa en Auto Scaling Group. Genom att använda en Auto Scaling Group kan du säkerställa att du har rätt antal instanser som körs vid alla tider, baserat på den belastning och trafik som din applikation hanterar. Detta gör det möjligt att skala upp eller ner efter behov, vilket bidrar till kostnadseffektivitet och resurseffektivitet.

Lägg till följande kod under `Resources:` i din `CloudFormation.yaml`-fil:

```yaml
Resources:
  # Tidigare definierade resurser...
  ...

  # Skapa Auto Scaling Group
  AutoScalingGroupUppgift1:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      AutoScalingGroupName: !Sub '${AWS::StackName}-AutoScalingGroup'
      LaunchTemplate:
        LaunchTemplateId: !Ref LaunchTemplateUppgift1
        Version: !GetAtt LaunchTemplateUppgift1.LatestVersionNumber
      MinSize: '1'
      MaxSize: '3'
      DesiredCapacity: '1'
      VPCZoneIdentifier:
        - !Ref PublicSubnetAUppgift1
        - !Ref PublicSubnetBUppgift1
        - !Ref PublicSubnetCUppgift1
      TargetGroupARNs:
        - !Ref TargetGroupUppgift1
      Tags:
        - Key: Name
          Value: AutoScalingGroupUppgift1
          PropagateAtLaunch: true
```

### Förklaring av Auto Scaling Group-resursen:

- **Type**: `AWS::AutoScaling::AutoScalingGroup` anger att vi skapar en auto scaling group.
- **AutoScalingGroupName**: Namnet på auto scaling-gruppen, här dynamiskt baserat på stacknamnet.
- **LaunchTemplate**: Referens till den lanseringsmall som ska användas för att starta instanser.
  - **LaunchTemplateId**: ID för den lanseringsmall som ska användas.
  - **Version**: Referens till den senaste versionen av lanseringsmallen.
- **MinSize**: Minimum antal instanser som alltid ska finnas i gruppen.
- **MaxSize**: Maximum antal instanser som gruppen kan expandera till.
- **DesiredCapacity**: Det önskade antalet instanser i gruppen.
- **VPCZoneIdentifier**: En lista över subnät där instanserna kan placeras.
- **TargetGroupARNs**: Referens till den target group som auto scaling-gruppen är kopplad till.
- **Tags**: Används för att namnge auto scaling-gruppen, vilket gör det enklare att identifiera den.

### Provisionera Auto Scaling Group med AWS CLI

När du har lagt till Auto Scaling Group-resurserna kan du provisionera dem genom att köra följande kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

### Verifiera Auto Scaling Group

1. Logga in på AWS Management Console.
2. Navigera till EC2-tjänsten.
3. Kontrollera Auto Scaling Groups:
   - Välj "Auto Scaling Groups" i sidomenyn.
   - Bekräfta att `AutoScalingGroupUppgift1` finns i listan över auto scaling-grupper.


[⬆️ Till toppen](#top)

## Skapa Scaling Policy för Auto Scaling Group

För att automatiskt skala instanser baserat på CPU-användning behöver vi skapa en scaling policy. Denna policy kommer att definiera hur vår Auto Scaling Group ska reagera på förändringar i belastningen. Genom att implementera en scaling policy kan vi säkerställa att vår applikation alltid har tillräckligt med resurser för att hantera den aktuella trafiken, vilket förbättrar både prestanda och tillförlitlighet.

Lägg till följande kod under `Resources:` i din `CloudFormation.yaml`-fil:

```yaml
Resources:
  # Tidigare definierade resurser...
  ...

  # Skapa Scaling Policy för Auto Scaling Group
  ScalingPolicyUppgift1:
    Type: AWS::AutoScaling::ScalingPolicy
    Properties:
      AutoScalingGroupName: !Ref AutoScalingGroupUppgift1
      PolicyType: TargetTrackingScaling
      TargetTrackingConfiguration:
        PredefinedMetricSpecification:
          PredefinedMetricType: ASGAverageCPUUtilization
        TargetValue: 50.0  # Skala vid 50% CPU-användning
```

### Förklaring av Scaling Policy-resursen:

- **Type**: `AWS::AutoScaling::ScalingPolicy` anger att vi skapar en scaling policy för auto scaling-gruppen.
- **AutoScalingGroupName**: Referens till den auto scaling-grupp som denna policy tillhör.
- **PolicyType**: Typen av scaling policy, här `TargetTrackingScaling` som skalar instanser baserat på en definierad målnivå för en specifik metric.
- **TargetTrackingConfiguration**: Konfigurationen för hur skalning ska ske.
  - **PredefinedMetricSpecification**: Anger den fördefinierade metric som ska övervakas, här med `PredefinedMetricType` satt till `ASGAverageCPUUtilization` för att övervaka den genomsnittliga CPU-användningen.
  - **TargetValue**: Den önskade målningen för CPU-användningen, i det här fallet 50%.

### Provisionera Scaling Policy med AWS CLI

När du har lagt till scaling policy-resursen kan du provisionera den genom att köra följande kommando:

```bash
aws cloudformation deploy --template-file CloudFormation.yaml --stack-name uppgift-1
```

### Verifiera Scaling Policy

1. Logga in på AWS Management Console.
2. Navigera till EC2-tjänsten.
3. Kontrollera Scaling Policies:
   - Välj "Auto Scaling Groups" i sidomenyn.
   - Välj `AutoScalingGroupUppgift1` och navigera till fliken "Scaling Policies".
   - Bekräfta att `ScalingPolicyUppgift1` finns i listan över scaling policies.

[⬆️ Till toppen](#top)

## Rensa Upp Efter Dig

När du är klar med att använda dina resurser i AWS är det viktigt att rensa upp för att undvika onödiga kostnader. Ett effektivt sätt att ta bort alla resurser som skapats under din CloudFormation-stack är att använda AWS CLI för att radera hela stacken. Genom att ta bort stacken kommer alla resurser som skapats inom den automatiskt att tas bort.

### Så här gör du för att ta bort din stack:

Kör följande kommando i din terminal:

```bash
aws cloudformation delete-stack --stack-name uppgift-1
```

### Verifiera Borttagningen

1. **Logga in på AWS Management Console.**
2. **Navigera till CloudFormation-tjänsten.**
3. **Kontrollera att stacken inte längre finns i listan över aktiva stackar.**

Genom att följa dessa steg säkerställer du att alla resurser kopplade till din stack tas bort på ett effektivt sätt, vilket hjälper dig att hålla kostnaderna nere och din miljö organiserad.


[⬆️ Till toppen](#top)