# Base de Conhecimento

## Dados Utilizados

São utilizados os arquivos da pasta `data` fornecidos pelo desafio,com foco a interpretação do contexto de **micro-finanças universitárias** (o que o Dante chama de "Mapa do Inferno"):

| Arquivo | Formato | Utilização no Agente Dante |
|---------|---------|---------------------|
| `historico_atendimento.csv` | CSV | **Contexto de Sobrevivência:** Lembrar dificuldades passadas do aluno e dúvidas anteriores (ex: "Mês passado você zerou a conta pagando aquele congresso"). |
| `perfil_investidor.json` | JSON | **Perfil Acadêmico:** Adaptado para entender se o aluno é conservador (bolsista restrito) ou se tem flexibilidade (faz freelas/estágio). |
| `produtos_financeiros.json` | JSON | **Catálogo de Metas:** Utilizado para sugerir produtos simples e seguros (ex: "Reserva de Emergência", "Tesouro Direto para Formatura"). |
| `transacoes.csv` | CSV | **Auditoria de Rotina:** Analisar o padrão de gastos diários (passagem, lanches, xerox) para sugerir cortes táticos e bater metas. |

> [!TIP]
> Optei por manter os dados mockados locais para garantir a privacidade do estudante e simular um ambiente de processamento rápido e de baixo custo, ideal para o cenário universitário.

---

## Adaptações nos Dados

Manteve-se os dados originais em sua estrutura tabular e JSON, foi adpatado a **interpretação semântica**  via engenharia de prompt para a realidade do estudante:
*   Configura-se o Dante para interpretar as categorias de gastos genéricas no `transacoes.csv` (como "Alimentação" ou "Educação") como "Bandejão/RU", "Lanches no Centro Acadêmico" e "Xerox/Materiais".
*   Mapee-se o arquivo `perfil_investidor.json` para que o agente o entenda como **Nível de Risco Acadêmico**. Quando o perfil é "Conservador", ativa-se um filtro rigoroso de segurança, orientando a IA a focar apenas em sobrevivência mensal e corte de gastos supérfluos.

---

## Estratégia de Integração

### Como os dados são carregados?
O sistema é configurado para carregar os arquivos CSV e JSON na memória logo no início da sessão, utilizando as bibliotecas `pandas` e `json` do Python. Em uma arquitetura real, ele desenvolveu-se para funcionar como um banco de dados local extraído diretamente do extrato bancário do estudante.

### Como os dados são usados no prompt?
Não foi deixado os dados fixos ou soltos no System Prompt. Utiliza-se o *System Prompt* estritamente para guardar a Persona (Dante) e as regras éticas do agente. 
Programou-se o sistema para que os dados sejam **consultados dinamicamente e injetados no contexto do usuário**. Quando o aluno faz uma pergunta, o  código em Python filtra as informações relevantes dos CSVs (como calcular o saldo atual e buscar as últimas 5 transações) e concatena essas informações de forma invisível junto à mensagem do usuário, enviando tudo para a LLM.

---

## Exemplo de Contexto Montado

```text
[Contexto Injetado pelo Sistema Oculto]
Dados do Estudante:
- Nome: Ana Costa
- Perfil Acadêmico: Conservador (Bolsista integral)
- Saldo disponível atual: R$ 180,00
- Meta prioritária identificada: Inscrição no Congresso (R$ 80,00)

Últimas transações mapeadas:
- 15/06: Restaurante Universitário (RU) - R$ 2,50
- 16/06: Lanchonete (Café e Salgado) - R$ 18,00
- 18/06: Papelaria (Xerox e Apostilas) - R$ 12,00
- 20/06: Assinatura Streaming - R$ 29,90

Mensagem do usuário: "Dante, acha que eu consigo pagar o congresso semana que vem se eu continuar gastando assim?"
