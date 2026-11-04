# floci · Guia rápido em português

Projeto original: [floci-io/floci](https://github.com/floci-io/floci) · [Español](README.es.md)

## O que é o floci

Um emulador local e gratuito da AWS. Ele sobe serviços com o formato da AWS na sua própria máquina, em `http://localhost:4566`, sem conta da AWS, sem token de autenticação e sem funções trancadas atrás de plano pago. Seu AWS CLI, seus SDKs, Terraform, CDK ou OpenTofu e seus testes com Testcontainers continuam iguais: só muda o endpoint.

Ele chega na hora certa: a edição comunitária do LocalStack parou de receber atualizações em março de 2026 e passou a exigir token. O floci ocupa esse espaço com licença MIT.

## floci x LocalStack Community

| | floci | LocalStack Community |
|---|---|---|
| Token de autenticação | Não | Sim |
| Atualizações de segurança | Sim | Congeladas |
| Tempo de inicialização | ~24 ms* | ~3,3 s |
| Memória em repouso | ~13 MiB* | ~143 MiB |
| Tamanho da imagem Docker | ~90 MB | ~1,0 GB |
| Licença | MIT | Restrita |
| API Gateway v2 / HTTP API | Sim | Não |

\* Números da tabela comparativa do próprio projeto. Uma medição independente colocou a inicialização em torno de 130 ms: continua muito rápido, mas vale saber de onde vem cada número.

## Três jeitos de subir

**1. Com a CLI oficial (o mais curto)**

```bash
floci start
eval $(floci env)
```

O `floci env` exporta as variáveis que suas ferramentas precisam. Daí em diante, AWS CLI normal:

```bash
aws s3 mb s3://meu-bucket
aws dynamodb list-tables
```

**2. Com Docker Compose**

```yaml
services:
  floci:
    image: floci/floci:latest
    ports:
      - "4566:4566"
```

```bash
docker compose up
```

E você configura o ambiente na mão:

```bash
export AWS_ENDPOINT_URL=http://localhost:4566
export AWS_DEFAULT_REGION=us-east-1
export AWS_ACCESS_KEY_ID=test
export AWS_SECRET_ACCESS_KEY=test
```

As credenciais podem ser qualquer valor não vazio: é um emulador local, não a AWS.

**3. Sem Docker**: cada release publica um binário nativo `floci-aws` para Linux, macOS e Windows.

## Como os serviços rodam (isso decide no que dá para confiar)

| Grupo | Exemplos | Como roda |
|---|---|---|
| Em processo, leves | SQS, SNS, IAM, STS, KMS, Secrets Manager, Step Functions, EventBridge, API Gateway, Cognito, CloudFormation | Dentro do próprio floci |
| Com estado próprio | S3, DynamoDB (e DynamoDB Streams) | Dentro do floci, com sua camada de persistência |
| Contêineres reais | Lambda, RDS, ECS, EKS, EC2, ElastiCache, OpenSearch, CodeBuild | Sobe contêineres Docker de verdade |

A lista oficial passa de 120 serviços emulados. A diferença prática: um serviço «em processo» responde à API; um de «contêineres reais» executa seu código ou seu banco de dados de verdade, então se comporta bem mais perto do serviço real.

## Persistência: quatro modos

| Modo | Quando |
|---|---|
| `memory` | Testes e CI: tudo se perde ao parar, e é isso que você quer |
| `hybrid` | Desenvolvimento diário, equilíbrio entre velocidade e manter os dados |
| `persistent` | Você quer que buckets e tabelas sobrevivam a reinicializações |
| `wal` | Write-ahead log, para durabilidade mais rigorosa |

## Vindo do LocalStack: migração em um passo

Mesma porta (4566), mesmas variáveis de ambiente. Na prática, você troca a imagem e segue:

```yaml
image: floci/floci:latest
```

Armadilha conhecida: a imagem antiga `hectorvent/floci` não recebe mais atualizações. Se o seu compose ainda a usa, troque ou você fica com uma versão morta.

## Extras que vale conhecer

- **Console web** em `http://localhost:4566/_floci/ui` para inspecionar recursos. Ele sobe como contêiner auxiliar, então precisa de acesso ao socket do Docker.
- **Família de emuladores**, mesmo padrão, outra porta: AWS `4566` · Azure `4577` (floci-az) · GCP `4588` (floci-gcp) · OCI `4599` (floci-oci).
- Qualquer região publicada da AWS funciona, em todas as partições.

## O que saber antes de confiar

- Bedrock Runtime e Bedrock AgentCore respondem com respostas de preenchimento (stub): servem para testar a fiação, não para chamar modelos de verdade.
- Os serviços de contêineres reais montam o socket do Docker. É a permissão que faz tudo funcionar, e também a que vale entender antes de ativar numa máquina compartilhada.
- Seus testes passarem contra o emulador não garante que passem contra a AWS real. Use para iterar rápido e barato; valide contra a AWS antes de produção.

## Links

- Repositório: https://github.com/floci-io/floci
- Documentação: https://floci.io/floci/
- Releases: https://github.com/floci-io/floci/releases

---

Guía elaborada a partir de la documentación oficial del proyecto · Guia elaborada a partir da documentação oficial do projeto. El software pertenece a sus autores · O software pertence aos seus autores.
