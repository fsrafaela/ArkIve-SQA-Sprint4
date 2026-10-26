\---



\# Sprint 4 — Compliance, Quality Assurance \& Tests



\*\*Branch da entrega:\*\* `develop`



\## Parte A — Testes manuais (Azure Boards)



\- \*\*Acesso ao projeto no Azure DevOps:\*\* https://dev.azure.com/RM561671/ArkIve

\- \*\*Plano de testes:\*\* https://dev.azure.com/RM561671/ArkIve/\_testPlans/define?planId=174

\- Cada PBI tem pelo menos 2 casos de teste (`PBI<ID>-CT01` e `CT02`), com objetivo (Summary), passos, dados de entrada e resultado esperado, usando parâmetros `@nome` e a tabela de Parameter values.

\- O professor está cadastrado com perfil \*\*Basic\*\* na organização e com acesso de \*\*Administrador\*\* no projeto.



\## Parte B — Automação de testes (Java: JUnit 5 + Mockito + Maven Surefire)



\- \*\*Vídeo (configuração, execução e resultados):\*\* https://youtu.be/4N-M0B7BFC4

\- Os testes são do backend do ArkIve (repositório `java-advanced`: https://github.com/2TDSPO-ArkIve/java-advanced), executados com Java 17 e Maven.



Comando executado no vídeo (5 casos de teste automatizados):



```powershell

.\\mvnw.cmd test "-Dtest=ClinicaServiceTest#criarClinicaProvisionaUsuarioAdminClinicaComVinculo,ConsultaWorkflowServiceTest#inicioConsomeObservacaoPendenteUmaUnicaVezSobLockDoAnimal,DiagnosticoAuthorizationServiceTest#criaSuporteClinicoIaComoProvisorioNaoConfirmado,ArkiveUserDetailsServiceTest#crmvResolveMesmoUsuarioVeterinarioQuandoLoginNaoExiste,FinancialVisibilityServiceTest#veterinarioVeSomenteCustosDeConsultaPropria"

```



Resultado (execução de 23/10/2026): `Tests run: 5, Failures: 0, Errors: 0, Skipped: 0` — `BUILD SUCCESS`.



| ID | Classe#método | Valida | PBI do plano |

|---|---|---|---|

| CT-UNIT-200 | `ClinicaServiceTest#criarClinicaProvisionaUsuarioAdminClinicaComVinculo` | Criação de clínica provisiona o usuário administrador com vínculo | 83 |

| CT-UNIT-274 | `ConsultaWorkflowServiceTest#inicioConsomeObservacaoPendenteUmaUnicaVezSobLockDoAnimal` | Início da consulta consome a observação pendente uma única vez | 58 |

| CT-UNIT-297 | `DiagnosticoAuthorizationServiceTest#criaSuporteClinicoIaComoProvisorioNaoConfirmado` | Sugestão da IA é salva como provisória, a decisão final é do veterinário | 61 e 6 |

| CT-UNIT-047 | `ArkiveUserDetailsServiceTest#crmvResolveMesmoUsuarioVeterinarioQuandoLoginNaoExiste` | Login por CRMV resolve o usuário veterinário | 16 |

| CT-UNIT-310 | `FinancialVisibilityServiceTest#veterinarioVeSomenteCustosDeConsultaPropria` | Veterinário vê só os custos das próprias consultas | 86 |



O pacote completo de evidências (57 classes unitárias, 523 execuções aprovadas) está em `sprint4-testes/`:



```text

sprint4-testes/

├── README.md

├── documentacao/      relatório, casos de teste, guia de execução, inventário CSV

├── codigo-fonte/      cópias das classes de teste

├── evidencias/        relatórios Surefire e resumo de resultados

└── configuracao/      pom.xml

```

