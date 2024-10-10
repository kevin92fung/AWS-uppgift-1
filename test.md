## Skapa Target Group

För att dirigera trafik till dina instanser via load balancern kan du skapa en Target Group. Lägg till följande kod under `Resources:` i din `CloudFormation.yaml`-fil:

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

För att verifiera att Target Group har skapats korrekt kan du använda följande steg:

1. **Logga in på AWS Management Console.**
2. **Navigera till EC2-tjänsten.**
3. **Kontrollera Target Groups:**
   - Välj "Target Groups" i sidomenyn under "Load Balancing".
   - Bekräfta att `TargetGroupUppgift1` finns i listan över target groups.