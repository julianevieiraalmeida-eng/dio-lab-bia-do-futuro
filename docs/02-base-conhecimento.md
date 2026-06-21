# Base de Conhecimento do Dante

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
<SYSTEM_CONTEXT>
[METADADOS_SESSAO]
- Data da Análise: 2026-06-21
- Perfil de Risco: Conservador (Bolsista Integral)
- Flag de Atenção: Risco de Liquidez Imediata Detectado

[DADOS_DO_CLIENTE_JSON]
{
  "nome": "Ana Costa",
  "objetivo_critico": "Inscrição Congresso (Meta: R$ 80,00)",
  "reserva_disponivel": 180.00
}

[AGREGAÇÃO_DE_GASTOS_MES_ATUAL]
- Moradia/República: R$ 450,00 (62% da renda)
- Alimentação (RU/Cantina): R$ 120,00 (16% da renda)
- Transporte: R$ 60,00 (8% da renda)
- Educação/Materiais: R$ 45,00 (6% da renda)
- Lazer/Supérfluos: R$ 50,00 (8% da renda)

[ANALISE_COMPORTAMENTAL_PREVIA]
- Tendência: Aumento de 15% em "Alimentação" nos últimos 7 dias.
- Alerta: Se o padrão de Lazer e Alimentação se mantiver, o capital para o "Objetivo Crítico" será consumido em 9 dias.

[CATALOGO_PRODUTOS_FILTRADO]
- "Caixinha Reserva Acadêmica": Risco zero, liquidez imediata. (Recomendação Principal).
</SYSTEM_CONTEXT>

<INTERACAO_USUARIO_AGENTE>
Usuário: "Dante, acha que eu consigo pagar o congresso semana que vem se eu continuar gastando assim na lanchonete do CA?"

Dante: "Salve, Ana! Analisando seu Mapa do Inferno aqui... a matemática é cruel: você tem R$ 180 e a inscrição é R$ 80. Sobram só R$ 100 pra sobreviver ao mês. Só que o sistema apitou que, nesse seu ritmo de salgado no CA e streaming, seu caixa zera em exatos 9 dias! Se você não focar no bandejão essa semana, o congresso vai virar lenda. Minha recomendação técnica? Pega esses R$ 80 hoje e joga na 'Caixinha Reserva Acadêmica' pra travar esse dinheiro com risco zero. Bora garantir essa meta!"

