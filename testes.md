# Testes

> O HTML da ferramenta, as suítes de teste, os scripts e os PDFs fictícios **não estão publicados** neste repositório. Este documento descreve a metodologia e os resultados.

## Como foi testado

- **Playwright com Chromium**, abrindo o HTML final por `file://`, com a rede desligada.
- **O arquivo testado é o mesmo arquivo entregue**, conferido por hash.
- **Duas fontes:** cada rodada completa foi feita com a fonte padrão do ambiente e com uma segunda fonte de medidas parecidas com a fonte oficial. Isso pega problemas de texto que cabe em um computador e não cabe em outro.
- **Uma fonte por vez:** as rodadas não rodam em paralelo, e cada suíte grava a saída completa. Uma suíte interrompida aparece como "INTERROMPIDA", e não como aprovada.

## Material de teste

- **PDFs fictícios** gerados por script, com o resultado esperado de cada campo em JSON. Eles simulam layouts de várias seguradoras e administradoras: senha, OCR, arquivo corrompido, tabelas, colunas lado a lado, dados contraditórios.
- **Documentos reais** foram usados só por hash e situação de cada campo, sem nenhum valor gravado em logs ou arquivos.
- **Varredura de privacidade:** antes da entrega, um script procurou palavras e números característicos dos documentos reais em todos os arquivos de teste e documentação. Nenhum dado pessoal foi encontrado.

## Tipos de verificação

| Área | O que é verificado |
|---|---|
| Importação | Senha, senha errada, OCR, corrompido, não PDF, sem dados, importação repetida |
| Identificação | Campo a campo, comparado com o resultado esperado |
| Conferência | Pendências, correções, confirmação geral, bloqueios |
| Modelos e formatos | Todas as combinações de modelo, formato, fundo e conteúdo |
| Editor | Mover, redimensionar, desfazer, travas, proteções |
| Exportação | Arquivo baixado comparado pixel a pixel com a prévia, dimensões, nome do arquivo |
| Privacidade | Nada gravado no navegador, nome do arquivo sem dados, canal coletivo sem dados |
| Offline | Nenhuma requisição externa em nenhuma suíte |

## Referência visual

Antes de cada mudança grande, as artes da versão anterior foram gravadas como referência. Depois da mudança, as novas foram comparadas pixel a pixel.

Na versão final: **936 artes** (3 formatos × 13 tipos de produto × 3 modelos × com e sem imagem × conteúdo típico e completo × 2 fundos), **936 idênticas** em cada fonte.

## Resultado da versão final

| Suíte | Aprovadas | Reprovadas |
|---|---|---|
| Suíte nova da versão final | 55 | 0 |
| 16 suítes das versões anteriores | 687 | 0 |
| **Total** | **742** | **0** |

Mais 4 verificações "não se aplica", as mesmas desde versões anteriores.

**Leitura de PDFs fictícios novos:** 76 de 76 campos conforme o esperado. A versão anterior acertava 64 de 76.

## Falhas encontradas durante o desenvolvimento

| Falha | Correção |
|---|---|
| CNPJ da seguradora na mesma linha do CPF do cliente gerava conflito | O documento na linha de "Seguradora:" passou a ser dessa parte |
| Dados do estipulante preenchiam a razão social do cliente | Estipulante virou seção própria e só aparece como sugestão |
| Colunas segurado/corretor lado a lado perdiam o segurado | Cabeçalhos lado a lado passaram a abrir uma seção por coluna |
| Datas com o mês escrito não eram lidas | Novo leitor de datas |
| Um teste supunha duas exportações no mesmo segundo | Relógio da página fixado no teste |
| Testes antigos esperavam o nome de arquivo anterior | Expectativa atualizada e **regressão completa refeita** |

## Como a regressão é executada (internamente)

1. Preparar Node e Playwright, com o HTML final numa pasta vazia e os PDFs fictícios.
2. Rodar a regressão com a fonte padrão e, depois, com a segunda fonte.
3. Rodar a comparação de pixels com a referência.
4. Rodar o medidor de leitura de PDF.
