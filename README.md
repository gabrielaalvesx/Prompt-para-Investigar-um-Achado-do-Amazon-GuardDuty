# Investigação de Achado do Amazon GuardDuty

Projeto desenvolvido como parte do desafio **Do Alerta à Resposta: Prompt para Investigar um Achado do Amazon GuardDuty**, com foco em triagem, análise de evidências e tomada de decisão em cibersegurança.

## Objetivo

Desenvolver um prompt estruturado para apoiar a investigação de um alerta de possível força bruta SSH identificado pelo Amazon GuardDuty, organizando fatos, hipóteses, evidências necessárias e ações de resposta.

## Cenário analisado

O exercício utiliza um cenário simulado com o seguinte achado:

- **Finding:** `UnauthorizedAccess:EC2/SSHBruteForce`
- **Recurso:** instância EC2 Linux `i-0a1b2c3d4e5f67890`
- **Função:** servidor Linux de homologação que hospeda uma API interna
- **Severidade:** 8.0 — alta
- **Janela de atividade:** 18/11/2026, das 02h10 às 03h40 (BRT)
- **Endereço IP de origem:** `203.0.113.45`, reservado para documentação e exemplos

O cenário também considera que a equipe de desenvolvimento teria realizado um teste de segurança no mesmo período, mas não apresentou inicialmente autorização formal, escopo ou endereços IP utilizados.

## Metodologia

A investigação foi organizada em etapas:

1. Identificação e classificação do achado.
2. Separação entre fatos confirmados, hipóteses e informações ausentes.
3. Identificação das evidências necessárias.
4. Avaliação da possibilidade de atividade legítima.
5. Análise das alternativas de contenção e preservação de evidências.
6. Elaboração de uma conclusão técnica fundamentada.

## Conteúdo do projeto

- [Prompt e roteiro de validação](./investigacao-achado-amazon-guardduty.md)
- [Evidências dos testes](./evidencias/)

## Testes de validação do prompt

Foram executados quatro testes para verificar se o prompt permanece preciso e não inventa informações:

| Teste | Objetivo | Evidência |
| --- | --- | --- |
| 1. Cenário-base | Avaliar a triagem inicial do alerta | [Captura](./evidencias/03-teste-cenario-base.png) |
| 2. Dados ausentes | Verificar se país e usuário são informados como desconhecidos | [Captura](./evidencias/04-teste-dados-ausentes.png) |
| 3. Atividade autorizada | Verificar o tratamento de um possível teste sem comprovação formal | [Captura](./evidencias/05-teste-atividade-autorizada.png) |
| 4. Contenção | Avaliar os riscos de encerrar imediatamente a instância | [Captura](./evidencias/06-teste-decisao-contencao.png) |

Os testes verificam que o prompt separa fatos de hipóteses, exige fontes verificáveis e mantém o caso inconclusivo quando os dados não sustentam uma conclusão.

## Estrutura do projeto

```text
Prompt para Investigar um Achado do Amazon GuardDuty/
├── README.md
├── investigacao-achado-amazon-guardduty.md
└── evidencias/
    ├── 03-teste-cenario-base.png
    ├── 04-teste-dados-ausentes.png
    ├── 05-teste-atividade-autorizada.png
    └── 06-teste-decisao-contencao.png
```

## Tecnologias e conceitos

- Amazon GuardDuty
- Amazon EC2
- AWS CloudTrail
- Amazon VPC Flow Logs
- Linux e SSH
- Engenharia de prompt
- Triagem de alertas
- Análise de evidências
- Resposta a incidentes

## Observação sobre o cenário

Este projeto utiliza dados fictícios para fins educacionais. Os identificadores, horários e o endereço `203.0.113.45` não representam uma conta AWS ou um incidente real.

## Aprendizados

O desenvolvimento deste projeto permitiu praticar a estruturação de investigações de segurança, a distinção entre fatos e hipóteses e a elaboração de recomendações de resposta baseadas em evidências.

---

**Projeto educacional — Cibersegurança e Cloud Security.**
