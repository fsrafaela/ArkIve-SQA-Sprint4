# Limitações e cobertura

## Tipos de teste existentes

O repositório contém quatro grupos distintos:

| Tipo | Classes | Característica |
|---|---:|---|
| Unitário | 57 | Unidade instanciada diretamente, com dependências simuladas ou lógica pura |
| MVC | 20 | `MockMvc`, `@WebMvcTest` ou contexto web completo |
| Integração | 17 | JPA/H2, contexto Spring, JDBC/migration ou composição de múltiplos serviços reais |
| Estrutural | 1 | Inspeção do texto de consultas JPQL por reflexão |

Essas categorias não devem ser somadas como se todas representassem testes unitários.

## Papel do Mockito

Mockito substitui repositories, gateways, serviços colaboradores, relógios, processos e clientes externos por objetos controlados. Isso permite validar regras como autorização, transições de estado, validações e chamadas esperadas sem depender de banco ou rede.

O comportamento aprovado com um mock demonstra a interação da unidade com o contrato simulado. Não demonstra que a infraestrutura real responde da mesma forma.

## Banco de dados

Os 523 casos desta entrega não iniciaram Oracle nem executaram migrations. Portanto, eles não comprovam:

- conexão e autenticação no Oracle;
- compatibilidade de SQL, CLOB, constraints e índices;
- comportamento transacional real;
- aplicação integral das migrations Flyway;
- desempenho de consultas sobre dados reais.

Esses aspectos exigem os testes de integração classificados separadamente e validação específica em ambiente Oracle.

## MVC e segurança HTTP

Os unitários não substituem os 20 testes MVC. Rotas, status HTTP, JSON, Bean Validation no controller, CSRF, filtros Spring Security, views Thymeleaf e redirecionamentos devem ser evidenciados pelas classes MVC.

## Serviços externos e IA

A execução desta entrega não chamou Groq, Render, Azure Speech ou Expo reais. Os testes de provider e push usam `MockRestServiceServer`; Azure Speech e conversão de áudio usam factories, processos e arquivos temporários controlados.

Assim, os testes validam adaptação de payloads, tratamento de respostas e falhas, mas não disponibilidade, latência, autenticação ou qualidade de resposta dos serviços reais.

## Cobertura

O `pom.xml` não configura JaCoCo e não foi localizado relatório de cobertura. Logo:

- não existe percentual verificável de cobertura de linhas, branches ou métodos;
- 523 execuções não significam 523 métodos Java distintos;
- quantidade de testes não deve ser convertida em percentual de cobertura;
- não se deve declarar cobertura total do backend.

Uma medição futura de cobertura exigiria configuração e execução específicas, fora do escopo deste pacote.

## Testes manuais e ponta a ponta

Testes manuais e E2E continuam necessários para validar a jornada completa, interface, integrações reais, dados representativos, usabilidade e comportamento do ambiente implantado. Eles complementam, mas não substituem, os testes unitários; o inverso também é verdadeiro.

## Limitação temporal da evidência

Os relatórios representam a execução de 20/10/2026 entre 18:58:12 e 18:58:18, no estado das fontes verificado durante a preparação. Alterações posteriores no projeto exigem nova execução e novo pacote de evidências.
