# AWS Security Groups Documentation

## 1. Jenkins Server — `jenkins-sg`

### Inbound Rules

| Rule   | Type       | Protocol | Port Range | Source                     | Description    |
|--------|------------|----------|------------|----------------------------|----------------|
| Rule 1 | SSH        | TCP      |    22      | 106.221.237.195/32 (My IP) | SSH access     |
| Rule 2 | Custom TCP | TCP      |    8080    | 106.221.237.195/32 (My IP) | Jenkins Web UI |

## 2. SonarQube Server — `sonar-sg`

### Inbound Rules

| Security Group Rule ID | Protocol | Port Range           | Source               | Security Group |
|------------------------|----------|----------------------|----------------------|----------------|
| sgr-03fe66096dab81cc1  | TCP      | 22                   | 106.221.237.195/32   | sonar-sg       |
| sgr-0dc6e3c20206bd4be  | TCP      | 80                   | sg-01a745fe28580f47a | sonar-sg       |
| sgr-0a1f4a05c9a723639  | TCP      | 80                   | 106.221.237.195/32   | sonar-sg       |

## 3. Nexus Server — `nexus-sg`

### Inbound Rules

| Security Group Rule ID | Protocol | Port Range | Source               | Security Group |
|------------------------|----------|------------|----------------------|----------------|
| sgr-0c64d64baa76f8d6f  | TCP      | 8081       | 106.221.237.195/32   | nexus-sg       |
| sgr-0ad26ec97a789ef95  | TCP      | 22         | 106.221.237.195/32   | nexus-sg       |
| sgr-0eb2260f578ee1afd  | TCP      | 8081       | sg-01a745fe28580f47a | nexus-sg       |