# Avaliação de Grau 1

Este repositório reúne os documentos da avaliação da disciplina Desenvolvimento de Sistemas. O trabalho analisa a issue [#3 do projeto +Alunos](https://github.com/BitStudioLabs/mais-alunos/issues/3), atribuída a Weversson e fechada em 28/08/2026.

## Arquivos

- [`docs/issues/3/questoes.md`](docs/issues/3/questoes.md): respostas às questões 1, 2 e 3, com referências ao código, aos testes e aos commits da implementação.
- [`docs/issues/3/sequencia.mmd`](docs/issues/3/sequencia.mmd): fonte Mermaid do diagrama de sequência.
- [`docs/issues/3/sequencia.png`](docs/issues/3/sequencia.png): imagem do diagrama para anexar à entrega.

O código analisado pertence ao repositório [BitStudioLabs/mais-alunos](https://github.com/BitStudioLabs/mais-alunos), no snapshot [342f9fee04f4063566001d3dfcc22e7d69ede6d6](https://github.com/BitStudioLabs/mais-alunos/tree/342f9fee04f4063566001d3dfcc22e7d69ede6d6), incorporado em `unstable`. Todas as referências de arquivo e linha usam esse snapshot, independentemente de alterações posteriores na branch. Os commits de implementação citados no documento são `a537160` e `368f910`. Este repositório contém apenas o material da avaliação, não o código da aplicação.

O documento declara o uso de inteligência artificial, conforme a regra da avaliação. O arquivo `.gitignore` exclui configurações locais de assistentes e rascunhos de IA.

## Exportar o diagrama

Edite `docs/issues/3/sequencia.mmd` e gere novamente o PNG a partir dessa mesma fonte. Na raiz deste repositório, com Node.js e npm instalados:

```bash
npx --yes --package=@mermaid-js/mermaid-cli@11.12.0 -- mmdc -i docs/issues/3/sequencia.mmd -o docs/issues/3/sequencia.png -b white -w 1800 -s 2
```

O Mermaid CLI usa um navegador Chromium/Puppeteer para renderizar a imagem. Se for necessário usar um navegador já instalado, informe sua configuração com `-p /caminho/puppeteer.json`, conforme a [documentação do CLI](https://github.com/mermaid-js/mermaid-cli/tree/11.12.0). Versione a fonte e a imagem juntas após cada alteração; a numeração, os fragmentos e as mensagens devem coincidir. A escala 2 permite ampliar a imagem para ler as referências durante a arguição.
