# Arquitetura

> Os arquivos e pastas citados aqui (`app.js`, `fonte/` etc.) fazem parte do código interno da ferramenta, que **não está publicado** neste repositório. Eles aparecem para mostrar como o projeto foi organizado.

## Restrições do projeto

| Restrição | Como foi atendida |
|---|---|
| Arquivo único | Um script Python junta CSS, scripts, leitor de PDF, OCR e materiais em um só HTML |
| Offline | Nenhuma requisição externa (verificado em todos os testes, com a rede desligada) |
| Aberto direto no Chrome | Funciona por `file://`. Por isso, só scripts comuns: o Chrome bloqueia módulos JavaScript e `fetch` de arquivos locais |
| Sem servidor, API ou banco | Todo o processamento acontece na página |
| Sem armazenamento permanente | Documento, senha, dados e histórico ficam só na memória. Fechou a página, sumiu |
| Sem IA durante o uso | A ferramenta não depende de nenhum serviço de IA para funcionar |

## Fluxo de dados

```
PDF ──importacao.js──▶ linhas de texto (com posição) ──extracao.js──▶ resultado por campo
       (pdf.js; OCR para                              (valor, situação, origem,
        página digitalizada)                           confiança, trecho, opções)
                                                              │
                                                     conferencia.js
                                                     (conferência campo a campo
                                                      e confirmação geral)
                                                              │
formulário (app.js) ──estado──▶ renderizador.js ──▶ canvas da pré-visualização
                                     │
                                     └──▶ exportar.js: PNG / PDF (mesmo desenho)
                                                       + marca d'água no rascunho
validacao.js ◀── estado + layout ──▶ "O que falta" e liberação da peça comercial
```

## Módulos

| Arquivo | Responsabilidade |
|---|---|
| `tokens.js` | Cores e tipografia, geradas a partir de um arquivo de design tokens. Não é editado à mão |
| `formatos.js` | Catálogo de formatos: dimensões, margens e áreas seguras |
| `modelos.js` | Tipos de produto, canais, chamadas para ação e fundos |
| `modelos-arte.js` | Modelos de arte, situação de cada um e sugestão por tipo de produto |
| `dados.js` | Campos do cliente, da contratação e do pagamento: máscaras e validação (CPF, CNPJ alfanumérico, datas, telefone) |
| `importacao.js` | Leitura do PDF, senha, OCR e detecção de importação repetida (hash) |
| `extracao.js` | Identificação dos campos no texto |
| `conferencia.js` | Lista única dos campos que vêm de documentos, origem de cada valor e conferência |
| `renderizador.js` | Desenho da peça: layout, compactação, formatos, ajustes manuais e verificações geométricas |
| `validacao.js` | Regras que liberam ou bloqueiam a peça comercial |
| `sessao.js` | Histórico, duplicação e comparação entre peças, tudo em memória |
| `exportar.js` | PNG, PDF, marca d'água e nome do arquivo |
| `app.js` | Interface e eventos |

## Leitura de PDF

**Princípio:** só preenche o que está escrito no documento, com evidência suficiente. Na dúvida, "Revisar". Sem evidência, "Não identificado".

1. **Linhas com posição:** cada trecho guarda a posição horizontal. Cabeçalhos e rodapés repetidos em várias páginas são ignorados para dados do cliente.
2. **Seções:** blocos como "Corretor", "Pagador", "Beneficiário", "Estipulante" ou "Credor fiduciário" nunca alimentam os dados do cliente. Cabeçalhos lado a lado abrem uma seção por coluna.
3. **Rótulos:** listas de sinônimos normalizados (sem acento, minúsculas), com força *explícita* ou *genérica*. Qualificadores como "do corretor" ou "anterior" tiram o rótulo do cliente ou da apólice atual.
4. **Valor:** na mesma linha, no trecho seguinte ou na linha de baixo (tabelas).
5. **Decisão:** rótulo forte prevalece. Valores diferentes viram opções. OCR ou rótulo genérico levam a "Revisar".
6. **Regras específicas:**
   - CPF e CNPJ num campo único, com detecção do tipo;
   - datas em DD/MM/AAAA e com o mês escrito ("1º de junho de 2026");
   - data de emissão nunca vira início de vigência;
   - valores em reais sempre como opções a escolher;
   - forma de pagamento só com modalidade reconhecida; dados bancários são recusados.
7. **Transparência:** cada campo mostra página, rótulo, método (texto ou OCR), confiança e o trecho lido.

## Conferência

- Todo campo que veio do PDF começa como "Aguardando conferência".
- Alterar o valor desfaz a conferência e muda a origem para "Corrigido após extração".
- "Voltar ao valor do PDF" restaura o valor lido.
- A confirmação geral só fica disponível sem pendências.
- Um painel mostra identificados, conferidos, pendentes, não identificados e corrigidos, com link para cada campo.

## Desenho da peça

- **Layout em zonas:** cabeçalho, imagem, título, apoio, dados, chamada, contato, texto legal e faixa final.
- **Compactação:** quatro níveis, do padrão ao máximo, sempre respeitando um tamanho mínimo de leitura. O que não cabe é marcado e bloqueia a peça. Nada é cortado.
- **Formatos:** cada um tem parâmetros próprios, em vez de redimensionar a arte.
- **Ajustes manuais:** guardados por formato e por elemento e aplicados depois do layout automático. O editor confere sobreposição, texto sobre imagem, área e hierarquia.
- **Uma função para tudo:** a mesma função desenha a prévia, o PNG e o PDF. Por isso o arquivo exportado é idêntico ao que aparece na tela.

## Exportação

- **PNG:** `canvas.toBlob` nas dimensões do formato.
- **PDF:** escrito à mão, PDF 1.4 de uma página, com a imagem RGB comprimida sem perda.
- **Rascunho:** marca d'água desenhada na própria imagem.
- **Nome do arquivo:** `[RASCUNHO-]<formato>-<tipo de produto>-AAAAMMDD-HHMMSS.png|pdf`, sem nenhum dado do cliente. Exportações no mesmo segundo recebem `-2`, `-3`.
- **Clique duplo:** um clique repetido durante a geração é ignorado.

## Montagem (processo interno)

1. Editar os arquivos em `fonte/`.
2. Rodar o script que incorpora os materiais (logotipos, avisos), se mudaram.
3. Rodar o script que monta o arquivo único.
4. Rodar a regressão completa no HTML final.
