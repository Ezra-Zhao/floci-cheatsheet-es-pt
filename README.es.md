# floci · Guía rápida en español

Proyecto original: [floci-io/floci](https://github.com/floci-io/floci) · [Português](README.pt.md)


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

