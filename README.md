<p align="center">
  <img src="docs/img/gajeiro.png" alt="Gajeiro" width="180">
</p>

# gajeiro

Supervisão industrial baseada em Kubernetes.

O gajeiro era o marinheiro que subia ao cesto da gávea, no alto do mastro das naus e caravelas portuguesas, para vigiar o horizonte e avisar o comando do que via. É o papel da camada supervisória: ficar acima da operação, observar e informar quem decide.

Este repositório reúne os documentos da pesquisa de Matheus Corteletti Delfino sobre supervisão declarativa de plantas industriais com Kubernetes: o TCC (Engenharia de Controle e Automação, Ifes campus Serra, defendido em 29/10/2026), que usou o Processo Tennessee Eastman (TEP) como planta, os artigos derivados e o plano do mestrado. O código fica nos repositórios listados em [Projetos](#projetos).

## A ideia

A pergunta de fundo é o que uma implementação precisa demonstrar para que se possa dizer que uma planta está sob supervisão industrial baseada em Kubernetes. A resposta pretendida é uma especificação de arquitetura e de requisitos: quais componentes devem existir, quais recursos do Kubernetes representam a planta e sua política de operação, como os componentes conversam e que comportamentos precisam ser verificados. O Kubernetes é a base que coordena; a especificação diz o que conta como supervisão.

A especificação se organiza em capacidades verificáveis. Uma implementação pode atender só parte delas, e o que ela demonstrou fica registrado.

| Capacidade | O que precisa demonstrar | Situação |
| --- | --- | --- |
| Observar a operação | O supervisor recebe dados suficientes e atualizados para comparar a planta com a política declarada. | Demonstrada no TEP, no TCC, com um historiador entre a planta e o Kubernetes. |
| Avaliar e executar mudanças | Uma alteração na declaração inicia um procedimento: preparar o gêmeo digital, testar a mudança, avaliar critérios, autorizar ou recusar, aplicar e verificar a resposta da planta, inclusive quando o resultado é inconclusivo ou algo falha. | Proposta, no [plano do mestrado](plano-pos/). Hoje o supervisor só observa. |
| Gerir dispositivos e versões | Manter o inventário dos dispositivos, as versões de software e configuração e o histórico das alterações, separando o estado declarado do encontrado. | Proposta. |
| Integrar por OPC UA | Definir como os equipamentos expõem dados e comandos, com identificação, permissões e tratamento de falhas de comunicação. | Leitura demonstrada no TEP. Escrita, permissões e falhas de comunicação ainda não foram avaliadas. |

## Projetos

Os repositórios de código ficam na organização [Green-Cinnamon-Labs](https://github.com/Green-Cinnamon-Labs). A entrada é o `spec-tennessee-eastman`.

| Repositório | Papel |
| --- | --- |
| [`spec-tennessee-eastman`](https://github.com/Green-Cinnamon-Labs/spec-tennessee-eastman) | Arquitetura, experimentos e coordenação entre os repositórios do laboratório do TEP. |
| [`tep-lab`](https://github.com/Green-Cinnamon-Labs/tep-lab) | Como rodar o laboratório numa máquina só e o registro do que foi rodado. |
| [`monjolo`](https://github.com/Green-Cinnamon-Labs/monjolo) | Runtime genérico de simulação dinâmica em Rust, com adaptador OPC UA. Não sabe nada do TEP. |
| [`tep-plant`](https://github.com/Green-Cinnamon-Labs/tep-plant) | A planta TEP (Downs e Vogel, 1993) em Rust, sobre o `monjolo`, publicando por OPC UA. |
| [`tep-historian`](https://github.com/Green-Cinnamon-Labs/tep-historian) | Historiador em Python: coleta os sinais OPC UA e serve estatísticas de janela por HTTP. |
| [`plant-supervisor`](https://github.com/Green-Cinnamon-Labs/plant-supervisor) | O supervisor: controlador do Kubernetes em Go que avalia a planta contra a política declarada e grava o veredito. Não escreve na planta. |
| [`tep-ihm`](https://github.com/Green-Cinnamon-Labs/tep-ihm) | IHM web com o estado da planta e as decisões do supervisor. |
| [`tep-optimizer`](https://github.com/Green-Cinnamon-Labs/tep-optimizer) | Otimizador de regime permanente do custo de operação (Ricker, 1995), que chama o modelo da planta como função. |

## Documentos

| Pasta | O que é | Arquivo de entrada |
| --- | --- | --- |
| [`tcc-monografia/`](tcc-monografia/) | A monografia do TCC (ABNT, classe `ifes8`). Capítulos e apêndices em `trabalho/`, figuras em `imagens/`. O [README](tcc-monografia/README.md) traz o mapa da monografia e o inventário de referências. | `main.tex` |
| [`tcc-slide/`](tcc-slide/) | Slides da defesa (Beamer, modelo institucional do Ifes), roteiro da fala e transcrição da defesa. | `main.tex`, [`roteiro.md`](tcc-slide/roteiro.md) |
| [`paper-arxiv/`](paper-arxiv/) | Artigo derivado da monografia, no estilo arXiv. Ainda é o esqueleto do modelo. Ver o [README](paper-arxiv/README.md). | `main.tex` |
| [`paper-ieee/`](paper-ieee/) | Artigo WiP para o ICIT 2027 (até 4 páginas, submissão em 3/11/2026): a especificação em capacidades e a primeira implementação, no TEP. Ver o [README](paper-ieee/README.md). | `main.tex` |
| [`plano-pos/`](plano-pos/) | Plano de pesquisa do mestrado: Kubernetes na supervisão de uma planta industrial. | `main.tex` |

## Material de apoio

- [`referencial/`](referencial/): vault Obsidian com as notas de fonte (`notes/`). Os PDFs (`articles/`, `books/`, `standard/` e `new/`, com os que ainda não têm nota) são de terceiros e ficam no repositório privado [`gajeiro-referencial`](https://github.com/Green-Cinnamon-Labs/gajeiro-referencial); aqui essas pastas são ignoradas pelo Git.
  - [`references.bib`](referencial/references.bib): bibliografia única, usada por todos os documentos (`\bibliography{../referencial/references}`).
  - `bibTex/`: o BibTeX de cada fonte, um arquivo por fonte.
- [`docs/`](docs/):
  - **Modelos:** [`templates/`](docs/templates/), só os usados: LaTeX (Ifes, arXiv, IEEE) e o modelo de slides do Ifes. Ver o [README](docs/templates/README.md).
  - **Ambiente:** [`doc_setup-latex-monografia.md`](docs/doc_setup-latex-monografia.md) (instalação e compilação) e [`doc_obsidian_setup.md`](docs/doc_obsidian_setup.md) (plugins do vault).
  - **Normas resumidas:** [`IEC_62264.md`](docs/IEC_62264.md), [`IEC_62541.md`](docs/IEC_62541.md), [`IEC_63303.md`](docs/IEC_63303.md).
  - **Narrativa da monografia:** [`br_narrativa.md`](docs/br_narrativa.md) e [`br_narrativa_08_10_26.md`](docs/br_narrativa_08_10_26.md).
  - **Leitura de fontes:** os prompts `doc_prompt_*.md` e as respostas em [`respostas/`](docs/respostas/).
  - **Ícone do projeto:** [`img/gajeiro.png`](docs/img/gajeiro.png).
  - **Diagramas:** [`miscellanea.drawio`](docs/miscellanea.drawio). O mapa geral da monografia está no [README da monografia](tcc-monografia/README.md#big-picture).
  - **Planta Festo do Ifes:** os manuais e exercícios, para o mestrado, ficam no [`gajeiro-referencial`](https://github.com/Green-Cinnamon-Labs/gajeiro-referencial).

## Como compilar

Cada documento LaTeX tem o próprio `main.tex` e compila sozinho. Em cada pasta de documento:

```
latexmk -pdf main.tex
```

Os PDFs e arquivos auxiliares não são versionados (ver `.gitignore`). Detalhes do ambiente em [`docs/doc_setup-latex-monografia.md`](docs/doc_setup-latex-monografia.md).

## Licença

Todos os direitos reservados a Matheus Corteletti Delfino e à Green-Cinnamon-Labs. O repositório é público para leitura, mas o reuso depende de autorização. Documentos com coautoria e material de terceiros têm outros titulares. Ver [`LICENSE`](LICENSE).
