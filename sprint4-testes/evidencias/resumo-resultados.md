# Resumo dos resultados

## Resultado confirmado

| Métrica | Valor |
|---|---:|
| Classes unitárias executadas | 57 |
| Execuções Surefire | 523 |
| Métodos-base distintos | 481 |
| Métodos parametrizados | 15 |
| Execuções parametrizadas | 57 |
| Aprovadas | 523 |
| Falhas | 0 |
| Erros | 0 |
| Ignoradas | 0 |
| Build | `BUILD SUCCESS` |

Execução concluída em 20/10/2026 às 18:58:18 (`America/Sao_Paulo`) com Maven 3.9.15, Java 17.0.18 e Surefire 3.5.5.

## Distribuição das execuções

| Domínio | Execuções |
|---|---:|
| Administração e demais funcionalidades | 21 |
| Autenticação e segurança | 91 |
| Compartilhamento e permissões | 49 |
| Consultas e fluxo clínico | 120 |
| Custos assistenciais | 17 |
| Diagnósticos e suporte da IA | 60 |
| Notificações | 33 |
| Pacientes e responsáveis | 90 |
| Transcrição de áudio | 42 |
| **Total** | **523** |

## Evidência verificável

- 57 XMLs sanitizados em `relatorios-surefire/`;
- 57 resumos TXT em `relatorios-surefire/`;
- uma linha por execução em `../../documentacao/inventario-casos-teste.csv`;
- nenhuma evidência MVC ou de integração foi misturada neste diretório.

Os XMLs tiveram apenas as propriedades do ambiente removidas. Elementos `testsuite` e `testcase`, tempos e resultados permanecem iguais aos originais.
