# ArkIve — Evidências de Testes Unitários para SQA

**Curso:** Análise e Desenvolvimento de Sistemas — FIAP  
**Disciplina de destino:** Software Quality Assurance (SQA)  
**Projeto:** ArkIve — Java Advanced

## Objetivo

Este pacote reúne a documentação, o código de consulta e as evidências de execução dos testes classificados como unitários no backend ArkIve. Ele foi preparado para permitir a análise acadêmica sem exigir que a responsável por SQA investigue toda a aplicação Java.

Foram confirmadas **57 classes unitárias** e **523 execuções** pelo Maven Surefire, com:

- 523 aprovadas;
- 0 falhas;
- 0 erros;
- 0 ignoradas;
- build concluído com sucesso.

Os totais acima não incluem as 20 classes MVC, 17 classes de integração nem a classe de contrato estrutural existentes no repositório.

## Tecnologias verificadas

- Java 17.0.18 na execução Maven;
- Spring Boot 3.5.14;
- JUnit Jupiter 5.12.2;
- Mockito 5.17.0;
- Maven 3.9.15;
- Maven Surefire 3.5.5;
- Spring Test e `MockRestServiceServer` para fronteiras HTTP simuladas.

Não existe configuração JaCoCo nem relatório de cobertura percentual no projeto.

## Estrutura

```text
entrega-sqa-testes-unitarios/
├── README.md
├── documentacao/
│   ├── 01-relatorio-testes-unitarios.md
│   ├── 02-casos-de-teste.md
│   ├── 03-guia-de-execucao.md
│   ├── 04-limitacoes-e-cobertura.md
│   └── inventario-casos-teste.csv
├── codigo-fonte/
│   └── src/test/java/...
├── evidencias/
│   ├── relatorios-surefire/
│   ├── resultado-execucao.txt
│   └── resumo-resultados.md
└── configuracao/
    └── pom.xml
```

## Onde consultar

- Visão técnica e inventário das 57 classes: [`documentacao/01-relatorio-testes-unitarios.md`](documentacao/01-relatorio-testes-unitarios.md).
- Matriz de casos e parametrizações: [`documentacao/02-casos-de-teste.md`](documentacao/02-casos-de-teste.md).
- Inventário completo das 523 execuções: [`documentacao/inventario-casos-teste.csv`](documentacao/inventario-casos-teste.csv).
- Reprodução em Windows/PowerShell: [`documentacao/03-guia-de-execucao.md`](documentacao/03-guia-de-execucao.md).
- Escopo, limitações e cobertura: [`documentacao/04-limitacoes-e-cobertura.md`](documentacao/04-limitacoes-e-cobertura.md).
- Resultado consolidado: [`evidencias/resumo-resultados.md`](evidencias/resumo-resultados.md).
- Relatórios por classe: `evidencias/relatorios-surefire/`.
- Cópias dos testes: `codigo-fonte/src/test/java/`.

## Observação sobre as cópias dos testes

As classes em `codigo-fonte` são material de consulta e preservam os pacotes originais. Elas dependem das classes de produção e das dependências Maven do projeto completo. Para reproduzir a execução, use o repositório original e siga o guia; não tente compilar apenas esta pasta de entrega.

Os XMLs Surefire do pacote são cópias sanitizadas. Foi removido somente o bloco de propriedades do ambiente, que continha caminhos absolutos locais. As suítes, casos, tempos e resultados foram preservados.
