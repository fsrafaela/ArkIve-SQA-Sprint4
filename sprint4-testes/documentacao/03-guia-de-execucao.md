# Guia de execução dos testes unitários

## 1. Pré-requisitos

- JDK 17 configurado em `JAVA_HOME`;
- PowerShell;
- acesso ao repositório completo do ArkIve;
- acesso ao Maven Central no primeiro build, caso as dependências ainda não estejam no cache;
- Maven Wrapper do repositório ou Maven 3.9.x instalado.

Oracle, Groq, Render, Azure Speech e Expo não são necessários para esta seleção. As fronteiras externas são simuladas.

Confirme o ambiente:

```powershell
java -version
$env:JAVA_HOME
```

Quando o Maven estiver instalado:

```powershell
mvn -version
```

O `mvn -version` deve indicar Java 17, mesmo que outro Java esteja disponível no `PATH`.

## 2. Obter e abrir o projeto

Obtenha o repositório pelo canal da equipe, por clone ou arquivo compactado. Em caso de clone:

```powershell
git clone <URL_FORNECIDA_PELA_EQUIPE>
Set-Location <PASTA_DO_REPOSITORIO>
```

Abra como projeto Maven pela raiz que contém `pom.xml`. Não execute os testes a partir da pasta deste pacote, pois as cópias não incluem o código de produção.

## 3. Selecionar somente as classes unitárias

O script abaixo reproduz a seleção auditada. Ele exclui classes com contexto MVC, JPA ou Spring e as quatro classes sem contexto que foram classificadas como fluxo composto, migration ou contrato estrutural.

```powershell
$excluded = @(
  'AnimalRepositorySearchContractTest',
  'ClinicalWorkflowEndToEndServiceTest',
  'VeterinarioAuthenticationFlowTest',
  'V8__backfill_arkive_patient_idsTest'
)

$unitTests = Get-ChildItem -LiteralPath src\test\java -Recurse -Filter *.java |
  Where-Object {
    $source = [IO.File]::ReadAllText($_.FullName)
    $source -notmatch '@WebMvcTest\b|@AutoConfigureMockMvc\b|@DataJpaTest\b|@SpringBootTest\b' -and
      $_.BaseName -notin $excluded
  } |
  Sort-Object BaseName

$unitTests.Count
```

O resultado esperado para esta versão é `57`. Se o número for diferente, interrompa a reprodução e refaça a classificação, pois o conjunto de fontes mudou.

## 4. Executar com Maven Wrapper

```powershell
.\mvnw.cmd test "-Dtest=$($unitTests.BaseName -join ',')"
```

Em uma execução bem-sucedida, o terminal encerra com `BUILD SUCCESS` e apresenta os totais de testes, falhas, erros e ignorados.

## 5. Alternativa com Maven instalado

Se o Wrapper falhar, use um Maven 3.9.x instalado e disponível no `PATH`:

```powershell
mvn test "-Dtest=$($unitTests.BaseName -join ',')"
```

Na máquina da auditoria, `mvnw.cmd` falhou antes de iniciar o Maven com `Cannot start maven from wrapper` e `Cannot index into a null array`. A execução confirmada utilizou o Maven 3.9.15 já existente no cache local do Wrapper. Esse caminho é específico de cada usuário e, por isso, não deve ser fixado em um procedimento compartilhável.

Se houver uma distribuição Maven válida no cache, ela pode ser localizada sem caminho absoluto fixo:

```powershell
$cachedMaven = Get-ChildItem "$env:USERPROFILE\.m2\wrapper\dists" -Recurse -Filter mvn.cmd |
  Where-Object FullName -Match 'apache-maven-3\.9\.15' |
  Select-Object -First 1

& $cachedMaven.FullName test "-Dtest=$($unitTests.BaseName -join ',')"
```

Confirme antes que `$cachedMaven` não seja nulo e que `& $cachedMaven.FullName -version` use Java 17.

## 6. Executar uma única classe

Com Wrapper:

```powershell
.\mvnw.cmd test "-Dtest=ConsultaServiceTest"
```

Com Maven instalado:

```powershell
mvn test "-Dtest=ConsultaServiceTest"
```

Também é possível executar um método não parametrizado:

```powershell
mvn test "-Dtest=ConsultaServiceTest#rejeitaCriacaoNoPassado"
```

## 7. Consultar os resultados

O resumo aparece no terminal. Os relatórios detalhados ficam em:

```text
target/surefire-reports/
```

Para cada classe, o Surefire normalmente cria:

```text
TEST-<nome-qualificado-da-classe>.xml
<nome-qualificado-da-classe>.txt
```

O XML contém cada execução individual e seu tempo. Métodos parametrizados aparecem em múltiplos elementos `testcase`, geralmente identificados por índices como `[1]`, `[2]` e `[3]`.

## 8. Interpretação dos status

- `Failures`: uma assertion não foi satisfeita.
- `Errors`: ocorreu uma exceção não esperada ou falha de infraestrutura do teste.
- `Skipped`: execução ignorada ou abortada.
- `BUILD SUCCESS`: o ciclo Maven terminou sem falha para a seleção executada.

O resultado de referência deste pacote é 523 execuções, 0 falhas, 0 erros e 0 ignorados. Resultados futuros devem ser tratados como uma nova evidência, sem sobrescrever a data desta auditoria.
