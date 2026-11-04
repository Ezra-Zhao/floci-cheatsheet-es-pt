# floci · Guía rápida en español y portugués

[![Repo original](https://img.shields.io/github/stars/floci-io/floci?style=social)](https://github.com/floci-io/floci/stargazers) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> 🌍 Español primero · Português mais abaixo · Proyecto original: [floci-io/floci](https://github.com/floci-io/floci)

---

# 🇪🇸 Español

## Qué es floci

Un emulador local y gratuito de AWS. Levanta servicios con la forma de AWS en tu propia máquina, en `http://localhost:4566`, sin cuenta de AWS, sin token de autenticación y sin funciones bloqueadas tras un plan de pago. Tu AWS CLI, tus SDK, Terraform, CDK u OpenTofu y tus tests con Testcontainers siguen igual: solo cambia el endpoint.

Llega en el momento justo: la edición comunitaria de LocalStack dejó de actualizarse en marzo de 2026 y pasó a exigir token. Floci ocupa ese hueco con licencia MIT.

## floci frente a LocalStack Community

| | floci | LocalStack Community |
|---|---|---|
| Token de autenticación | No | Sí |
| Actualizaciones de seguridad | Sí | Congeladas |
| Tiempo de arranque | ~24 ms* | ~3,3 s |
| Memoria en reposo | ~13 MiB* | ~143 MiB |
| Tamaño de imagen Docker | ~90 MB | ~1,0 GB |
| Licencia | MIT | Restringida |
| API Gateway v2 / HTTP API | Sí | No |

\* Cifras de la tabla comparativa del propio proyecto. Una medición independiente situó el arranque en torno a 130 ms: sigue siendo muy rápido, pero conviene saber de dónde sale cada número.

## Tres formas de arrancarlo

**1. Con la CLI oficial (la más corta)**

```bash
floci start
eval $(floci env)
```

`floci env` deja exportadas las variables que tus herramientas necesitan. A partir de ahí, AWS CLI normal:

```bash
aws s3 mb s3://mi-bucket
aws dynamodb list-tables
```

**2. Con Docker Compose**

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

Y configuras el entorno a mano:

```bash
export AWS_ENDPOINT_URL=http://localhost:4566
export AWS_DEFAULT_REGION=us-east-1
export AWS_ACCESS_KEY_ID=test
export AWS_SECRET_ACCESS_KEY=test
```

Las credenciales pueden ser cualquier valor no vacío: son un emulador local, no AWS.

**3. Sin Docker**: cada release publica un binario nativo `floci-aws` para Linux, macOS y Windows.

## Cómo se ejecutan los servicios (esto decide qué puedes fiarle)

| Grupo | Ejemplos | Cómo corre |
|---|---|---|
| En proceso, ligeros | SQS, SNS, IAM, STS, KMS, Secrets Manager, Step Functions, EventBridge, API Gateway, Cognito, CloudFormation | Dentro del propio floci |
| Con estado propio | S3, DynamoDB (y DynamoDB Streams) | Dentro de floci, con su capa de persistencia |
| Contenedores reales | Lambda, RDS, ECS, EKS, EC2, ElastiCache, OpenSearch, CodeBuild | Levanta contenedores Docker de verdad |

La lista oficial supera los 120 servicios emulados. La diferencia práctica: un servicio «en proceso» responde al API; uno de «contenedores reales» ejecuta tu código o tu base de datos de verdad, así que se comporta mucho más cerca del servicio real.

## Persistencia: cuatro modos

| Modo | Cuándo |
|---|---|
| `memory` | Tests y CI: todo se pierde al parar, y es lo que quieres |
| `hybrid` | Desarrollo diario, equilibrio entre velocidad y conservar datos |
| `persistent` | Quieres que buckets y tablas sobrevivan a reinicios |
| `wal` | Write-ahead log, para durabilidad más estricta |

## Vienes de LocalStack: migración en un paso

Mismo puerto (4566), mismas variables de entorno. En la práctica, cambias la imagen y sigues:

```yaml
image: floci/floci:latest
```

Trampa conocida: la imagen antigua `hectorvent/floci` ya no recibe actualizaciones. Si tu compose la sigue usando, cámbiala o te quedarás con una versión muerta.

## Extras que conviene conocer

- **Consola web** en `http://localhost:4566/_floci/ui` para inspeccionar recursos. Arranca como contenedor auxiliar, así que necesita acceso al socket de Docker.
- **Familia de emuladores**, mismo patrón, otro puerto: AWS `4566` · Azure `4577` (floci-az) · GCP `4588` (floci-gcp) · OCI `4599` (floci-oci).
- Cualquier región publicada de AWS sirve, en todas las particiones.

## Lo que conviene saber antes de confiar

- Bedrock Runtime y Bedrock AgentCore responden con respuestas de relleno (stub): sirven para probar el cableado, no para llamar a modelos de verdad.
- Los servicios de contenedores reales montan el socket de Docker. Es el permiso que hace posible que funcionen, y también el que conviene entender antes de activarlo en una máquina compartida.
- Que tus tests pasen contra el emulador no garantiza que pasen contra AWS real. Úsalo para iterar rápido y barato; valida contra AWS antes de producción.

## Enlaces

- Repositorio: https://github.com/floci-io/floci
- Documentación: https://floci.io/floci/
- Releases: https://github.com/floci-io/floci/releases

---

# 🇧🇷 Português

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
