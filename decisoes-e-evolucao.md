# Decisões e evolução

## Princípios

1. **Não inventar.** Nenhuma regra de marca, texto comercial, condição de produto ou aviso é criado pela ferramenta. Só entra o que foi aprovado.
2. **A leitura automática nunca vale como conferência.** O PDF sugere; a pessoa confere.
3. **Na dúvida, perguntar.** Campo incerto fica em "Revisar", com opções, e nunca é preenchido por suposição.
4. **Nada é cortado.** Se não cabe, a ferramenta mostra o que excedeu e bloqueia a peça comercial. O rascunho continua disponível.
5. **Privacidade por padrão.** Nada sai do computador, nada fica gravado, e o nome do arquivo nunca leva dado do cliente.
6. **Registrar antes de mudar.** Cada versão começou com uma cópia da anterior conferida por hash, e cada decisão foi registrada com o motivo.

## Registro de decisões

Todas as decisões do projeto ficaram num registro único, com marcadores de situação:

| Marcador | Significado |
|---|---|
| Aprovada | Decisão tomada pelo responsável do projeto |
| Recomendação | Proposta ainda não aprovada |
| Pendente | Depende de outra área |
| Em aberto | Ainda não decidida |
| Contradição | Conflito entre regras, documentado antes de qualquer mudança |
| Planejado | Registrado para uma versão futura, não implementado |

Quando uma regra mudava, o texto anterior era mantido com uma nota, em vez de apagado.

## Evolução por versão

| Versão | O que entrou |
|---|---|
| 0.1 | Primeiro gerador: um formato, exportação PNG, rascunho com marca d'água, bloqueios da peça comercial |
| 0.2 | Correção: a interface quebrava quando o HTML era aberto sem as pastas. Virou **arquivo único**, com a peça desenhada em canvas |
| 0.3 | Layout em três zonas: marca, mensagem e fechamento |
| 0.4 | Escala tipográfica para leitura no celular, limite por campo e validação visível de espaço |
| 0.5 | Tipo de produto e imagem enviada pelo usuário, com editor de enquadramento |
| 0.6 | Dados do cliente, comunicação promocional × individualizada, regras para canais coletivos |
| 0.7 – 0.8 | Preparação para materiais oficiais e nova interface |
| 0.9 | **Importação de PDF** com senha e OCR, conferência humana, exportação em PDF |
| 0.9.1 | Leitura de PDF reescrita: sinônimos, tabelas, contexto, transparência por campo |
| 0.10 | Layout compacto, CPF/CNPJ num campo único, novos tipos de produto, sugestão de texto de apoio |
| 0.11 | Forma de pagamento, parcelas, tipo de documento, conferência de **todos** os campos, painel de conferência |
| 0.13 | Modelos de arte, duplicação de peças, histórico em memória, comparação entre peças |
| 0.14 | Formatos quadrado e stories, **editor de ajustes** com proteções |
| 0.14.1 | Aprovações finais e reorganização das etapas |
| 0.15 | Leitura de novos layouts de PDF (64/76 → 76/76), pendências agrupadas, novo nome de arquivo |

A versão 0.12 foi planejada e não foi implementada, por decisão do projeto.

## Algumas decisões que valeram a pena

- **Arquivo único:** resolveu de vez o problema de quem abria o HTML direto do `.zip` ou de um anexo.
- **A mesma função para prévia e exportação:** garante que o arquivo baixado é exatamente o que aparece na tela, e permite testar isso pixel a pixel.
- **Inventário único de campos:** um campo novo vindo de documentos só precisa entrar numa lista para herdar origem, conferência e painel.
- **Bloquear em vez de esconder:** quando o conteúdo não cabe, mostrar o problema é mais seguro do que reduzir a fonte até ficar ilegível.
- **Duas fontes nos testes:** pegou vários casos de texto que cabia em um ambiente e não cabia em outro.
