# CLAUDE.md

Acervo da legislação brasileira em Markdown (Constituição, normas federais, estaduais e distritais,
atos de reguladores, tratados, jurisprudência vinculante e legislação municipal), com cabeçalho YAML e
link para a fonte oficial. Visão geral e escopo: `README.md`. Números atuais: `PROGRESSO.md` e
`COBERTURA.md`.

## Escala: leia antes de buscar

O repositório tem mais de 1,2 milhão de arquivos e `.git` de ~4 GB. Por isso:

- **Nunca** use `Glob` com `**/*` na raiz nem `ls` recursivo. Delimite sempre a pasta (ex.: `federal/leis-ordinarias/2002/`).
- Para achar uma norma, prefira o nome do arquivo (`lei-10406-2002-01-10.md`) ou `Grep` com `path` restrito e `glob`. Para só listar quais arquivos casam, use `output_mode: files_with_matches` com `head_limit`.
- Busque por `urn:`, `numero:` ou `ementa:` no cabeçalho YAML antes de varrer o corpo do texto.
- Não rode `git log`/`git blame` sem limitar (`-n 20 -- <caminho>`); o histórico tem centenas de milhares de commits.
- Não leia `WORKLOG.md` (186 KB, diário de coleta), `PROGRESSO.md`, `estatisticas.html` nem `progresso.svg` inteiros, a menos que a tarefa seja sobre eles.
- Pastas e arquivos que começam com `_` (`_auditoria_*`, `_provas_*`, `_lotes`, `_fonte`, `_indice`, `_falhas.jsonl`) são insumo de auditoria e coleta, não legislação. Ignore ao consultar normas.

## Onde fica cada coisa

```
constituicao.md                    CF/1988 (único .md sem cabeçalho YAML)
federal/<tipo>/AAAA/*.md           leis-ordinarias, leis-complementares, decretos, decretos-lei,
                                   medidas-provisorias, emendas-constitucionais, regulamentos…
estaduais/<UF>/<tipo>/…            constituição, emendas, leis, decretos-lei etc. (inclui DF)
reguladores/<orgao>/<tipo>/        atos infralegais (CONTRAN, ANVISA, RFB, CMN/BCB, CVM…)
jurisprudencia/<tribunal>/         súmulas, súmulas vinculantes, repercussão geral, controle concentrado
tratados/<tema>/                   tratados internalizados
municipais/<UF>/<municipio>/       legislação municipal
scripts/                           coletores, conversores e auditorias (Python 3)
fonte/                             snapshots oficiais da Constituição (preservar bytes)
```

Arquivos de norma: `<tipo>-<numero>-<AAAA-MM-DD>.md`, p.ex. `federal/leis-ordinarias/2020/lei-13974-2020-01-07.md`.

## Formato do arquivo de norma

Cabeçalho YAML, depois `# Título` e o texto. Campos sempre presentes: `titulo`, `tipo`, `numero`,
`data`, `versao_texto`, `fonte`, `baixado_em`. Outros (`ementa`, `revogada`, `situacao`, `urn`,
`publicacao`, `uf`…) aparecem quando a fonte informa. O campo `fonte` traz a URL oficial.

## Ao responder sobre o direito

- Cite sempre o caminho do arquivo **e** a `fonte` do cabeçalho.
- Confira `revogada`, `situacao` e `versao_texto` (`original` x `compilada`) antes de afirmar que uma norma está em vigor. Compilado pode incorporar alterações; original não.
- O README avisa: os textos são conversões automáticas (e OCR em ~17 mil arquivos) e **não substituem o Diário Oficial**. Se houver dúvida de transcrição (numeração de artigos, valores), diga e remeta à fonte.
- Lacunas conhecidas estão em `COBERTURA.md`, no README (seção Pendências) e nos `_falhas.jsonl` de cada pasta. Se a norma não está no acervo, verifique ali antes de concluir que não existe.

## Convenções para alterar o repositório

- Rode scripts sempre com `python3 -I scripts/<nome>.py` (modo isolado). Dependências: Python 3, `pdftotext` (poppler), `pypdf`, `beautifulsoup4`, `lxml`, `PyYAML`, `requests`. OCR via Vision depende de macOS.
- Coletores são retomáveis e limitam a taxa (1–4 req/s). **Não contorne bloqueios de acesso automatizado.** Originais baixados vão para `_fonte/` (fora do git).
- Não edite `.md` de norma à mão: corrija o conversor/normalizador e regenere (`reconverter.py`, `normalizar.py`, `corrigir_ocr.py`, `corrigir_datas_invalidas.py`). O vocabulário de `tipo` é `CANONICO` em `scripts/comum.py`.
- `constituicao.md` é gerada por `converter_constituicao.py` (use `--check` para só conferir). Não altere `fonte/*.htm` nem os `.html`/`.bin` de `municipais/_fonte`: o SHA-256 depende dos bytes (ver `.gitattributes`).
- Testes: `python3 -I -m unittest discover -s scripts -p test_vigencia.py`.
- Hook de pré-commit em `.githooks/pre-commit` recalcula `PROGRESSO.md` e `progresso.svg`. Para ativá-lo localmente: `git config core.hooksPath .githooks`. Não commite esses dois arquivos à mão.
- Commits de lote (um por coleta) são o padrão atual; o histórico norma a norma só existe para a base inicial. Registre o andamento da coleta em `WORKLOG.md`.
- Nunca faça `git add -A`/`git add .` neste repositório: stage somente caminhos explícitos.
