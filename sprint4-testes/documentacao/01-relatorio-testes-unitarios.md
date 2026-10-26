# Relatório técnico dos testes unitários

## 1. Introdução

Os testes unitários do ArkIve validam regras de negócio, políticas de autorização, transformações de dados e tratamento de falhas em unidades executadas sem contexto completo do Spring e sem banco externo. O objetivo desta entrega é fornecer rastreabilidade entre classe, cenário e resultado executado para a disciplina de Software Quality Assurance.

O repositório possui 95 classes automatizadas. Destas, 57 atendem ao critério unitário adotado; as 20 classes MVC, 17 classes de integração e a classe estrutural JPQL permanecem fora dos totais deste relatório.

## 2. Ambiente verificado

| Componente | Versão/observação |
|---|---|
| Java do build | Eclipse Adoptium 17.0.18 |
| Java definido no projeto | 17 |
| Spring Boot | 3.5.14 |
| JUnit Jupiter | 5.12.2 |
| Mockito | 5.17.0 |
| Maven | 3.9.15 |
| Maven Surefire | 3.5.5 |
| Provider Surefire | `JUnitPlatformProvider` |
| Sistema da execução | Windows 11 amd64 |

As versões foram obtidas do `pom.xml`, do classpath registrado pelo Surefire e do Maven usado na execução. O comando `java` global da máquina apontava para Java 22, mas o Maven foi efetivamente iniciado pelo JDK 17.0.18 configurado no ambiente do build.

## 3. Metodologia e critério de isolamento

Uma classe foi classificada como unitária quando não carregava `@SpringBootTest`, `@DataJpaTest`, `@WebMvcTest` ou `MockMvc`, e seu objeto sob teste era criado diretamente. As dependências foram controladas por:

- Mockito para repositories, services colaboradores, gateways, relógios e processos;
- stubs e respostas em memória;
- `MockRestServiceServer` para HTTP simulado, sem rede externa;
- arquivos temporários JUnit em testes de áudio;
- funções e objetos de domínio puros quando não havia dependências.

Quatro classes sem contexto Spring foram deliberadamente excluídas da categoria unitária: dois fluxos que compõem múltiplos serviços reais, uma migration executada em H2 e um teste estrutural que inspeciona JPQL.

Legenda de frameworks: **J5** = JUnit 5; **M** = Mockito; **MRSS** = `MockRestServiceServer`.

## 4. Inventário completo

### 4.1 Consultas e fluxo clínico

| Classe | Caminho original | Funcionalidade e principais cenários | Exec. | Frameworks |
|---|---|---|---:|---|
| `AdesaoPrescricaoServiceTest` | `src/test/java/br/com/fiap/arkive/service/AdesaoPrescricaoServiceTest.java` | Registro e leitura de adesão; autorização; imutabilidade; validações e eventos | 13 | J5+M |
| `ConsultaBusinessTimeTest` | `src/test/java/br/com/fiap/arkive/service/ConsultaBusinessTimeTest.java` | Horário de São Paulo, meia-noite, reagendamento, edição histórica e relógio de negócio | 13 | J5+M parametrizado |
| `ConsultaReminderPropertiesTest` | `src/test/java/br/com/fiap/arkive/config/ConsultaReminderPropertiesTest.java` | Antecedência padrão e limites das propriedades de lembrete | 1 | J5 |
| `ConsultaReminderSchedulerTest` | `src/test/java/br/com/fiap/arkive/service/notification/ConsultaReminderSchedulerTest.java` | Delegação da tarefa agendada ao serviço testável | 1 | J5+M |
| `ConsultaReminderServiceTest` | `src/test/java/br/com/fiap/arkive/service/ConsultaReminderServiceTest.java` | Janela temporal, idempotência, veterinário inativo e isolamento de consulta malformada | 4 | J5+M |
| `ConsultaResumoPdfServiceTest` | `src/test/java/br/com/fiap/arkive/service/ConsultaResumoPdfServiceTest.java` | PDF final, autorização, prescrições, textos longos e endereço presencial | 11 | J5+M parametrizado |
| `ConsultaServiceTest` | `src/test/java/br/com/fiap/arkive/service/ConsultaServiceTest.java` | Criação, edição, exclusão, escopo, filtros, datas, custos e endereço | 37 | J5+M parametrizado |
| `ConsultaStatusTransitionPolicyTest` | `src/test/java/br/com/fiap/arkive/domain/consulta/ConsultaStatusTransitionPolicyTest.java` | Transições válidas, saltos inválidos e estados terminais | 3 | J5 |
| `ConsultaWorkflowServiceTest` | `src/test/java/br/com/fiap/arkive/service/ConsultaWorkflowServiceTest.java` | Início, narrativa, finalização, cancelamento, observação pendente e autorização | 21 | J5+M |
| `PrescricaoAuthorizationServiceTest` | `src/test/java/br/com/fiap/arkive/service/PrescricaoAuthorizationServiceTest.java` | Prescrição, custo, escopo clínico, privacidade financeira, adesão e bloqueios | 16 | J5+M |

Subtotal: **10 classes e 120 execuções aprovadas**.

### 4.2 Diagnósticos e suporte da IA

| Classe | Caminho original | Funcionalidade e principais cenários | Exec. | Frameworks |
|---|---|---|---:|---|
| `ClinicalSupportPersistenceServiceTest` | `src/test/java/br/com/fiap/arkive/service/ClinicalSupportPersistenceServiceTest.java` | Persistência do suporte, alteração concorrente de estado e recuperação | 4 | J5+M |
| `ClinicalSupportResponseTest` | `src/test/java/br/com/fiap/arkive/dto/response/ClinicalSupportResponseTest.java` | Fontes, RAG, tokens, avaliação veterinária e JSON legado/malformado | 10 | J5 |
| `ClinicalSupportServiceTest` | `src/test/java/br/com/fiap/arkive/service/ClinicalSupportServiceTest.java` | Provider, estados EP/AP, idempotência, concorrência, recuperação e autorização | 12 | J5+M |
| `DiagnosticoAuthorizationServiceTest` | `src/test/java/br/com/fiap/arkive/service/DiagnosticoAuthorizationServiceTest.java` | Criação/leitura autorizada, campos controlados, severidade, tokens e fontes | 13 | J5+M |
| `DiagnosticoValidacaoVeterinariaServiceTest` | `src/test/java/br/com/fiap/arkive/service/DiagnosticoValidacaoVeterinariaServiceTest.java` | Avaliação útil/não útil, alteração, idempotência e rejeições de acesso | 7 | J5+M |
| `RenderClinicalSupportProviderTest` | `src/test/java/br/com/fiap/arkive/service/clinical/RenderClinicalSupportProviderTest.java` | Contrato JSON, RAG, tokens, quota, payload inválido, servidor e timeout | 14 | J5+MRSS |

Subtotal: **6 classes e 60 execuções aprovadas**. Nenhuma requisição atingiu a IA real.

### 4.3 Pacientes e responsáveis

| Classe | Caminho original | Funcionalidade e principais cenários | Exec. | Frameworks |
|---|---|---|---:|---|
| `AnimalResponsavelServiceTest` | `src/test/java/br/com/fiap/arkive/service/AnimalResponsavelServiceTest.java` | Vínculos, responsável principal, vigência e permissões por perfil | 11 | J5+M |
| `AnimalServiceAuthorizationTest` | `src/test/java/br/com/fiap/arkive/service/AnimalServiceAuthorizationTest.java` | Matriz de autorização, cadastro, edição, clínica, observação, filtros e nascimento | 39 | J5+M parametrizado |
| `ArkivePatientIdGeneratorTest` | `src/test/java/br/com/fiap/arkive/service/ArkivePatientIdGeneratorTest.java` | Formato e variabilidade do identificador público ArkIve | 2 | J5 |
| `RacaServiceTest` | `src/test/java/br/com/fiap/arkive/service/RacaServiceTest.java` | Normalização, nome, espécie ativa, duplicidade e conflito concorrente | 9 | J5+M parametrizado |
| `RelatoAnimalServiceTest` | `src/test/java/br/com/fiap/arkive/service/RelatoAnimalServiceTest.java` | Criação, autoria, limites, data, autorização, normalização e paginação | 8 | J5+M |
| `RelatoSearchNormalizerTest` | `src/test/java/br/com/fiap/arkive/service/RelatoSearchNormalizerTest.java` | Acentos, caixa, espaços, ausência e ausência de stemming | 3 | J5 |
| `ResponsavelLookupServiceTest` | `src/test/java/br/com/fiap/arkive/service/ResponsavelLookupServiceTest.java` | Proteção contra enumeração, busca mínima, paginação e ordenação | 5 | J5+M parametrizado |
| `ResponsavelRegistrationServiceTest` | `src/test/java/br/com/fiap/arkive/service/ResponsavelRegistrationServiceTest.java` | Cadastro, papel fixo, senha, CPF/RG, telefone, duplicidades e erro transacional | 13 | J5+M |

Subtotal: **8 classes e 90 execuções aprovadas**.

### 4.4 Compartilhamento e permissões

| Classe | Caminho original | Funcionalidade e principais cenários | Exec. | Frameworks |
|---|---|---|---:|---|
| `ClinicalAccessServiceTest` | `src/test/java/br/com/fiap/arkive/service/ClinicalAccessServiceTest.java` | Acesso de responsável, veterinário, clínica e SysAdmin; falha fechada | 20 | J5+M |
| `PatientAccessRequestServiceTest` | `src/test/java/br/com/fiap/arkive/service/PatientAccessRequestServiceTest.java` | Solicitação, duplicidade, decisão, idempotência, listagem e privacidade | 14 | J5+M |
| `PatientAccessServiceTest` | `src/test/java/br/com/fiap/arkive/service/PatientAccessServiceTest.java` | Concessão idempotente e revogação lógica | 2 | J5+M |
| `PatientShareServiceTest` | `src/test/java/br/com/fiap/arkive/service/PatientShareServiceTest.java` | Convites, papéis, token, preview, resgate, expiração e revogação | 13 | J5+M |

Subtotal: **4 classes e 49 execuções aprovadas**.

### 4.5 Autenticação e segurança

| Classe | Caminho original | Funcionalidade e principais cenários | Exec. | Frameworks |
|---|---|---|---:|---|
| `AccountProvisioningServiceTest` | `src/test/java/br/com/fiap/arkive/service/AccountProvisioningServiceTest.java` | Provisionamento por perfil e validação das associações | 4 | J5+M |
| `ArkiveUserDetailsServiceTest` | `src/test/java/br/com/fiap/arkive/security/ArkiveUserDetailsServiceTest.java` | Carregamento por login/CRMV, ausência e estado da conta | 4 | J5+M |
| `AuthServiceTest` | `src/test/java/br/com/fiap/arkive/service/AuthServiceTest.java` | Identidade autenticada e autocadastro seguro | 5 | J5+M |
| `InitialSysAdminBootstrapTest` | `src/test/java/br/com/fiap/arkive/bootstrap/InitialSysAdminBootstrapTest.java` | Bootstrap desabilitado, conta existente, criação e configuração obrigatória | 7 | J5+M |
| `PasswordLifecycleServiceTest` | `src/test/java/br/com/fiap/arkive/service/PasswordLifecycleServiceTest.java` | Troca obrigatória/normal, senha atual, confirmação, política e reset | 4 | J5+M |
| `PasswordPolicyTest` | `src/test/java/br/com/fiap/arkive/security/PasswordPolicyTest.java` | Comprimento, maiúscula, minúscula e número | 3 | J5 |
| `TemporaryPasswordGeneratorTest` | `src/test/java/br/com/fiap/arkive/security/TemporaryPasswordGeneratorTest.java` | Formato e aderência da credencial temporária à política | 1 | J5 |
| `UsuarioPrincipalTest` | `src/test/java/br/com/fiap/arkive/security/UsuarioPrincipalTest.java` | Authorities, IDs vinculados, troca obrigatória e principal autenticado | 11 | J5 |
| `UsuarioServiceTest` | `src/test/java/br/com/fiap/arkive/service/UsuarioServiceTest.java` | Associações, criação, BCrypt, estados, edição, último SysAdmin e vínculos | 43 | J5+M |
| `VeterinarioServiceTest` | `src/test/java/br/com/fiap/arkive/service/VeterinarioServiceTest.java` | Cadastro, autocadastro, BCrypt, CRMV, propagação de erro e perfil próprio | 9 | J5+M |

Subtotal: **10 classes e 91 execuções aprovadas**.

### 4.6 Custos assistenciais

| Classe | Caminho original | Funcionalidade e principais cenários | Exec. | Frameworks |
|---|---|---|---:|---|
| `AnimalCustoServiceTest` | `src/test/java/br/com/fiap/arkive/service/AnimalCustoServiceTest.java` | Resumo do paciente, agregação, valor nulo/zero e escopo | 10 | J5+M |
| `FinancialVisibilityServiceTest` | `src/test/java/br/com/fiap/arkive/service/FinancialVisibilityServiceTest.java` | Visibilidade por perfil, propriedade e clínica; falha fechada | 3 | J5 |
| `VeterinarioCustoServiceTest` | `src/test/java/br/com/fiap/arkive/service/VeterinarioCustoServiceTest.java` | Dashboard, consultas finalizadas, valores ausentes e identidade autenticada | 4 | J5+M |

Subtotal: **3 classes e 17 execuções aprovadas**.

### 4.7 Notificações

| Classe | Caminho original | Funcionalidade e principais cenários | Exec. | Frameworks |
|---|---|---|---:|---|
| `ExpoPushClientTest` | `src/test/java/br/com/fiap/arkive/service/notification/ExpoPushClientTest.java` | Push desabilitado, sucesso, dispositivo removido, HTTP inválido e timeout | 5 | J5+MRSS |
| `NotificacaoPushListenerTest` | `src/test/java/br/com/fiap/arkive/service/notification/NotificacaoPushListenerTest.java` | Execução após commit, delegação e contenção de falhas | 2 | J5+M |
| `NotificacaoPushServiceTest` | `src/test/java/br/com/fiap/arkive/service/notification/NotificacaoPushServiceTest.java` | Preferência, dispositivos, payload, tipos, desativação e tolerância a falha | 7 | J5+M parametrizado |
| `NotificacaoServiceTest` | `src/test/java/br/com/fiap/arkive/service/NotificacaoServiceTest.java` | Destinatários, agendamento, lembrete, solicitação, inbox e idempotência | 13 | J5+M |
| `UsuarioDispositivoServiceTest` | `src/test/java/br/com/fiap/arkive/service/UsuarioDispositivoServiceTest.java` | Registro, atualização, reativação, posse, soft delete e resposta segura | 6 | J5+M |

Subtotal: **5 classes e 33 execuções aprovadas**.

### 4.8 Transcrição de áudio

| Classe | Caminho original | Funcionalidade e principais cenários | Exec. | Frameworks |
|---|---|---|---:|---|
| `AudioConversionServiceTest` | `src/test/java/br/com/fiap/arkive/service/speech/AudioConversionServiceTest.java` | Argumentos FFmpeg, formatos, falha, timeout, interrupção e limpeza | 8 | J5+M parametrizado |
| `AzureSpeechConfigTest` | `src/test/java/br/com/fiap/arkive/config/AzureSpeechConfigTest.java` | Vocabulário veterinário configurado | 1 | J5 |
| `AzureSpeechTranscriptionServiceTest` | `src/test/java/br/com/fiap/arkive/service/speech/AzureSpeechTranscriptionServiceTest.java` | Segmentos, cancelamento, timeout, áudio vazio, limpeza e falhas SDK | 7 | J5+M+`@TempDir` |
| `SupportedAudioFormatTest` | `src/test/java/br/com/fiap/arkive/domain/transcricao/SupportedAudioFormatTest.java` | Resolução e rejeição de combinações de arquivo/MIME | 12 | J5 parametrizado |
| `TranscricaoServiceTest` | `src/test/java/br/com/fiap/arkive/service/TranscricaoServiceTest.java` | Idiomas, tamanho, MIME, WAV, ausência de fala e perfis autorizados | 14 | J5+M parametrizado |

Subtotal: **5 classes e 42 execuções aprovadas**.

### 4.9 Administração e demais funcionalidades

| Classe | Caminho original | Funcionalidade e principais cenários | Exec. | Frameworks |
|---|---|---|---:|---|
| `ClinicaServiceTest` | `src/test/java/br/com/fiap/arkive/service/ClinicaServiceTest.java` | Cadastro, provisionamento, BCrypt, e-mail, propagação transacional e atualização | 5 | J5+M |
| `GlobalExceptionHandlerTest` | `src/test/java/br/com/fiap/arkive/exception/GlobalExceptionHandlerTest.java` | Respostas 403, validação, 405 e ordenação inválida | 4 | J5+M; chamada direta |
| `OpenApiConfigTest` | `src/test/java/br/com/fiap/arkive/config/OpenApiConfigTest.java` | Normalização do parâmetro `sort` no contrato OpenAPI | 1 | J5 |
| `PdfBrandHeaderTest` | `src/test/java/br/com/fiap/arkive/service/PdfBrandHeaderTest.java` | Transparência do símbolo no cabeçalho PDF | 1 | J5 |
| `PublicRequestContractTest` | `src/test/java/br/com/fiap/arkive/dto/request/PublicRequestContractTest.java` | Campos públicos e ausência de propriedades controladas pelo servidor | 4 | J5 |
| `SysAdminDashboardServiceTest` | `src/test/java/br/com/fiap/arkive/service/SysAdminDashboardServiceTest.java` | Contadores, 30 dias, série mensal, espécies, perfis e status | 6 | J5+M |

Subtotal: **6 classes e 21 execuções aprovadas**.

## 5. Resultado consolidado

| Métrica | Resultado |
|---|---:|
| Classes unitárias | 57 |
| Execuções reais Surefire | 523 |
| Falhas | 0 |
| Erros | 0 |
| Ignoradas | 0 |
| Build | `BUILD SUCCESS` |

Os totais foram recalculados a partir dos 57 XMLs Surefire correspondentes. Métodos parametrizados foram contados pelas execuções `testcase`, não apenas pelas anotações Java.

O inventário de cada execução está em `inventario-casos-teste.csv`. Os relatórios sanitizados ficam em `../evidencias/relatorios-surefire/`.
