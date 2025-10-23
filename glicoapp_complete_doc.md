# 🩺 GlicoApp - Documentação Completa do Projeto

> Plataforma inteligente de acompanhamento de glicemia, conectando pacientes e profissionais de saúde através de chatbot WhatsApp, backend automatizado com IA e dashboard web interativo.

**Versão:** 1.0  
**Data:** Outubro 2025  
**Status:** Planejamento e Arquitetura

---

## 📑 Índice

1. [Visão Geral](#1-visão-geral)
2. [Arquitetura do Sistema](#2-arquitetura-do-sistema)
3. [Respostas às Questões Estratégicas](#3-respostas-às-questões-estratégicas)
4. [Stack Tecnológica](#4-stack-tecnológica)
5. [Modelo de Dados](#5-modelo-de-dados)
6. [Cronograma de Desenvolvimento](#6-cronograma-de-desenvolvimento)
7. [Estrutura de Issues GitHub](#7-estrutura-de-issues-github)
8. [Estrutura de Diretórios](#8-estrutura-de-diretórios)
9. [Guia de Desenvolvimento](#9-guia-de-desenvolvimento)
10. [Métricas de Sucesso](#10-métricas-de-sucesso)
11. [Próximos Passos](#11-próximos-passos)

---

## 1. Visão Geral

### 1.1 Objetivo do Projeto

O GlicoApp é uma plataforma de saúde digital que visa facilitar o monitoramento contínuo de glicemia para pacientes diabéticos, oferecendo:

- **Para Pacientes:**
  - Registro simplificado via WhatsApp
  - Alertas automáticos de hipo/hiperglicemia
  - Relatórios personalizados com insights de IA
  - Acompanhamento visual de tendências

- **Para Profissionais de Saúde:**
  - Dashboard centralizado de pacientes
  - Alertas de casos críticos
  - Relatórios analíticos automáticos
  - Comunicação direta via WhatsApp

- **Para o Sistema de Saúde:**
  - Compliance com LGPD
  - Dados estruturados para análises epidemiológicas
  - Redução de custos com internações evitáveis

### 1.2 Principais Funcionalidades

#### Fase 1 (MVP - Semanas 1-4)
- ✅ Sistema de autenticação JWT
- ✅ CRUD de leituras de glicemia
- ✅ Dashboard básico para paciente
- ✅ API REST documentada

#### Fase 2 (Semanas 5-8)
- ✅ Chatbot WhatsApp (Evolution API)
- ✅ Registro de glicemia via texto
- ✅ Lembretes automáticos
- ✅ Alertas críticos

#### Fase 3 (Semanas 9-12)
- ✅ Integração com IA (CrewAI + OpenAI)
- ✅ Geração automática de relatórios
- ✅ Análise preditiva de padrões
- ✅ Dashboard com analytics avançado

#### Fase 4 (Semanas 13-16)
- ✅ Painel médico completo
- ✅ Gestão de pacientes
- ✅ Sistema de alertas para médicos
- ✅ Comunicação médico-paciente

#### Fase 5 (Semanas 17-20)
- ✅ Implementação de MFA
- ✅ Compliance LGPD completo
- ✅ Auditoria e logs centralizados
- ✅ Pentesting e hardening

#### Fase 6 (Semanas 21-24)
- ✅ Deploy em produção
- ✅ Observabilidade completa
- ✅ Otimização de performance
- ✅ Documentação final

---

## 2. Arquitetura do Sistema

### 2.1 Visão Geral da Arquitetura

```
┌─────────────────────────────────────────────────────────────┐
│                        CAMADA DE ENTRADA                      │
├─────────────────────────────────────────────────────────────┤
│  WhatsApp (Evolution API) ──→ Webhook Handler                │
│  Dashboard Web (React)    ──→ REST API                       │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│                    API GATEWAY / LOAD BALANCER               │
│                    (Nginx + Redis Rate Limiter)              │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│                      CAMADA DE APLICAÇÃO                      │
├─────────────────────────────────────────────────────────────┤
│  Django REST Framework (Gunicorn)                            │
│  ├─ Auth Service (JWT + Refresh Tokens)                      │
│  ├─ Patient Service                                          │
│  ├─ Glucose Reading Service                                  │
│  ├─ Report Service                                           │
│  ├─ Notification Service                                     │
│  └─ AI Agent Orchestrator (CrewAI)                           │
└────────────────────┬────────────────────────────────────────┘
                     │
            ┌────────┴────────┐
            ▼                 ▼
┌─────────────────┐  ┌─────────────────┐
│  TASK QUEUE     │  │   CACHE LAYER   │
│  RabbitMQ       │  │   Redis         │
│  + Celery       │  │   (Sessions,    │
│                 │  │    Cache, Queue)│
└────────┬────────┘  └─────────────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│         WORKERS (Celery)                 │
│  ├─ AI Analysis Worker                   │
│  ├─ Report Generator Worker              │
│  ├─ Notification Worker                  │
│  └─ Data Cleanup Worker                  │
└─────────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────────────────┐
│                      CAMADA DE DADOS                          │
├─────────────────────────────────────────────────────────────┤
│  PostgreSQL (Primary + Read Replicas)                        │
│  ├─ Users & Auth                                             │
│  ├─ Glucose Readings (Time-series optimized)                 │
│  ├─ Reports & Analytics                                      │
│  └─ Audit Logs (LGPD Compliance)                             │
│                                                               │
│  S3-Compatible Storage (MinIO/AWS S3)                        │
│  └─ Encrypted Reports, Attachments                           │
└─────────────────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────────────────┐
│                   OBSERVABILIDADE                             │
├─────────────────────────────────────────────────────────────┤
│  Logs: ELK Stack (Elasticsearch, Logstash, Kibana)          │
│  Metrics: Prometheus + Grafana                               │
│  APM: Sentry (Error Tracking)                                │
│  Backup: PostgreSQL WAL + S3 Snapshots (Daily)              │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 Fluxo de Dados Principal

#### 2.2.1 Registro de Glicemia via WhatsApp

```
1. Usuário envia: "120 mg/dL" via WhatsApp
2. Evolution API recebe mensagem
3. Webhook POST → Django endpoint /api/chatbot/webhook/
4. Parser extrai valor: 120 mg/dL
5. Valida dados e usuário
6. Salva GlucoseReading no PostgreSQL
7. Dispara signal: glucose_reading_created
8. Celery task: Verifica se é crítico
9. Se crítico: Envia alerta via WhatsApp + Email médico
10. Responde usuário: "Glicemia registrada: 120 mg/dL ✅"
11. Atualiza cache (última leitura)
```

#### 2.2.2 Geração de Relatório

```
1. Celery Beat dispara task semanal
2. Worker: report_generator.generate_weekly_report(patient_id)
3. Busca leituras dos últimos 7 dias (PostgreSQL)
4. Calcula métricas: média, TIR, CV, HbA1c estimado
5. CrewAI Agent analisa padrões e gera insights
6. Gera PDF (WeasyPrint)
7. Upload PDF → S3
8. Salva registro Report no DB
9. Celery task: Envia notificação WhatsApp + Email
10. Atualiza cache: report_ready
```

### 2.3 Componentes Principais

#### 2.3.1 Backend (Django)

| Componente | Responsabilidade | Tecnologia |
|------------|------------------|------------|
| API REST | Endpoints CRUD | Django REST Framework |
| Autenticação | JWT Auth | djangorestframework-simplejwt |
| Validações | Business rules | Django Models + Serializers |
| Permissões | RBAC | Custom DRF Permissions |
| Background Jobs | Tasks assíncronas | Celery + RabbitMQ |
| IA Integration | Análise e insights | CrewAI + OpenAI API |

#### 2.3.2 Frontend (React)

| Componente | Responsabilidade | Tecnologia |
|------------|------------------|------------|
| UI Components | Interface visual | React + TypeScript |
| Styling | Design system | Bootstrap 5 |
| State Management | Estado global | Zustand |
| Data Fetching | API calls + cache | Axios + React Query |
| Charts | Visualização de dados | Recharts |
| Routing | Navegação | React Router v6 |

#### 2.3.3 Chatbot (WhatsApp)

| Componente | Responsabilidade | Tecnologia |
|------------|------------------|------------|
| Mensageria | Envio/recebimento | Evolution API |
| NLP | Parsing de mensagens | Custom + LLM |
| Contexto | Conversação | Redis (sessions) |
| Templates | Mensagens pré-formatadas | Django Templates |

---

## 3. Respostas às Questões Estratégicas

### 3.1 Questões Técnicas

#### 3.1.1 Versionamento da API

**Estratégia:** Semantic Versioning (SemVer) + URL Versioning

```
Estrutura de URLs:
/api/v1/patients/
/api/v2/patients/

Regras:
- v1: Versão inicial (suporte mínimo 12 meses após v2)
- v2: Breaking changes apenas
- Headers opcionais: Accept: application/vnd.glicoapp.v1+json

Versionamento de Modelos:
- Migrations automáticas (Django)
- Backward compatibility para 2 versões anteriores
- Deprecation warnings com 3 meses de antecedência
```

**Exemplo de Breaking Change:**
```python
# v1: Campo "glucose_value"
class GlucoseReadingSerializerV1:
    glucose_value = IntegerField()

# v2: Renomeado para "value" (breaking change)
class GlucoseReadingSerializerV2:
    value = IntegerField()
```

#### 3.1.2 Backup e Recuperação de Dados

**Estratégia 3-2-1:**
- 3 cópias dos dados
- 2 tipos de mídia diferentes
- 1 cópia off-site

**Implementação:**

**PostgreSQL:**
```yaml
Contínuo (WAL):
  - Write-Ahead Logging habilitado
  - Point-in-Time Recovery (PITR)
  - Streaming replication para standby

Snapshots:
  - Diários: Retenção 30 dias (S3 Standard)
  - Semanais: Retenção 12 semanas (S3 Standard)
  - Mensais: Retenção 1 ano (S3 Glacier)

Automação:
  - Cron job: 2h da manhã (horário de menor uso)
  - Script: pg_dump + gzip + s3 upload
  - Verificação de integridade: checksum MD5
```

**Redis:**
```yaml
RDB Snapshots:
  - Frequência: A cada 6 horas
  - Retenção: 7 dias

AOF (Append-Only File):
  - Habilitado para persistência
  - Fsync: everysec (balanço performance/segurança)
```

**Arquivos (S3):**
```yaml
Configuração:
  - Versionamento habilitado
  - Cross-region replication (backup secundário)
  - Lifecycle policies: Archive após 90 dias
```

**Objetivos de Recuperação:**
```
RTO (Recovery Time Objective): < 1 hora
RPO (Recovery Point Objective): < 15 minutos
Testes: Mensais (disaster recovery drill)
```

#### 3.1.3 Métricas de Monitoramento

**Application Metrics (Prometheus):**

```yaml
Performance:
  - http_request_duration_seconds (histogram)
    * Buckets: 0.1, 0.5, 1, 2, 5 segundos
    * Labels: method, endpoint, status
  - http_requests_total (counter)
  - http_requests_in_flight (gauge)

Database:
  - django_db_query_duration_seconds
  - django_db_connections_total
  - django_db_connections_in_use

Celery:
  - celery_task_duration_seconds
  - celery_tasks_total
  - celery_queue_length
  - celery_workers_active

Cache:
  - redis_hits_total
  - redis_misses_total
  - redis_memory_usage_bytes

Custom Business Metrics:
  - glucose_readings_total (counter)
  - glucose_alerts_triggered_total (counter)
  - reports_generated_total (counter)
  - active_users_gauge (gauge)
```

**Alerting Rules (Prometheus):**

```yaml
Critical:
  - name: HighErrorRate
    condition: http_error_rate > 5%
    duration: 5m
    action: PagerDuty + Slack

  - name: HighQueueLength
    condition: celery_queue_length > 1000
    duration: 10m
    action: PagerDuty

Warning:
  - name: SlowAPIResponse
    condition: http_p95_duration > 2s
    duration: 10m
    action: Slack

  - name: HighDatabaseConnections
    condition: db_connections_usage > 80%
    duration: 5m
    action: Slack

  - name: LowCacheHitRate
    condition: cache_hit_rate < 70%
    duration: 15m
    action: Slack (informativo)
```

**Grafana Dashboards:**

1. **Application Overview:**
   - Request rate (req/s)
   - Error rate (%)
   - Response time (p50, p95, p99)
   - Active users

2. **Infrastructure:**
   - CPU, Memory, Disk I/O
   - Network throughput
   - Database performance
   - Cache performance

3. **Business Metrics:**
   - Readings per day (trend)
   - Alert distribution
   - User engagement
   - Report generation time

#### 3.1.4 Gestão de Logs Centralizados

**Stack:** ELK (Elasticsearch, Logstash, Kibana)

**Estrutura de Logs:**

```json
{
  "timestamp": "2024-01-15T10:30:45.123Z",
  "level": "INFO",
  "service": "api",
  "logger": "apps.readings.views",
  "trace_id": "abc123-def456-ghi789",
  "span_id": "span-001",
  "user_id": "hashed_user_id",
  "action": "glucose_reading_created",
  "resource_type": "GlucoseReading",
  "resource_id": "uuid-reading-id",
  "ip_address": "192.168.x.x",
  "user_agent": "Mozilla/5.0...",
  "request_method": "POST",
  "request_path": "/api/v1/readings/",
  "response_status": 201,
  "response_time_ms": 145,
  "metadata": {
    "glucose_value": 120,
    "is_critical": false
  }
}
```

**Configuração Django Logging:**

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'json': {
            '()': 'pythonjsonlogger.jsonlogger.JsonFormatter',
            'format': '%(timestamp)s %(level)s %(name)s %(message)s'
        }
    },
    'handlers': {
        'logstash': {
            'level': 'INFO',
            'class': 'logstash.TCPLogstashHandler',
            'host': 'logstash',
            'port': 5000,
            'version': 1,
        },
    },
    'loggers': {
        'django': {
            'handlers': ['logstash'],
            'level': 'INFO',
        },
        'apps': {
            'handlers': ['logstash'],
            'level': 'INFO',
        },
    }
}
```

**Retenção de Logs:**

```yaml
Hot Storage (Elasticsearch):
  - Período: 7 dias
  - Performance: Máxima (SSD)
  - Uso: Debugging em tempo real

Warm Storage (Elasticsearch):
  - Período: 30 dias
  - Performance: Média (HDD)
  - Uso: Análises recentes

Cold Storage (S3):
  - Período: 1 ano
  - Performance: Lenta
  - Uso: Compliance LGPD, auditorias

Delete:
  - Após: 1 ano
  - Exceção: Logs de auditoria críticos (manter indefinidamente)
```

**Anonimização de PII:**

```python
# Middleware para hash de dados sensíveis
class PIIAnonymizationMiddleware:
    def process_log_record(self, record):
        if 'user_id' in record:
            record['user_id'] = hashlib.sha256(
                record['user_id'].encode()
            ).hexdigest()[:16]
        
        if 'ip_address' in record:
            # Ofuscar últimos 2 octetos após 24h
            if record['timestamp'] < now() - timedelta(days=1):
                record['ip_address'] = obscure_ip(record['ip_address'])
        
        return record
```

**Kibana Dashboards:**

1. **Security Dashboard:**
   - Failed login attempts
   - Unauthorized access attempts
   - Anomalous activity patterns

2. **Performance Dashboard:**
   - Slow queries
   - High response times
   - Error spikes

3. **Business Dashboard:**
   - User activity patterns
   - Feature usage
   - Critical alerts timeline

---

### 3.2 Questões de Segurança

#### 3.2.1 Níveis de Autenticação

**Hierarquia de Acesso:**

```
┌─────────────────────────────────────────────┐
│ NÍVEL 1: PACIENTE                            │
├─────────────────────────────────────────────┤
│ Autenticação: JWT + Refresh Token            │
│ MFA: Opcional (recomendado)                  │
│ ├─ SMS (Twilio)                              │
│ ├─ TOTP (Google Authenticator)              │
│ └─ Email (fallback)                          │
│                                               │
│ Permissões:                                   │
│ ✓ CRUD próprios dados                        │
│ ✓ Visualizar próprios relatórios             │
│ ✓ Configurar notificações                    │
│ ✗ Acessar dados de outros pacientes          │
│ ✗ Gerenciar usuários                         │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│ NÍVEL 2: PROFISSIONAL DE SAÚDE              │
├─────────────────────────────────────────────┤
│ Autenticação: JWT + Refresh Token            │
│ MFA: OBRIGATÓRIO                             │
│ ├─ TOTP (preferencial)                       │
│ └─ SMS (backup)                              │
│                                               │
│ Permissões:                                   │
│ ✓ Visualizar pacientes sob supervisão        │
│ ✓ Gerar relatórios de pacientes              │
│ ✓ Enviar mensagens a pacientes               │
│ ✓ Configurar metas personalizadas            │
│ ✗ Modificar dados de leitura                 │
│ ✗ Gerenciar outros médicos                   │
│                                               │
│ Audit: TODAS as ações registradas            │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│ NÍVEL 3: ADMINISTRADOR                       │
├─────────────────────────────────────────────┤
│ Autenticação: JWT + Refresh Token            │
│ MFA: OBRIGATÓRIO                             │
│ IP Whitelist: OBRIGATÓRIO                    │
│                                               │
│ Permissões:                                   │
│ ✓ Gerenciamento completo do sistema          │
│ ✓ Acesso a logs de auditoria                 │
│ ✓ Configurações globais                      │
│ ✓ Gerenciamento de usuários                  │
│                                               │
│ Audit: LOG COMPLETO com alertas em tempo real│
│ Session: Timeout reduzido (15 minutos)       │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│ NÍVEL 4: API EXTERNA (Third-party)          │
├─────────────────────────────────────────────┤
│ Autenticação: API Key + OAuth2               │
│ Rate Limiting: Agressivo (100 req/hora)      │
│                                               │
│ Permissões: Granulares por scope             │
│ ├─ readings:read                             │
│ ├─ readings:write                            │
│ ├─ reports:read                              │
│ └─ patient:read (dados limitados)            │
│                                               │
│ Monitoring: Uso monitorado em tempo real     │
└─────────────────────────────────────────────┘
```

**Implementação JWT:**

```python
# settings.py
SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=15),  # Curto para segurança
    'REFRESH_TOKEN_LIFETIME': timedelta(days=7),
    'ROTATE_REFRESH_TOKENS': True,
    'BLACKLIST_AFTER_ROTATION': True,
    'UPDATE_LAST_LOGIN': True,
    
    'ALGORITHM': 'HS256',
    'SIGNING_KEY': env('JWT_SECRET_KEY'),
    'VERIFYING_KEY': None,
    
    'AUTH_HEADER_TYPES': ('Bearer',),
    'USER_ID_FIELD': 'id',
    'USER_ID_CLAIM': 'user_id',
    
    'AUTH_TOKEN_CLASSES': ('rest_framework_simplejwt.tokens.AccessToken',),
    'TOKEN_TYPE_CLAIM': 'token_type',
}
```

**Password Policy:**

```python
AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
        'OPTIONS': {'min_length': 12}  # Mínimo 12 caracteres
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
    {
        'NAME': 'apps.accounts.validators.ComplexityValidator',
        'OPTIONS': {
            'require_uppercase': True,
            'require_lowercase': True,
            'require_digits': True,
            'require_special': True,
        }
    },
]
```

#### 3.2.2 Proteção de Dados (LGPD)

**Medidas Técnicas:**

**1. Encryption**

```yaml
At Rest (Dados em Repouso):
  PostgreSQL:
    - TDE (Transparent Data Encryption)
    - Encryption algorithm: AES-256
    - Key rotation: Trimestral
    
  S3 Storage:
    - Server-side encryption: AES-256
    - KMS (Key Management Service)
    - Bucket policies: Private only
  
  Backup:
    - Encrypted before upload
    - Separate encryption keys
    - Multi-region redundancy

In Transit (Dados em Trânsito):
  - TLS 1.3 obrigatório (TLS 1.2 mínimo)
  - Certificate pinning (mobile apps)
  - HSTS enabled (max-age=31536000)
  - Perfect Forward Secrecy (PFS)

Application Level:
  - Sensitive fields: django-cryptography
  - Password hashing: Argon2
  - API tokens: Fernet encryption
```

**2. Pseudonimização**

```python
# Exemplo de implementação
class User(AbstractBaseUser):
    id = UUIDField(primary_key=True, default=uuid4)  # UUID v4 (não sequencial)
    email_hash = CharField(max_length=64)  # SHA-256 hash
    
    def set_email(self, email):
        self.email_hash = hashlib.sha256(
            f"{email}{settings.EMAIL_SALT}".encode()
        ).hexdigest()
    
    def get_anonymized_id(self):
        """ID anonimizado para logs"""
        return hashlib.sha256(
            f"{self.id}{settings.ANONYMIZATION_SALT}".encode()
        ).hexdigest()[:16]
```

**3. Controle de Acesso**

```python
# RBAC (Role-Based Access Control)
class PermissionMatrix:
    PATIENT = {
        'glucose_readings': ['create', 'read_own', 'update_own', 'delete_own'],
        'reports': ['read_own'],
        'profile': ['read_own', 'update_own'],
    }
    
    DOCTOR = {
        'glucose_readings': ['read_assigned'],
        'reports': ['read_assigned', 'generate'],
        'patients': ['read_assigned', 'update_notes'],
        'notifications': ['send_to_patients'],
    }
    
    ADMIN = {
        '*': ['*'],  # Acesso completo com auditoria
    }

# Implementação com decorator
@require_permissions('glucose_readings.read_own')
def get_my_readings(request):
    return GlucoseReading.objects.filter(patient__user=request.user)
```

**4. Auditoria**

```python
# Signal para audit log automático
@receiver(post_save)
def log_model_change(sender, instance, created, **kwargs):
    if sender in AUDITED_MODELS:
        AuditLog.objects.create(
            user=get_current_user(),
            action='CREATE' if created else 'UPDATE',
            resource_type=sender.__name__,
            resource_id=instance.pk,
            changes=get_model_changes(instance),
            ip_address=get_current_ip(),
            timestamp=now()
        )
```

**5. Consentimento (LGPD Art. 7)**

```python
class Consent(models.Model):
    user = ForeignKey(User, on_delete=CASCADE)
    consent_type = CharField(choices=CONSENT_TYPES)
    # data_collection, data_processing, data_sharing, marketing
    
    version = CharField(max_length=10)  # Versão dos termos
    granted_at = DateTimeField(auto_now_add=True)
    revoked_at = DateTimeField(null=True, blank=True)
    
    # Evidência do consentimento
    ip_address = GenericIPAddressField()
    user_agent = TextField()
    consent_text_hash = CharField(max_length=64)  # SHA-256 dos termos
    
    class Meta:
        unique_together = ['user', 'consent_type', 'version']
```

**Compliance Checklist:**

```
✅ Art. 6 LGPD: Base legal definida (consentimento + legítimo interesse)
✅ Art. 7 LGPD: Consentimento livre, informado e inequívoco
✅ Art. 9 LGPD: Direito de acesso aos dados garantido
✅ Art. 18 LGPD: Direitos do titular implementados:
   ├─ Confirmação de tratamento
   ├─ Acesso aos dados
   ├─ Correção de dados incompletos/inexatos
   ├─ Anonimização/bloqueio/eliminação
   ├─ Portabilidade
   ├─ Eliminação (direito ao esquecimento)
   ├─ Informação sobre compartilhamento
   ├─ Revogação do consentimento
   └─ Oposição ao tratamento
✅ Art. 46 LGPD: Medidas de segurança técnicas e administrativas
✅ Art. 48 LGPD: Comunicação de incidente < 72 horas
✅ Art. 41 LGPD: DPO (Data Protection Officer) designado
```

#### 3.2.3 Política de Retenção de Dados

```yaml
Leituras de Glicemia:
  Período: 5 anos
  Razão: Histórico médico necessário
  Processo:
    - Anos 0-2: Hot storage (acesso rápido)
    - Anos 2-5: Cold storage (S3 Glacier)
    - Após 5 anos: Oferece export + delete ou anonimização

Relatórios Médicos:
  Período: 20 anos
  Razão: Prontuário eletrônico (CFM Resolução 1.821/2007)
  Processo:
    - Anos 0-5: Hot storage
    - Anos 5-20: Cold storage (S3 Glacier Deep Archive)
    - Anonimização após 20 anos (mantém dados epidemiológicos)

Logs de Acesso:
  Período: 1 ano
  Razão: Auditoria LGPD (Art. 37)
  Processo:
    - Meses 0-3: Elasticsearch (análise rápida)
    - Meses 3-12: S3 (compressed)
    - Após 1 ano: Delete automático

Dados de Autenticação:
  Período: Enquanto conta ativa
  Razão: Segurança e acesso
  Processo:
    - Conta ativa: Mantém todos os dados
    - Conta inativa (> 2 anos sem login): Email de aviso
    - Conta inativa (> 3 anos): Anonimização + soft delete

Mensagens do Chatbot:
  Período: 90 dias
  Razão: Contexto de atendimento
  Processo:
    - Dias 0-30: Hot storage (contexto ativo)
    - Dias 30-90: Cold storage
    - Após 90 dias: Delete automático
    - Exceção: Mensagens com dados clínicos relevantes → migra para notes

Dados de Usuários Inativos:
  Período: 3 anos após última atividade
  Razão: Compliance + possível retorno
  Processo:
    Ano 0: Conta ativa
    Ano 1: Soft delete (flag deleted_at)
    Ano 2: Email: "Sua conta será excluída em 1 ano"
    Ano 3: Anonimização completa:
      - user_id → mantém (UUID)
      - email → hash irreversível
      - phone → null
      - full_name → "Usuario Anonimizado"
      - glucose_readings → mantém (dados epidemiológicos)
```

**Implementação Automática:**

```python
# Celery periodic task
@app.task
def cleanup_expired_data():
    """Executa diariamente às 3h da manhã"""
    
    # 1. Delete mensagens antigas do chatbot
    cutoff_date = now() - timedelta(days=90)
    ChatbotConversation.objects.filter(
        expires_at__lt=cutoff_date
    ).delete()
    
    # 2. Anonimiza usuários inativos
    inactive_threshold = now() - timedelta(days=3*365)
    inactive_users = User.objects.filter(
        last_login__lt=inactive_threshold,
        is_anonymized=False
    )
    
    for user in inactive_users:
        anonymize_user(user)
    
    # 3. Move dados antigos para cold storage
    old_readings_date = now() - timedelta(days=2*365)
    old_readings = GlucoseReading.objects.filter(
        measured_at__lt=old_readings_date,
        archived=False
    )
    
    for reading in old_readings.iterator(chunk_size=1000):
        archive_to_glacier(reading)
        reading.archived = True
        reading.save()
```

---

### 3.3 Questões de Funcionalidade

#### 3.3.1 Relatórios Automáticos

**Tipos de Relatórios:**

**1. Diário (Paciente)**

```yaml
Frequência: Gerado às 21h de cada dia

Conteúdo:
  - Resumo das medições do dia
  - Média de glicemia
  - Número de episódios de hipo/hiperglicemia
  - Horários das medições
  - Comparativo com dia anterior
  - Sugestões de ajuste (se aplicável)

Formato:
  - Push notification (WhatsApp)
  - In-app (dashboard)

Exemplo de Mensagem:
  "📊 Resumo do Dia (15/01/2024)
  
  🩸 Medições: 5 de 6 planejadas
  📈 Média: 135 mg/dL
  ⚠️ 1 episódio de hiperglicemia (18h: 210 mg/dL)
  
  💡 Dica: Considere reduzir carboidratos no jantar.
  
  Ver relatório completo: [link]"
```

**2. Semanal (Paciente + Médico)**

```yaml
Frequência: Gerado às segundas-feiras, 8h

Conteúdo:
  Métricas:
    - Média semanal
    - Desvio padrão
    - Coeficiente de variação (CV%)
    - Time in Range (TIR)
    - Time Above Range (TAR)
    - Time Below Range (TBR)
  
  Gráficos:
    - Tendência dos 7 dias (linha)
    - Distribuição por faixa (pizza)
    - Comparativo com semana anterior
  
  Análise:
    - Padrões identificados (pré/pós refeição)
    - Horários críticos
    - Adherence rate (% medições realizadas)
  
  Recomendações (IA):
    - Ajustes sugeridos no tratamento
    - Alertas para médico (se necessário)

Formato:
  - PDF (gerado e armazenado em S3)
  - Email para paciente
  - Email para médico (se configurado)
  - Disponível no dashboard
```

**3. Mensal (Médico)**

```yaml
Frequência: Gerado no dia 1 de cada mês, 9h

Conteúdo:
  Métricas Avançadas:
    - HbA1c estimado (eAG)
    - Glucose Management Indicator (GMI)
    - Variabilidade glicêmica (MAGE, CONGA)
    - Índice de risco de hipoglicemia (LBGI)
  
  Correlações:
    - Glicemia x Medicação
    - Glicemia x Alimentação (se registrado)
    - Glicemia x Exercícios (se registrado)
  
  Gráficos Avançados:
    - AGP (Ambulatory Glucose Profile)
    - Scatter plot por horário
    - Heatmap semanal
  
  Comparativo:
    - Mês anterior
    - Média dos últimos 3 meses
    - Meta estabelecida

Formato:
  - PDF completo (10-15 páginas)
  - Dashboard interativo
  - API endpoint para sistemas externos
```

**4. Trimestral (Médico + IA)**

```yaml
Frequência: A cada 3 meses

Conteúdo:
  Análise Preditiva:
    - Tendência de evolução
    - Risco de complicações
    - Eficácia do tratamento atual
  
  Recomendações (IA - CrewAI):
    - Ajustes de medicação (sugestão para médico)
    - Mudanças no estilo de vida
    - Frequência de monitoramento
  
  Comparativo Populacional:
    - Como paciente se compara a população similar
    - Percentil de controle glicêmico
    - Benchmarks

Formato:
  - PDF executivo (5 páginas)
  - Apresentação (slides)
  - Dados estruturados (JSON para integração)
```

**Triggers e Automação:**

```python
# celery.py
from celery.schedules import crontab

app.conf.beat_schedule = {
    'generate-daily-reports': {
        'task': 'apps.reports.tasks.generate_daily_reports',
        'schedule': crontab(hour=21, minute=0),  # 21h todos os dias
    },
    'generate-weekly-reports': {
        'task': 'apps.reports.tasks.generate_weekly_reports',
        'schedule': crontab(day_of_week=1, hour=8, minute=0),  # Segunda 8h
    },
    'generate-monthly-reports': {
        'task': 'apps.reports.tasks.generate_monthly_reports',
        'schedule': crontab(day_of_month=1, hour=9, minute=0),  # Dia 1, 9h
    },
}

# tasks.py
@app.task
def generate_weekly_report(patient_id):
    """Gera relatório semanal para um paciente"""
    patient = Patient.objects.get(id=patient_id)
    
    # 1. Coleta dados dos últimos 7 dias
    end_date = now().date()
    start_date = end_date - timedelta(days=7)
    readings = GlucoseReading.objects.filter(
        patient=patient,
        measured_at__date__range=[start_date, end_date]
    )
    
    # 2. Calcula métricas
    metrics = calculate_metrics(readings)
    
    # 3. IA analisa padrões
    ai_agent = GlucoseAnalyzerAgent()
    insights = ai_agent.analyze(readings, metrics)
    
    # 4. Gera PDF
    pdf_path = generate_pdf_report(patient, metrics, insights)
    
    # 5. Upload para S3
    s3_url = upload_to_s3(pdf_path)
    
    # 6. Salva registro no DB
    report = Report.objects.create(
        patient=patient,
        report_type='weekly',
        period_start=start_date,
        period_end=end_date,
        file_url=s3_url,
        metrics=metrics,
        ai_insights=insights
    )
    
    # 7. Notifica paciente e médico
    send_report_notification.delay(report.id)
    
    return report.id
```

#### 3.3.2 Interface do Chatbot

**Arquitetura do Chatbot:**

```
User Message (WhatsApp)
    ↓
Evolution API (Webhook)
    ↓
Django Endpoint (/api/chatbot/webhook/)
    ↓
Message Router (identifica intenção)
    ↓
┌─────────────┬──────────────┬─────────────┬────────────┐
│   Handler   │   Handler    │   Handler   │  Handler   │
│  Reading    │   Query      │   Help      │  Settings  │
└──────┬──────┴──────┬───────┴──────┬──────┴─────┬──────┘
       │             │              │            │
       ▼             ▼              ▼            ▼
   Process      Fetch Data     Return FAQ    Update Prefs
       │             │              │            │
       └─────────────┴──────────────┴────────────┘
                     │
                     ▼
              Response Builder
                     │
                     ▼
             Evolution API (Send)
                     │
                     ▼
              User (WhatsApp)
```

**Perfis e Fluxos:**

**PERFIL 1: PACIENTE**

```yaml
Comandos Principais:

1. Registrar Glicemia:
   Inputs aceitos:
     - "120"
     - "120 mg/dL"
     - "Glicemia 120"
     - "Minha glicemia está 120"
     - "Acabei de medir: 120"
   
   Fluxo:
     User: "120"
     Bot: "📊 Glicemia registrada: 120 mg/dL ✅
          ⏰ Horário: 10:30
          📌 Contexto: Durante o dia
          
          Tudo está dentro da sua faixa ideal! 💚"
   
   Contexto Adicional (opcional):
     User: "120 após café da manhã"
     Bot: "📊 Glicemia registrada: 120 mg/dL ✅
          ⏰ Horário: 10:30
          🍽️ Contexto: Após refeição
          
          Ótimo controle pós-refeição! 👍"

2. Consultar Última Medição:
   Inputs:
     - "Última medição"
     - "Qual minha glicemia"
     - "Como estou"
   
   Resposta:
     "📊 Sua última medição:
     
     🩸 135 mg/dL
     ⏰ Hoje às 14:30
     📌 Antes do almoço
     
     Quer registrar uma nova medição?"

3. Ver Estatísticas:
   Inputs:
     - "Estatísticas"
     - "Como estou na semana"
     - "Resumo"
   
   Resposta:
     "📈 Resumo dos últimos 7 dias:
     
     📊 Média: 128 mg/dL
     ✅ Medições realizadas: 18/21 (86%)
     💚 Na faixa ideal: 72%
     ⚠️ Acima: 20%
     🔻 Abaixo: 8%
     
     Ver relatório completo: [link]"

4. Lembretes:
   Sistema Automático:
     - 7h: "☀️ Bom dia! Hora de medir sua glicemia em jejum 🩸"
     - 12h: "🍽️ Lembre-se de medir antes do almoço!"
     - 19h: "🌙 Última medição do dia! Como está sua glicemia?"
   
   Configuração:
     User: "Mudar horário dos lembretes"
     Bot: "⏰ Horários atuais:
          • 7h (jejum)
          • 12h (antes almoço)
          • 19h (antes jantar)
          
          Qual horário deseja mudar?"

5. Perguntas Frequentes:
   Inputs:
     - "Ajuda"
     - "O que você faz"
     - "Comandos"
   
   Resposta:
     "🤖 Eu posso te ajudar com:
     
     🩸 Registrar glicemia: digite o valor
     📊 Ver última medição: 'última medição'
     📈 Ver estatísticas: 'resumo'
     ⏰ Configurar lembretes: 'lembretes'
     📱 Ver relatórios: 'relatórios'
     ❓ Tirar dúvidas: 'faq'
     
     Ou simplesmente converse comigo!"

6. IA Conversacional (CrewAI):
   User: "Por que minha glicemia fica alta depois do almoço?"
   Bot: "🤔 Analisando seu histórico...
   
        Notei que nas últimas 2 semanas, sua glicemia
        sobe em média 60 mg/dL após o almoço.
        
        Possíveis causas:
        • Quantidade de carboidratos na refeição
        • Tipo de carboidrato (simples vs complexos)
        • Timing da medicação
        
        💡 Sugestão: Tente reduzir carboidratos no
        almoço por 3 dias e veja se há melhora.
        Caso persista, converse com seu médico.
        
        Quer mais detalhes?"
```

**PERFIL 2: MÉDICO**

```yaml
Comandos Principais:

1. Status dos Pacientes:
   Input: "Status pacientes"
   
   Resposta:
     "👨‍⚕️ Resumo dos seus pacientes:
     
     ✅ Controlados: 12
     ⚠️ Atenção: 3
     🚨 Críticos: 1
     
     Casos que precisam de atenção:
     
     🚨 João Silva
     Última leitura: 280 mg/dL (há 2h)
     3 episódios de hiperglicemia hoje
     [Ver detalhes] [Ligar para paciente]
     
     ⚠️ Maria Santos
     Não registra medições há 3 dias
     [Enviar lembrete] [Ver histórico]"

2. Detalhes de Paciente:
   Input: "Paciente João Silva"
   
   Resposta:
     "👤 João Silva (45 anos)
     
     📊 Última medição: 180 mg/dL (há 1h)
     📈 Média 7 dias: 165 mg/dL
     📉 Tendência: ↗️ Subindo
     
     🎯 Meta: 70-140 mg/dL
     ✅ TIR: 58% (abaixo do ideal)
     
     ⚠️ Alertas:
     • 3 hiperglicemias hoje
     • Variabilidade alta (CV: 42%)
     
     [Ver relatório] [Ajustar tratamento] [Mensagem]"

3. Relatórios:
   Input: "Relatório semanal Maria Santos"
   
   Resposta:
     "📄 Gerando relatório semanal...
     
     ✅ Relatório pronto!
     
     Destaques:
     • Média: 135 mg/dL (↓ 12 mg/dL vs semana anterior)
     • TIR: 68% (↑ 10%)
     • Adherence: 90%
     
     📎 PDF: [link]
     📧 Enviar por email: [enviar]"

4. Alertas Críticos:
   Sistema Automático:
     "🚨 ALERTA CRÍTICO
     
     Paciente: Carlos Oliveira
     Glicemia: 45 mg/dL (HIPOGLICEMIA)
     Horário: 14:35
     
     [Ligar agora] [Ver localização] [Enviar protocolo]"

5. Comunicação com Paciente:
   Input: "Mensagem para João Silva: Vamos ajustar sua
          insulina. A partir de amanhã, aumente 2 unidades..."
   
   Bot: "✅ Mensagem