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

| **Subdomínio**                           | **Descrição**                                                                 | **Tipo**       | **Por quê está nessa categoria** |
|------------------------------------------|-------------------------------------------------------------------------------|----------------|----------------------------------|
| **Orquestração de Consulta Conversacional** | Interpreta a pergunta em português, identifica intenção, seleciona fonte, gera SQL/DSL e aplica validações antes da execução. | **Core Domain** | Diferencial competitivo direto: transformar linguagem natural em consultas seguras. |
| **Semântica de Negócio & Ontologia**     | Vocabulário do negócio (métricas, dimensões, entidades) mapeado para tabelas e regras oficiais de cálculo. | **Core Domain** | Sustenta consistência e precisão das respostas; representa o “cérebro” do domínio. |
| **Geração de Insights & Narrativas**     | Pós-processa os resultados e gera insights, resumos executivos e recomendações acionáveis. | **Core Domain** | Traduz dados em decisões de negócio, entregando valor ao usuário. |
| **Conectividade & Virtualização de Dados** | Conecta DW/Datamarts/Data Lake, controla pushdown, caching de resultados e limites de custo. | **Supporting**  | Crítico para operação, mas não é diferencial de mercado. |
| **Governança de Acesso a Dados (LGPD/ABAC/RBAC)** | Políticas de acesso por papel/atributo, masking de dados sensíveis, filtros por linha e auditoria. | **Supporting**  | Essencial para compliance, porém não é exclusivo. |
| **Qualidade, Linhagem & Catálogo**       | Monitora freshness, completude, rastreia linhagem e certifica fontes.          | **Supporting**  | Garante confiança, mas não diferencia competitivamente. |
| **Sessão, Contexto & Workspaces**        | Histórico de conversas, rascunhos, coleções compartilhadas e versões de consultas. | **Supporting**  | Melhora a experiência e colaboração, mas não é o coração do negócio. |
| **Observabilidade da Plataforma**        | Métricas de uso, custo, performance e logs detalhados de consultas.            | **Supporting**  | Necessário para manter e otimizar a operação. |
| **Armazenamento de Arquivos**            | Export e arquivamento de relatórios, snapshots e anexos.                       | **Generic**     | Serviço comoditizado (S3, GCS, Azure Blob). |
| **Cache/Filas Técnicas**                 | Cache de consultas, filas para jobs pesados e processamento assíncrono.        | **Generic**     | Infra padrão (Redis, Kafka, RabbitMQ). |
| **Runtime de LLM**                       | Hospedagem/serving de modelos (API externa ou on-prem).                        | **Generic**     | O modelo é commodity, o diferencial está na orquestração (Core). |


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

| **Termo**               | **Descrição** |
|--------------------------|---------------|
| **Consulta Natural**     | Pergunta em linguagem natural feita pelo usuário. |
| **Plano de Execução**    | Estratégia gerada (fontes, filtros, joins, agregações) antes de rodar a consulta. |
| **Métrica Oficial**      | Cálculo padronizado e governado (ex.: Receita Líquida). |
| **Dimensão**             | Atributo de corte (ex.: Período, Produto, Segmento). |
| **Fonte Certificada**    | Tabela/conjunto com selo de governança e SLA de qualidade. |
| **Máscara de Dados**     | Regra de ocultação/parcialização (PII/PCI/LGPD). |
| **Workspace**            | Espaço colaborativo de consultas, painéis e coleções. |
---

## 7. Estratégia de Desenvolvimento

**Critério geral:**  
- **Core Domains** → Sempre **desenvolvimento interno** (diferencial competitivo).  
- **Supporting** → Interno, mas aproveitando frameworks existentes.  
- **Generic** → **Integração/compra** de soluções prontas de mercado.  

| **Subdomínio** | **Estratégia** | **Ferramentas ou Serviços (se aplicável)** |
|----------------|----------------|--------------------------------------------|
| **Orquestração de Consulta Conversacional (Core)** | **Build interno completo**, garantindo controle sobre interpretação de intenção, geração e validação de consultas. | LLMs via SDK + validação própria de SQL |
| **Semântica de Negócio & Ontologia (Core)** | **Build interno** com governança forte de métricas e vocabulário contábil. | dbt Semantic Layer / Catálogo interno |
| **Geração de Insights & Narrativas (Core)** | **Build interno** com foco na experiência do usuário e valor de negócio. | Python/Pandas + libs de NLG |
| **Conectividade & Virtualização de Dados (Supporting)** | **Interno**, aproveitando conectores já maduros para bancos e lakes. | SQLAlchemy, Trino/Presto |
| **Infra Genérica (Armazenamento, Cache, Runtime de LLM)** | **Buy/Integrar**, pois são commodities de mercado. | S3/GCS, Redis/Kafka, OpenAI/Vertex |

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
