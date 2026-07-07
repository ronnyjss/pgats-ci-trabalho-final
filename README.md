[![Code coverage badge](https://img.shields.io/badge/coverage-100%25-brightgreen)](https://stryker-mutator.io/robo-coasters-example/reports/coverage/lcov-report/index.html)
[![Mutation testing badge](https://img.shields.io/endpoint?style=flat&url=https%3A%2F%2Fbadge-api.stryker-mutator.io%2Fgithub.com%2Fstryker-mutator%2Frobo-coasters-example%2Fmaster)](https://dashboard.stryker-mutator.io/reports/github.com/stryker-mutator/robo-coasters-example/master)

# PGATS - CI

## Introdução

Este projeto apresenta uma implementação de Integração Contínua (CI) com GitHub Actions, com o objetivo de automatizar a validação do software por meio de inspeção estática, testes unitários e testes end-to-end (E2E). A solução foi estruturada para contemplar diferentes mecanismos de disparo, permitindo tanto a execução manual quanto a automação em cenários recorrentes.

## Objetivo da solução

A proposta da pipeline é garantir maior confiabilidade no processo de desenvolvimento, reduzindo riscos associados à integração de alterações e proporcionando rastreabilidade dos resultados por meio de relatórios gerados durante a execução.

## Visão geral dos workflows

Os arquivos de automação estão localizados em [.github/workflows](.github/workflows), e cada um deles atende a um cenário específico de execução:

| Workflow                 | Arquivo                                                                                  | Gatilho                      | Função principal                              |
| ------------------------ | ---------------------------------------------------------------------------------------- | ---------------------------- | --------------------------------------------- |
| N1 - Execução Manual     | [.github/workflows/01-manual-exec.yaml](.github/workflows/01-manual-exec.yaml)           | `workflow_dispatch`          | Execução manual dos testes E2E                |
| N2 - Execução Agendada   | [.github/workflows/02-scheduled-exec.yaml](.github/workflows/02-scheduled-exec.yaml)     | `schedule`                   | Execução automática periódica                 |
| N3 - Execução por Deploy | [.github/workflows/03-post-deploy-exec.yaml](.github/workflows/03-post-deploy-exec.yaml) | `workflow_run`               | Execução após conclusão de outras pipelines   |
| N4 - Execução Integrada  | [.github/workflows/04-integrated-exec.yaml](.github/workflows/04-integrated-exec.yaml)   | `push` e `workflow_dispatch` | Validação completa do projeto antes do deploy |

## Descrição dos workflows

### 1. Workflow de execução manual

Arquivo: [.github/workflows/01-manual-exec.yaml](.github/workflows/01-manual-exec.yaml)

Este workflow permite a execução controlada dos testes E2E diretamente pela interface do GitHub. O processo inclui:

- checkout do repositório;
- configuração do ambiente com Node.js;
- instalação das dependências do projeto;
- download dos navegadores necessários para o Playwright;
- execução dos testes E2E;
- armazenamento do relatório em formato de artefato.

### 2. Workflow de execução agendada

Arquivo: [.github/workflows/02-scheduled-exec.yaml](.github/workflows/02-scheduled-exec.yaml)

O gatilho `schedule` permite a execução automática do workflow conforme uma agenda definida pelo cron. Nesse cenário, a pipeline é responsável por verificar continuamente a estabilidade da aplicação, mesmo na ausência de novas alterações no código.

### 3. Workflow disparado após outra execução

Arquivo: [.github/workflows/03-post-deploy-exec.yaml](.github/workflows/03-post-deploy-exec.yaml)

Este workflow utiliza o evento `workflow_run` para iniciar novas validações após a conclusão bem-sucedida de outras pipelines. Esse mecanismo é particularmente útil para organizar etapas sequenciais de validação e evitar que testes sejam executados antes da conclusão das etapas preliminares.

### 4. Workflow integrado com push

Arquivo: [.github/workflows/04-integrated-exec.yaml](.github/workflows/04-integrated-exec.yaml)

Esse workflow representa a abordagem mais completa de CI, pois executa, em sequência, as seguintes etapas:

- inspeção do código com `yarn lint`;
- execução dos testes unitários com `yarn run test`;
- execução dos testes E2E com `yarn run e2e`;
- publicação dos relatórios gerados;
- simulação do processo de deploy após a validação bem-sucedida.

## Conceitos fundamentais aplicados

- `workflow_dispatch`: ativação manual da pipeline;
- `schedule`: automação recorrente baseada em cron;
- `workflow_run`: disparo dependente da conclusão de outro workflow;
- `needs`: controle da ordem de execução entre jobs;
- `upload-artifact`: preservação dos relatórios para consulta posterior;
- `if: ${{ always() }}`: assegura que o artefato seja gerado mesmo quando há falhas.

## Relatórios de testes

Os resultados dos testes E2E são armazenados no artefato `playwright-report`, permitindo a análise detalhada dos cenários executados. Essa prática contribui para a auditoria do processo e para a identificação rápida de falhas.

## Requisitos para execução local

1. Instale o [git](https://git-scm.com)
2. Instale o [nodejs](https://nodejs.org/)
3. Instale o Yarn com o comando:
   ```shell
   npm install -g yarn
   ```
4. Faça um fork do projeto e clone o repositório.
5. Instale as dependências:
   ```shell
   cd pgats-ci
   yarn
   ```
6. Execute os testes unitários:
   ```shell
   yarn run test
   ```
7. Gere o relatório de cobertura em `reports/coverage/lcov-report`.
8. Execute os testes de mutação com Stryker:
   ```shell
   yarn run test:mutation
   ```
9. Instale os navegadores necessários para o Playwright:
   ```shell
   yarn playwright install
   ```
10. Execute os testes end-to-end:
    ```shell
    yarn run e2e
    ```
11. Inicie a aplicação localmente:
    ```shell
    yarn start
    ```
12. Acesse a aplicação publicada em [https://pgats-ci-example.netlify.app](https://pgats-ci-example.netlify.app)

## Considerações finais

A arquitetura proposta demonstra como a automação de testes pode ser integrada ao ciclo de desenvolvimento de software, promovendo maior segurança, previsibilidade e qualidade nas entregas.
