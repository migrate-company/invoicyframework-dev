# Changelog

## 0.1.2

Correcao de um defeito que impedia QUALQUER configuracao no Android. Nenhuma
mudanca na API Dart.

- **`configuraInvoicy` falhava sempre no Android**, com
  `ChamadaNativaException: configuraInvoicy devolveu 0 (o canal devolveu nulo)`.
  O mesmo valia para `ambienteEmissao`, `configura`, `configuraDiretorio` e
  `arquivoSistema` -- todas as operacoes sem retorno.

  A causa estava em `impl_canal.dart`: um unico `_chama<T>` tratava retorno nulo
  como erro sempre que `T != Null`, e para `T == void` esse teste da verdadeiro.
  O shim Java responde `success(null)` para operacoes sem retorno, que e o
  resultado correto delas -- quem confere o codigo da fachada Java e o `exigeUm`
  do lado Java, que converte retorno != 1 em `PlatformException`.

  O helper foi separado em dois: `_pede<T>` para quem espera valor (`versao`,
  `emiteDocumento`), onde nulo continua sendo erro, e `_executa` para quem nao
  espera. Quem chama declara o que quer, em vez de o codigo deduzir por
  comparacao de tipo em tempo de execucao.

- **AAR em 1.50.04.** A biblioteca Android foi renumerada para acompanhar a
  versao do framework nativo, em vez de seguir uma serie propria. A codificacao
  de `Versao()` continua a do desktop (introduzida na 1.40.16): `0x015004` vira
  `15004`.

- Novo `test/impl_canal_test.dart`, com o canal mockado, travando esse contrato:
  operacao sem retorno aceita nulo, operacao com retorno o rejeita, e
  `PlatformException` continua virando `ChamadaNativaException`. Era a lacuna que
  deixou o defeito chegar ao cliente -- os testes cobriam a tabela de
  capacidades e o trilho FFI, nunca a camada do canal.

## 0.1.1

Correcao de empacotamento do Android. Nenhuma mudanca na API Dart.

- **O Android nao emitia: o aplicativo fechava na primeira emissao.** O AAR
  trazia `ksoap2.jar` e `sat.jar` embutidos em `libs/`, declarados no projeto
  Android como `implementation fileTree(...)` -- o que os embute no AAR mas
  **nao os declara no POM**. Consumido pelo Maven, o AAR chegava sem as 403
  classes dessas duas bibliotecas. O aplicativo compilava, subia e respondia
  `versao()`, e morria com `NoClassDefFoundError` na primeira chamada que usa
  SOAP, ou seja, na emissao. Diagnosticado em 15/10/2026 a partir do APK de um
  cliente.

  Os dois passam a ser artefatos Maven proprios -- `info.migrate:ksoap2` e
  `info.migrate:zpm-sat` -- declarados no POM do AAR, e acompanham o pacote em
  `invoicy_framework/android/repo/`.

- AAR atualizado para **1.40.16**. A versao mudou junto com a correcao para que
  nenhum cache do Gradle resolva o AAR anterior, que tem o defeito.

- **O `Versao()` do Android passou a usar a codificacao do desktop.** A fachada
  Java devolvia a constante crua (`0x014015` = 81941) -- uma terceira
  codificacao da mesma ideia, que so existia ali. Agora aplica o mesmo
  `atoi(sprintf("%lx", ...))` do C++ e devolve `14016` para a versao 01.40.16,
  igual ao Windows e ao Linux. `impl_canal.dart` passou a decodificar com
  `versaoDeDigitosDecimais`, o mesmo do desktop.

  Para quem usa `versao()` nada muda -- era justamente o ponto de a fachada
  expor so a string. Para quem le `versaoBruta()` no Android, o numero muda: de
  81941 para 14016. Um AAR 1.40.15 ainda em campo continua devolvendo o valor
  antigo, e por isso a decodificacao segue morando na implementacao de cada
  plataforma.

- Corrigido de passagem um erro de documentacao: cinco lugares diziam que o
  `Versao()` do Android devolvia `86037`, que e `0x015015` (01.50.15). O valor
  de `0x014015` e `81941`.

## 0.1.0

Primeira versao distribuivel. Uma API Dart, tres plataformas, sem codigo condicional no
aplicativo que consome.

### Plataformas

- **Windows x64** -- `dart:ffi` sobre `InvoiCyFramework.dll`. O CMake do plugin coloca a
  DLL junto do executavel. A DLL e autocontida: nenhum redistribuivel a instalar.
- **Linux x64** -- `dart:ffi` sobre `libInvoiCyFramework.so`, compilada em CentOS 7 para
  alcancar tambem as distribuicoes mais novas. Depende de `libcurl.so.4`,
  `libxml2.so.2`, `libz.so.1` e `libusb-1.0.so.0`; o OpenSSL e estatico.
- **Android (API 24+)** -- `MethodChannel` sobre um shim Java, sobre o AAR
  `info.migrate:invoicyframework-android`. O AAR acompanha o pacote em um repositorio
  Maven em arquivo, entao nao ha repositorio a configurar do lado de quem integra.

### API

- `InvoicyFramework.abre()` -- uma instancia por processo, espelhando a biblioteca, que
  tem estado global.
- **Toda a API e assincrona**, inclusive `versao()`. E o preco de o Android ir por canal
  de plataforma, e o que permite a mesma assinatura nas tres plataformas.
- `configuraInvoicy(ambiente:, cnpj:, chaveAcesso:, chaveParceiro:)` -- o `ambiente` e
  **obrigatorio** e vai primeiro. As credenciais sao gravadas por ambiente; a ordem
  inversa as grava sob o ambiente errado sem produzir erro nenhum. Nesta API a ordem
  errada nao e expressavel.
- `ambienteEmissao`, `configura`, `emiteDocumento`, `configuraDiretorio`,
  `arquivoSistema`, `log`, `identidadeModulo`.
- `emiteDocumento` emite, consulta ou envia evento conforme o **elemento raiz do XML**.
  No desktop roda em isolate separada; no Android, num executor de uma thread. A
  interface nao congela.
- `Ambiente` como enum em vez de `1`/`2` crus.
- Excecoes tipadas -- `BibliotecaNativaException` (com a lista de caminhos tentados),
  `ChamadaNativaException`, `OperacaoNaoSuportadaException` -- em vez de codigos de
  retorno.
- `Capacidades` / `OperacaoSdk` -- o que existe em cada plataforma, consultavel em tempo
  de execucao. Operacao ausente lanca; operacao degradada funciona e carrega a ressalva.

### Codificacao de versao

`versao()` devolve `String` e e a unica representacao tratada como verdade. O inteiro
por tras vem em codificacoes **diferentes** por plataforma -- `15000` no desktop para a
versao 01.50.00, a constante crua no Android -- e cada implementacao decodifica a sua.
`versaoBruta()` existe para diagnostico; nao compare esse numero entre plataformas.

### Fora do escopo desta versao

- `EmiteDocumentoJSON`, por nao existir na fachada Android.
- Impressao, SAT/MFE, relatorios de pendentes, sequenciais e servicos -- as demais
  funcoes das 46 da ABI.
- Apelidos em espanhol e ingles da ABI: a API Dart tem um nome por operacao.
- `InsereParametrosEmissao`, depreciada.
- `AtualizaAgora`, exposta na ABI nativa mas ainda sem ligacao Dart.
- iOS, macOS e web.
