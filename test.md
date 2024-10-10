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

För att verifiera att alla resurser har skapats korrekt kan du använda följande steg:

### Genom AWS Management Console

1. **Logga in på AWS Management Console.**
2. **Navigera till VPC-tjänsten.**
3. **Kontrollera Route Tables:**
   - Välj "Route Tables" i sidomenyn.
   - Bekräfta att `PublicRouteTableUppgift1` finns med en route till `0.0.0.0/0`.
4. **Kontrollera Subnet Associationer:**
   - Under "Route Tables", välj `PublicRouteTableUppgift1` och kontrollera att de offentliga subnäten (`PublicSubnetAUppgift1`, `PublicSubnetBUppgift1`, och `PublicSubnetCUppgift1`) är korrekt kopplade.

### Genom AWS CLI

1. **Verifiera Route Table:**

   För att säkerställa att din route har skapats korrekt kan du använda följande kommando:

   ```bash
   aws ec2 describe-route-tables --route-table-ids $(aws ec2 describe-route-tables --filters "Name=tag:Name,Values=PublicRouteTableUppgift1" --query "RouteTables[0].RouteTableId" --output text) --query "RouteTables[0].Routes"
   ```

   Bekräfta att det finns en route med destination `0.0.0.0/0` och att den pekar på din Internet Gateway.

2. **Verifiera Subnet Associationer:**

   Du kan kontrollera associationerna mellan subnäten och route table:n med följande kommando:

   ```bash
   aws ec2 describe-subnet-route-table-associations --route-table-id $(aws ec2 describe-route-tables --filters "Name=tag:Name,Values=PublicRouteTableUppgift1" --query "RouteTables[0].RouteTableId" --output text)
   ```

   Bekräfta att alla offentliga subnät (`PublicSubnetAUppgift1`, `PublicSubnetBUppgift1`, och `PublicSubnetCUppgift1`) är listade i associationerna.

## Ta bort Stack

När du är klar med att arbeta med din stack och vill minimera kostnader kan du ta bort stacken med följande kommando:

```bash
aws cloudformation delete-stack --stack-name uppgift-1
```

Detta kommando kommer att ta bort alla resurser som skapats i samband med stacken, inklusive route tables och subnätsassociationer.
