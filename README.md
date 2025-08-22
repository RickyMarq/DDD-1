# Dinâmica: Design Estratégico do Projeto

## Objetivo
Identificar os subdomínios do projeto, classificá-los (Core, Supporting, Generic) e desenhar os bounded contexts, incluindo suas interações. Esse exercício ajudará a criar uma visão clara e estratégica do domínio.

---

## 1. Nome do Projeto
**[Orion AI]**

---

## 2. Objetivo Principal do Projeto
Democratizar o acesso a dados contábeis e de negócios por meio de uma solução baseada em IA

---

## 3. Identificação dos Subdomínios


Subdomínio	Descrição	Tipo || 
Processamento de Linguagem Natural	Interpreta perguntas em português e converte para consultas estruturadas de dados. Inclui análise de intenção, geração de SQL e validação de consultas.	Core Domain
| **Conectividade & Virtualização de Dados**        | Conecta DW/Datamarts/Data Lake, controla pushdown, caching de resultados e limites de custo.                              | **Supporting**  | Crítico ao funcionamento, mas não é diferencial competitivo.                       |
| **Governança de Acesso a Dados (LGPD/ABAC/RBAC)** | Políticas por papel/atributo, masking de coluna, filtros por linha, trilha de auditoria.                                  | **Supporting**  | Regras sensíveis ao negócio, porém não exclusivas.                                 |
| **Qualidade, Linhagem & Catálogo**                | Monitora freshness/completeza, rastreia linhagem, cataloga fontes certificadas.                                           | **Supporting**  | Essencial para confiança, mas não diferencia no mercado.                           |
| **Sessão, Contexto & Workspaces**                 | Histórico de conversas, rascunhos, coleções compartilhadas, versões de consultas.                                         | **Supporting**  | Agrega usabilidade e colaboração, sem ser o “coração”.                             |
| **Observabilidade da Plataforma**                 | Métricas de uso/perf/perfil de custos, logs de consultas, alertas operacionais.                                           | **Supporting**  | Necessário para operar/otimizar, não diferencia.                                   |
| **Identidade & SSO**                              | Autenticação, SSO corporativo, provisionamento SCIM.                                                                      | **Generic**     | Commodity de mercado; melhor integrar (Azure AD/Okta/ADFS).                        |
| **Mensageria & Notificações**                     | Entrega de alertas/relatórios (Email, Slack, Teams, Webhooks).                                                            | **Generic**     | Serviços padronizados com integrações maduras.                                     |
| **Agendamento de Tarefas**                        | Schedules de relatórios e rotinas (cron, retries, SLAs).                                                                  | **Generic**     | Plataforma pronta é suficiente (Airflow/Cloud Scheduler).                          |
| **Armazenamento de Arquivos**                     | Export/arquivo de relatórios, snapshots, anexos.                                                                          | **Generic**     | Infra comoditizada (S3/GCS/Azure Blob).                                            |
| **Cache/Edge & Mensageria Técnica**               | Cache de resultados/efeitos N+1, filas para jobs pesados.                                                                 | **Generic**     | Infra padrão (Redis/Kafka/RabbitMQ).                                               |
| **Runtime de LLM**                                | Provedores/serving de modelos (API externa ou on-prem).                                                                   | **Generic**     | O **uso** de LLM é commodity; o **como orquestrar** continua Core.                 |


---

## 4. Desenho dos Bounded Contexts
Liste e descreva os bounded contexts identificados no projeto. Explique a responsabilidade de cada um.

| **Bounded Context**           | **Responsabilidade**                                                                                 | **Subdomínios Relacionados** |
|-------------------------------|-----------------------------------------------------------------------------------------------------|-----------------------------|
| Ex.: Contexto de Consultas    | Gerencia as consultas médicas, do agendamento à finalização, incluindo emissão de receitas.         | Gestão de Consultas         |
| Ex.: Contexto de Pagamentos   | Processa cobranças de consultas e repasses para médicos ou clínicas.                              | Pagamentos                  |

---

## 5. Comunicação entre os Bounded Contexts
Explique como os bounded contexts vão se comunicar. Use os padrões de comunicação, como:
- **Mensageria/Eventos (desacoplado):** Ex.: O Contexto de Consultas emite um evento "Consulta Finalizada", consumido pelo Contexto de Pagamentos.
- **APIs (síncrono):** Ex.: O Contexto de Pagamentos consulta informações de preços no Contexto de Consultas.

| **De (Origem)**              | **Para (Destino)**          | **Forma de Comunicação**    | **Exemplo de Evento/Chamada**                  |
|------------------------------|-----------------------------|-----------------------------|-----------------------------------------------|
| Contexto de Consultas        | Contexto de Pagamentos      | Mensageria (Evento)         | "Consulta Finalizada"                         |
| Contexto de Cadastro          | Contexto de Consultas      | API                         | Obter informações de um Paciente pelo ID      |



---

## 6. Definição da Linguagem Ubíqua
Liste os termos principais da Linguagem Ubíqua do projeto. Explique brevemente cada termo.

| **Termo**                    | **Descrição**                                                                                   |
|------------------------------|-----------------------------------------------------------------------------------------------|
| Ex.: Consulta                | Sessão médica entre paciente e médico.                                                       |
| Ex.: Paciente                | Usuário que agenda e realiza consultas.                                                      |
| Ex.: Receita                 | Prescrição médica gerada durante a consulta.                                                 |

---

## 7. Estratégia de Desenvolvimento
Para cada tipo de subdomínio, explique a abordagem para implementação:
- **Core Domain:** Desenvolver internamente com foco total.
- **Supporting Subdomain:** Desenvolver internamente ou parcialmente terceirizar.
- **Generic Subdomain:** Usar ferramentas ou serviços de mercado.

| **Subdomínio**              | **Estratégia**                         | **Ferramentas ou Serviços (se aplicável)** |
|-----------------------------|---------------------------------------|-------------------------------------------|
| Gestão de Consultas         | Desenvolvimento interno               |                                           |
| Cadastro de Usuários        | Interno com uso de Auth0 para login   | Auth0                                     |
| Pagamentos                  | Terceirizar usando API Stripe         | Stripe                                    |

---

## 8. Diagrama Visual (Opcional, mas Recomendado)
Desenhe um diagrama que mostre:
- Os bounded contexts.
- Como eles se comunicam.
- A relação com os subdomínios.

Use ferramentas como **Miro**, **Lucidchart** ou mesmo papel e caneta para criar seu diagrama e adicionar ao projeto.

---

## Dicas para Apresentação
- Explique cada parte do design, focando no **Core Domain** (o coração do negócio).
- Justifique por que certos subdomínios foram classificados como Supporting ou Generic.
- Destaque como a comunicação entre bounded contexts foi pensada para ser escalável.

---

Boa sorte com a dinâmica! 🚀
