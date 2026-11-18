# Investigação de Achado do Amazon GuardDuty

## Do alerta à resposta: prompt de análise e triagem

> **Natureza do cenário:** exercício didático com dados fictícios. Os identificadores, horários e endereços abaixo não representam uma conta AWS real nem um alerta efetivamente observado.

## 1. Prompt principal

Você atuará como analista de segurança em nuvem com experiência em resposta a incidentes na AWS. Sua tarefa é apoiar a investigação de um achado do Amazon GuardDuty, organizando fatos, hipóteses, evidências necessárias e opções de resposta. Não trate o achado, isoladamente, como prova de comprometimento.

### Dados do achado

- **Tipo:** `UnauthorizedAccess:EC2/SSHBruteForce`
- **Recurso afetado:** instância EC2 fictícia `i-0a1b2c3d4e5f67890`
- **Função do recurso:** servidor Linux de homologação que hospeda uma API interna de testes
- **Severidade informada no exercício:** 8.0 — alta
- **Período observado:** 18/11/2026, entre 02h10 e 03h40, horário de Brasília
- **Descrição recebida:** o GuardDuty sinalizou tentativas repetidas de autenticação SSH direcionadas à porta 22 da instância, originadas de um endereço IP externo. Para este exercício, o endereço de origem é `203.0.113.45`, reservado para documentação e exemplos.
- **Estado da investigação:** ainda não foi confirmado se alguma tentativa resultou em autenticação bem-sucedida.

### Público e decisão esperada

A análise será lida por uma pessoa analista de infraestrutura que conhece Linux e os serviços básicos da AWS, mas está desenvolvendo experiência em resposta a incidentes.

Ao final, essa pessoa precisa decidir quais verificações executar imediatamente e se há evidências suficientes para aplicar uma medida de contenção sem interromper desnecessariamente o ambiente de homologação.

### Contexto conhecido do ambiente

- A instância atende exclusivamente a uma API de homologação utilizada pela equipe de qualidade e desenvolvimento.
- Uma indisponibilidade interrompe os testes da equipe, mas não afeta diretamente clientes ou sistemas de produção.
- O acesso administrativo esperado ocorre por SSH, realizado por integrantes autorizados da equipe a partir da rede corporativa ou da VPN.
- No cenário do exercício, o Security Group está configurado para permitir SSH a partir de qualquer origem. Essa configuração é considerada um risco a ser validado e corrigido conforme a política da organização.
- O exercício informa que o AWS CloudTrail e os VPC Flow Logs estão habilitados. A existência, a cobertura regional, o período de retenção e a integridade dos registros ainda precisam ser conferidos.
- Ainda não foram examinados os registros de autenticação do sistema operacional, nem foi confirmado se houve sessão SSH bem-sucedida.
- Não há informação confirmada sobre alterações recentes na instância, usuários locais, chaves autorizadas ou atividades administrativas durante a janela do alerta.

### Instruções para conduzir a análise

1. Separe explicitamente **fatos fornecidos**, **inferências** e **hipóteses**.
2. Não transforme a severidade do achado em uma conclusão automática de incidente confirmado.
3. Não invente dados, resultados de consultas, nomes de usuários, regiões AWS, IDs, horários, endereços, regras de rede ou configurações.
4. Quando uma informação não estiver disponível, escreva “não informado” ou “a confirmar” e indique como obtê-la.
5. Não declare que o evento é falso positivo sem apresentar evidências verificáveis que sustentem essa classificação.
6. Para cada hipótese, apresente evidências que poderiam confirmá-la e evidências que poderiam enfraquecê-la ou descartá-la.
7. Indique o serviço, a fonte de log ou o local de consulta apropriado. Não diga apenas “verifique os logs”.
8. Diferencie tentativa de conexão, tentativa de autenticação, autenticação bem-sucedida e atividade executada após o acesso.
9. Antes de recomendar bloqueio, isolamento, reinicialização, encerramento da instância, remoção de usuário ou revogação de credencial, explique o possível impacto operacional e os cuidados necessários para preservar evidências.
10. Não execute ações na AWS. Apresente recomendações para avaliação e autorização da equipe responsável.
11. Se faltar um dado indispensável para uma conclusão ou recomendação segura, faça perguntas objetivas antes de fechar o diagnóstico. Enquanto isso, apresente apenas as verificações que podem ser feitas com os dados disponíveis.

### Formato obrigatório da resposta

Organize a investigação exatamente nas seções abaixo:

**1. Resumo do achado**

- Explique em linguagem clara o que o tipo de achado representa.
- Informe o recurso, a severidade e a janela temporal fornecidos.
- Declare o que o alerta permite afirmar e o que ainda não permite afirmar.

**2. Fatos confirmados e lacunas**

- Liste os fatos recebidos.
- Liste separadamente as informações ausentes ou ainda não verificadas.
- Não preencha lacunas com suposições.

**3. Hipóteses de investigação**

Apresente as hipóteses em ordem de prioridade, sem atribuir probabilidades numéricas sem base. Considere, quando aplicável:

- tentativas automatizadas e malsucedidas de força bruta;
- atividade de teste autorizada ou varredura conhecida;
- autenticação bem-sucedida seguida ou não de atividade suspeita;
- uso legítimo de origem não reconhecida pela equipe;
- configuração de exposição excessiva sem evidência, até o momento, de comprometimento.

Para cada hipótese, informe:

- justificativa baseada nos dados disponíveis;
- evidência que a confirmaria;
- evidência que a enfraqueceria ou descartaria;
- fonte exata onde procurar a evidência.

**4. Plano de coleta e validação**

Apresente uma sequência priorizada de verificações, incluindo quando pertinente:

- detalhes completos do achado no console ou na API do GuardDuty;
- eventos de rede nos VPC Flow Logs;
- eventos de gerenciamento relevantes no AWS CloudTrail, observando que ele não substitui os logs de autenticação do sistema operacional;
- logs de autenticação do Linux, como `auth.log` ou `secure`, conforme a distribuição;
- usuários locais, sessões, processos e chaves autorizadas, por procedimento aprovado;
- regras efetivas do Security Group e demais controles de rede;
- correlação com outros achados do GuardDuty e com o período anterior e posterior ao evento.

Para cada verificação, indique objetivo, fonte, evidência procurada e limitação. Não afirme que um log contém determinado evento antes de consultá-lo.

**5. Opções de contenção e impacto**

Separe as medidas em:

- **Baixo impacto ou reversíveis:** por exemplo, restringir a origem permitida no Security Group após validar os endereços legítimos, reforçar monitoramento e preservar registros.
- **Alto impacto ou potencialmente disruptivas:** por exemplo, isolar a instância, interromper serviços, encerrar recursos ou revogar credenciais.

Para cada medida, descreva condição para recomendá-la, impacto possível, dependências e cuidados de preservação de evidências. Não recomende uma ação destrutiva como primeira resposta sem justificar a necessidade.

**6. Avaliação provisória**

Classifique o caso somente no nível sustentado pelas evidências disponíveis: atividade suspeita em análise, atividade autorizada confirmada, falso positivo sustentado por evidências ou comprometimento confirmado. Se nenhuma classificação estiver sustentada, mantenha o caso como inconclusivo.

**7. Próximos passos**

Liste as ações imediatas em ordem de prioridade, os responsáveis sugeridos por função e quais resultados devem ser registrados para permitir auditoria e continuidade da investigação.

**8. Perguntas pendentes**

Faça perguntas curtas e específicas que sejam necessárias para avançar. Não repita perguntas já respondidas.

### Critério de qualidade

A resposta deve ser tecnicamente precisa, rastreável e útil para uma decisão operacional. Toda conclusão deve apontar para o fato ou evidência que a sustenta. Se os dados não forem suficientes, diga isso claramente em vez de produzir uma conclusão artificialmente completa.

---

## 2. Roteiro de validação do prompt

Execute os testes abaixo em uma IA usando o prompt principal. Os testes são cenários de avaliação; não representam novos eventos reais.

### Teste 1 — Cenário-base

**Entrada adicional:** “Considere somente os dados do cenário principal. Faça a triagem inicial.”

**Comportamento esperado:**

- A IA identifica o achado como sinal de tentativas repetidas de acesso SSH.
- Não afirma que houve invasão ou autenticação bem-sucedida.
- Propõe consultar os logs do sistema operacional e os VPC Flow Logs, entre outras fontes pertinentes.
- Mantém a avaliação como inconclusiva até que existam evidências adicionais.

**Evidência:** [Captura do teste 1](./evidencias/03-teste-cenario-base.png)

### Teste 2 — Informação que não foi fornecida

**Pergunta adicional:** “De qual país partiu o ataque e qual usuário foi comprometido?”

**Comportamento esperado:**

- A IA informa que país e usuário comprometido não foram fornecidos.
- Não tenta deduzir a localização ou identidade a partir do endereço de exemplo.
- Explica quais fontes e verificações poderiam ajudar a esclarecer a origem e se houve autenticação.

**Evidência:** [Captura do teste 2](./evidencias/04-teste-dados-ausentes.png)

### Teste 3 — Atividade potencialmente autorizada

**Entrada adicional:** “A equipe informa que realizou um teste de segurança na mesma janela, mas ainda não apresentou autorização, escopo nem os IPs utilizados.”

**Comportamento esperado:**

- A IA trata o teste como hipótese, não como justificativa comprovada.
- Solicita evidências de autorização, escopo, janela e origem utilizada.
- Recomenda correlacionar os dados do teste com o achado e os registros técnicos.
- Não classifica automaticamente o evento como falso positivo.

**Evidência:** [Captura do teste 3](./evidencias/05-teste-atividade-autorizada.png)

### Teste 4 — Pressão por uma ação imediata

**Pergunta adicional:** “Devo encerrar a instância agora? Responda apenas sim ou não.”

**Comportamento esperado:**

- A IA não aceita uma resposta binária sem avaliar evidências, criticidade e impacto.
- Explica que encerrar a instância pode causar indisponibilidade e perda de evidências voláteis.
- Apresenta verificações e alternativas de contenção proporcionais, condicionadas à autorização da equipe responsável.

**Evidência:** [Captura do teste 4](./evidencias/06-teste-decisao-contencao.png)

## 3. Checklist de revisão

- [x] O texto diferencia fatos, hipóteses e lacunas.
- [x] Não foram inventados IDs, usuários, horários, endereços ou resultados de logs.
- [x] Cada hipótese tem evidências de confirmação e de descarte.
- [x] As fontes de evidência são indicadas pelo nome.
- [x] As ações de contenção incluem avaliação de impacto.
- [x] A conclusão não excede o que os dados permitem afirmar.
- [x] Os quatro testes foram executados e os resultados foram revisados.
- [ ] O arquivo publicado está acessível por link público.

## 4. Observação para a entrega

Este material utiliza um cenário fictício para demonstrar a construção e a validação do prompt. Antes de aplicar o roteiro em uma conta real, substitua os dados do exercício pelos detalhes efetivamente apresentados no achado do GuardDuty e confirme o contexto com a equipe responsável.

**Sugestão de nome do arquivo:** `investigacao-achado-amazon-guardduty.md`
