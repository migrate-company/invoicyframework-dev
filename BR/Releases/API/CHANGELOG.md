# InvoiCy Framework API — Changelog

A InvoiCy Framework API expõe o InvoiCy Framework por HTTP/JSON, para Aplicativos Comerciais que não carregam
a biblioteca nativa (`.dll`/`.so`) diretamente. Cada método da API chama o método de mesmo nome do Framework;
regras fiscais, parâmetros de configuração e layout dos retornos são os do
[Manual de Integração do InvoiCy Framework](../../Documentos/Manual_InvoiCy_Framework.pdf).

## 1.0.0 — 09/11/2026

Primeira versão distribuível, para testes de integração.

| | |
|---|---|
| Pacote | [`InvoiCyFrameworkAPI_FW01.50.04_20261109_1358.zip`](https://drive.google.com/file/d/1JXPpRnqrqmcq8tsUriYaSsf2ZF7GosHc/view?usp=sharing) (Google Drive, 137 MB) |
| SHA-256 do pacote | `f89690a76954e3834b08e1b4e7dd992728507e510daf186196c9066b80080433` |
| InvoiCy Framework | 01.50.04 (Linux 64 bits) |
| Documentação | [Interface_InvoiCy_Framework_API.pdf](Interface_InvoiCy_Framework_API.pdf) |

### Instalação

Baixe o pacote pelo link acima (no Google Drive, confirme o download quando aparecer o aviso de que o
arquivo é grande demais para a verificação de vírus) e confira o SHA-256:

- Windows (PowerShell): `Get-FileHash .\InvoiCyFrameworkAPI_FW01.50.04_20261109_1358.zip`
- Linux: `sha256sum InvoiCyFrameworkAPI_FW01.50.04_20261109_1358.zip`

O pacote traz o sistema de arquivos do serviço (`ifw_api_imagem.tar`), scripts de instalação, execução e
remoção, e um `LEIA-ME.txt` com o passo a passo. Execute os scripts de dentro da pasta deles.

- **Linux** — com Docker: `linux/1-instalar.sh`, `2-executar.sh`, `3-desinstalar.sh`. O serviço roda no
  container `ifwapi`.
- **Windows** — com WSL 2 habilitado: `windows\1-instalar.bat`, `2-executar.bat`, `3-desinstalar.bat`. O serviço
  roda na distribuição WSL `ifwapi`; não é necessário Docker.

A API atende em `http://<host>:3000/api`. Teste: `http://localhost:3000/api/Versao` deve responder
`{"status":"ok","retorno":"15004"}`.

### Interface

- HTTP com JSON. Métodos com parâmetros usam `POST` com `{"parametros": [...]}` (posicional, na ordem do
  método do Framework); métodos sem parâmetros usam `GET`.
- Resposta `{"status":"ok","retorno":"..."}`, com o retorno do método do Framework como texto. `status: "ok"`
  indica que o método foi executado; o resultado da operação está em `retorno`.
- Requisições de até 10 MB (documentos com centenas de itens).
- As chamadas são executadas em fila, uma por vez, na ordem de chegada. Durante uma chamada demorada o serviço
  continua aceitando requisições, que aguardam a vez.
- O Framework é inicializado quando o serviço sobe e finalizado quando ele é encerrado. Configurações e
  numeração ficam gravadas e não precisam ser refeitas a cada início.

**Métodos disponíveis (43)**

| Grupo | Métodos |
|---|---|
| Configuração | `ConfiguraInvoicyEx`, `AmbienteEmissao`, `ConfiguraInvoicy`, `Configura`, `ConfiguraDiretorio` |
| Emissão, eventos e consultas | `EmiteDocumento`, `EmiteDocumentoJSON`, `EmiteContingencia` |
| Impressão | `ImprimeDocumento`, `ImprimeDocumentoEx`, `ImprimeTexto`, `AbrirGaveta`, `Servico` |
| Documentos pendentes | `RelatorioPendentes`, `EnviaDocumentosPendentes`, `EnviaDocumentosPendentesQuantidade` |
| Numeração | `ObtemUltimoSequencialDFe`, `DefineSequencialDFe` |
| Informações e utilitários | `Versao`, `GetModuleIdentity`, `ObtemParametro`, `ObtemDataHoraUF`, `AtualizaAgora`, `ExecuteCommand` |
| Paraguai (SIFEN) | `ConfigurarInvoicyEx`, `ConfigurarInvoicy`, `AmbienteEmision`, `Configurar`, `ConfigurarDirectorio`, `Emitir`, `ImprimirDE`, `InformePendientes`, `EnviarDocumentosPendientes`, `EnviarDocumentosPendientesCantidad`, `Version` |

### Segurança

> **Atenção** — a API **não tem autenticação** (nenhum usuário, senha, token ou chave) e usa **HTTP sem
> criptografia**. Qualquer programa que alcance a porta 3000 pode emitir e cancelar documentos, trocar o
> ambiente de emissão e as credenciais da empresa. Deixe a porta acessível somente à própria máquina ou aos
> Aplicativos Comerciais autorizados, nunca à internet. Veja as recomendações no documento da interface.

### Limitações conhecidas

- **Emitir para um CNPJ que não foi configurado no ambiente interrompe o serviço** (falha do Framework 01.50.04,
  já registrada para correção). Configure cada empresa com `ConfiguraInvoicyEx` antes da primeira emissão.
  Se o serviço parar, inicie-o de novo com o script `2-executar`.
- Uma chamada por vez por instalação. Não execute duas instalações sobre os mesmos dados.
- Impressão: somente impressoras de rede (`SERVIDOR:<ip>,<porta>`) ou o modo `BITMAP`, em que o DANFE volta como
  imagem no retorno da emissão.
- Configuração das empresas, numeração e documentos pendentes de contingência ficam dentro da instalação.
  **Desinstalar ou reinstalar apaga esses dados.** Antes, envie os pendentes (`EnviaDocumentosPendentes` até 0) e
  anote a numeração (`ObtemUltimoSequencialDFe`) para restaurá-la com `DefineSequencialDFe`.
- O serviço não é reiniciado automaticamente se parar.

### Fora do escopo desta versão

- Autenticação e HTTPS.
- Métodos do Framework não expostos: `Log`, `Alertas`, `RelatorioPendentesErro`,
  `QuantidadeDocumentosPendentesErro`, `ArquivoSistema`, `ObtemUltimoSequencialNFCe` (use
  `ObtemUltimoSequencialDFe`), `InsereParametrosEmissao` e `ConfiguraUF` (dados obtidos do InvoiCy), SAT e MFE.
- Impressoras USB, seriais e spool do Windows.
