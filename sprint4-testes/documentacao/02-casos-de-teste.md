# Documentação dos casos de teste

## 1. Fonte e rastreabilidade

Esta documentação foi construída a partir das classes Java copiadas em `../codigo-fonte/` e dos elementos `testcase` presentes nos XMLs Surefire. Assertions, exceções esperadas, valores parametrizados e verificações Mockito foram usados para descrever o comportamento esperado.

O arquivo `inventario-casos-teste.csv` é a matriz completa: possui uma linha para cada uma das 523 execuções. Elas correspondem a 481 métodos-base distintos; 15 métodos parametrizados produziram 57 execuções indexadas. O Markdown apresenta uma visão legível dos cenários mais representativos e documenta integralmente essa expansão.

Cada identificador `CT-UNIT-NNN` aponta para:

- classe e nome exato do caso informado pelo Surefire;
- código-fonte copiado;
- XML sanitizado da classe;
- tempo individual registrado;
- resultado efetivo.

## 2. Matriz resumida de casos

| ID | Módulo / classe e método | Objetivo | Condições e entradas verificadas | Resultado esperado e confirmado |
|---|---|---|---|---|
| CT-UNIT-253 | Consultas — `ConsultaServiceTest.criaConsultaSempreAgendadaQuandoStatusOmitidoOuAg` | Garantir o estado inicial da consulta | Request sem status ou com `AG`; veterinário e entidades retornados pelos mocks | Consulta persistida como agendada; aprovado |
| CT-UNIT-230 | Consultas — `ConsultaServiceTest.rejeitaCriacaoNoPassado` | Impedir agendamento retroativo | Data anterior ao instante do relógio fixo | Exceção de negócio antes da criação; aprovado |
| CT-UNIT-274 | Fluxo clínico — `ConsultaWorkflowServiceTest.inicioConsomeObservacaoPendenteUmaUnicaVezSobLockDoAnimal` | Preservar e consumir a observação do responsável atomicamente | Consulta `AG`, observação pendente e animal obtido para atualização | Consulta muda para `EP`, recebe snapshot e remove pendência do animal; aprovado |
| CT-UNIT-297 | Diagnóstico — `DiagnosticoAuthorizationServiceTest.criaSuporteClinicoIaComoProvisorioNaoConfirmado` | Separar hipótese de IA da conclusão veterinária | Dados de suporte clínico controlados e consulta autorizada | Diagnóstico da IA salvo como provisório/não confirmado; aprovado |
| CT-UNIT-159 | Provider clínico — `RenderClinicalSupportProviderTest.rejeitaTokenNegativoSemRejeitarMetadadosAusentes` | Rejeitar métricas de tokens inválidas | Resposta HTTP simulada com token negativo | `BusinessException` com erro de gateway; aprovado |
| CT-UNIT-093 | Paciente — `AnimalResponsavelServiceTest.responsavelLeSeusVinculosMasNaoConcedeAcesso` | Separar leitura de vínculo da gestão de acesso | Principal responsável vinculado | Leitura permitida e operação de concessão bloqueada; aprovado |
| CT-UNIT-128 | Paciente — `AnimalServiceAuthorizationTest.nascimentoOpcionalValidoNaCriacaoEAtualizacao` | Validar nascimento opcional | Primeira execução parametrizada; o método também usa `null`, `2021-04-17` e `2026-09-08` | Criação/atualização aceita os três valores e preserva a data; aprovado |
| CT-UNIT-345 | Solicitação — `PatientAccessRequestServiceTest.criaPendentePeloArkiveIdNormalizadoSemConcederAcessoNemExporNome` | Criar pedido sem conceder acesso antecipado | ArkIve ID normalizado, veterinário ativo e responsável elegível | Solicitação `PENDING`; nenhum acesso criado e nenhuma identidade clínica indevida exposta; aprovado |
| CT-UNIT-371 | Compartilhamento — `PatientShareServiceTest.wrongRoleDoesNotConsumeToken` | Impedir resgate por papel incompatível | Convite válido e usuário de papel diferente do destinatário | Resgate rejeitado e token permanece não consumido; aprovado |
| CT-UNIT-340 | Senha — `PasswordLifecycleServiceTest.trocaObrigatoriaSubstituiHashELimpaFlag` | Completar a troca obrigatória com armazenamento seguro | Nova senha e confirmação válidas; encoder e repository controlados | Hash substituído, flag de troca limpa e entidade salva; aprovado |
| CT-UNIT-503 | Usuário — `UsuarioServiceTest.rejeitaLoginDuplicado` | Preservar unicidade de login | Repository simula login já existente | Exceção de negócio e ausência de nova persistência; aprovado |
| CT-UNIT-310 | Custos — `FinancialVisibilityServiceTest.veterinarioVeSomenteCustosDeConsultaPropria` | Aplicar privacidade financeira entre profissionais | Contextos de veterinário proprietário e não proprietário | Proprietário vê o valor; outro veterinário não recebe o custo; aprovado |
| CT-UNIT-320 | Notificação — `NotificacaoServiceTest.consultaAgendadaNotificaCadaResponsavelComArkiveIdERecursoDaConsulta` | Criar uma notificação por destinatário elegível | Consulta agendada e responsáveis ativos retornados pelo repository | Uma linha por usuário, com paciente/ArkIve ID e referência à consulta; aprovado |
| CT-UNIT-325 | Push — `ExpoPushClientTest.identificaDeviceNotRegisteredNoTicketImediato` | Interpretar retorno de token inválido | Resposta Expo simulada com `DeviceNotRegistered` | Cliente retorna `DEVICE_NOT_REGISTERED`; nenhuma rede real; aprovado |
| CT-UNIT-427 | Áudio — `AudioConversionServiceTest.converteComArgumentosFixosELimpaTemporarios` | Padronizar áudio antes da transcrição | Primeira execução do conjunto `WEBM`, `M4A`, `AAC`; processo FFmpeg simulado | Argumentos incluem 16 kHz, mono e PCM 16 bits; temporários são removidos; aprovado |
| CT-UNIT-014 | Formato de áudio — `SupportedAudioFormatTest.resolveFormatosSuportados` | Mapear nome/MIME para formato suportado | Primeira das sete combinações válidas documentadas abaixo | Enum esperado retornado; aprovado |
| CT-UNIT-200 | Clínica — `ClinicaServiceTest.criarClinicaProvisionaUsuarioAdminClinicaComVinculo` | Vincular a nova clínica à conta administrativa | Clínica salva com ID e serviço de provisionamento simulado | Request de usuário `ADMIN_CLINICA`, login e ID da clínica corretos; aprovado |
| CT-UNIT-010 | OpenAPI — `OpenApiConfigTest.normalizaSortPageableParaStringSimplesSemSintaxeJsonArray` | Tornar o parâmetro de ordenação consumível | OpenAPI com `sort` inicialmente descrito como array | Parâmetro convertido para string, exemplo `dataHora,desc`, sem sintaxe de array; aprovado |

Os demais cenários, inclusive variações negativas e de autorização, estão individualizados no CSV. A descrição do CSV mantém o nome do método como fonte factual e não cria entradas inexistentes.

## 3. Métodos parametrizados

Os 523 casos não correspondem a 523 métodos Java. Quinze métodos parametrizados geraram múltiplos elementos `testcase`:

| Classe e método | Fonte de parâmetros efetivamente declarada | Execuções |
|---|---|---:|
| `SupportedAudioFormatTest.resolveFormatosSuportados` | `audio.webm/audio/webm/WEBM`; WebM com codec; nome sem extensão; WAV; M4A; MP4; AAC | 7 |
| `SupportedAudioFormatTest.rejeitaFormatosIncompativeis` | Cinco combinações incompatíveis: WebM/MP4, M4A/WebM, WAV/WebM, WebM/vídeo WebM e OGG | 5 |
| `AnimalServiceAuthorizationTest.nascimentoOpcionalValidoNaCriacaoEAtualizacao` | `null`, `2021-04-17`, `2026-09-08` | 3 |
| `ConsultaBusinessTimeTest.comparaMinutoDeSaoPauloIndependenteDoFusoDoServidor` | Oito instantes entre `2026-09-08T21:59:00` e `2026-09-09T00:01:00`, com booleano esperado | 8 |
| `ConsultaBusinessTimeTest.fronteiraDaMeiaNoite` | `23:58:59/false`, `23:59:00/true`, `00:00:00/true` | 3 |
| `ConsultaResumoPdfServiceTest.pdfIncluiEnderecoSomentePresencialPreenchido` | Presencial preenchido, nulo, branco e remoto preenchido | 4 |
| `ConsultaServiceTest.aceitaAgoraOuFuturo` | Acréscimos de `0`, `1` e `86400` segundos ao relógio fixo | 3 |
| `ConsultaServiceTest.enderecoVazioUsaClinica` | `null`, string vazia e espaços | 3 |
| `RacaServiceTest.rejeitaNomeInvalido` | `null`, vazio, espaço e nome acima do limite | 4 |
| `ResponsavelLookupServiceTest.naoPermiteEnumeracaoSemBusca` | `null`, vazio, espaço e texto de dois caracteres | 4 |
| `TranscricaoServiceTest.aceitaWebmDoNavegadorSemAlterarMime` | `audio/webm` e `audio/webm;codecs=opus` | 2 |
| `NotificacaoPushServiceTest.novosTiposFluemNoMesmoPayloadSemAlterarSchema` | `CONSULTA_AGENDADA` e `LEMBRETE_CONSULTA` | 2 |
| `AudioConversionServiceTest.converteComArgumentosFixosELimpaTemporarios` | `WEBM`, `M4A`, `AAC` | 3 |
| `AudioConversionServiceTest.falhasWebmSaoSegurasELimpamArquivos` | `corrupto`, `timeout`, `interrompido`, `indisponivel` | 4 |
| `AzureSpeechTranscriptionServiceTest.webmUsaConversaoExistenteELimpaMesmoQuandoAzureFalha` | `false` e `true` para falha simulada | 2 |

Há 15 linhas na tabela porque duas classes possuem dois métodos parametrizados cada. A contagem foi feita pelos XMLs, não pela quantidade de anotações.

## 4. Conteúdo do inventário CSV

O CSV usa ponto e vírgula, UTF-8 com BOM e contém 523 registros com as colunas:

1. ID do caso;
2. domínio;
3. classe;
4. método/caso Surefire;
5. método base;
6. descrição derivada do cenário implementado;
7. resultado;
8. tempo de execução em segundos;
9. caminho relativo do código;
10. caminho relativo da evidência XML.

Para casos parametrizados, cada índice Surefire recebe ID próprio. Os valores concretos são documentados na seção anterior e permanecem visíveis nas anotações do código copiado.

## 5. Critério de resultado

- `Aprovado`: o elemento `testcase` não possui `failure`, `error` ou `skipped`.
- `Reprovado (falha)`: existe elemento `failure`.
- `Reprovado (erro)`: existe elemento `error`.
- `Ignorado`: existe elemento `skipped`.

Nesta evidência, as 523 linhas estão como `Aprovado`.
