Nome: data-envio-empenho
Atalho: /data-envio-empenho
Quando usar: Extrai data de envio, destinatário e assunto dos documentos de e-mail de processos SEI informados em CSV e grava a relação consolidada em um Despacho no primeiro processo válido da lista.

### 1. Ler o CSV

- Identificar a primeira coluna como fonte dos números de processos.
- Ignorar cabeçalho, linhas vazias e valores sem número de processo.
- Preservar a ordem original dos processos.
- Não utilizar processos_pesquisar ou processo_consultar.
- Registrar a quantidade total de processos válidos.
- Identificar o primeiro processo válido: será o destino do Despacho.

### 2. Localizar os e-mails

Percorrer TODOS os processos válidos do CSV, individualmente, executando:

documentos_listar(
  processo: número do processo,
  filtro: "E-mail"
)

- Não interromper a consulta após encontrar documentos no primeiro processo.
- Considerar todos os documentos retornados cujo título contenha "E-mail".
- Registrar o número SEI de cada documento e associá-lo ao processo correspondente.
- Preservar a ordem dos documentos retornados.
- Não utilizar documentos de outros tipos.
- Ao terminar, reunir todos os documentos encontrados em uma única relação.
- Registrar a quantidade total de e-mails encontrados.

### 3. Ler os documentos

- Ler TODOS os documentos encontrados, inclusive os de processos diferentes.
- Reunir os números SEI dos documentos e seus respectivos processos.
- Executar documento_ler em lotes de até 10 documentos.
- Utilizar tamanho: 500.
- Processar todos os lotes até concluir a leitura de todos os documentos.
- Não solicitar continuação da leitura.
- Não analisar o corpo das mensagens.
- Não ler anexos.
- Registrar individualmente o resultado da leitura de cada documento.
- Não descartar resultados de lotes anteriores ao processar os seguintes.
- Identificar documentos cuja leitura falhou.

### 4. Extrair os campos

De cada documento lido com sucesso, extrair exclusivamente:

- Data de Envio
- Para (incluindo todos os emails)
- Assunto

Utilizar os rótulos presentes no conteúdo do documento.

Regras:
- Preservar integralmente a data e hora.
- Preservar todos os destinatários apresentados no campo Para.
- Preservar o assunto sem reformulação.
- Não confundir De com Para.
- Não extrair informações do corpo da mensagem.
- Não inferir campos ausentes.
- Se um campo não estiver disponível no trecho retornado, preencher com "Não identificado".

O número do processo deve ser associado ao documento conforme o mapeamento realizado na etapa 2.

O número do documento deve ser o número SEI retornado pela ferramenta, não o ID interno da árvore.

### 5. Consolidar

Manter um único acumulador de registros durante toda a execução.

Cada documento de e-mail encontrado deve produzir exatamente um registro, contendo:

Processo;Documento;Data de Envio;Para;Assunto

Regras:
- Acrescentar ao acumulador os registros de todos os processos e de todos os lotes.
- Não substituir os registros anteriores ao processar novos documentos.
- Não criar uma tabela separada por processo.
- Não agrupar diferentes e-mails em uma única linha.
- Produzir exatamente uma linha por documento de e-mail encontrado, inclusive quando houver vários no mesmo processo.
- Manter a ordem dos processos do CSV original.
- Dentro de cada processo, manter a ordem dos documentos retornados.
- Escapar corretamente caracteres especiais, aspas e delimitadores.
- Não incluir o corpo dos e-mails.

Antes de avançar:
- Conferir o total de documentos encontrados.
- Conferir o total de registros acumulados.
- Conferir a correspondência entre cada registro e seu processo.
- Conferir a correspondência entre cada registro e seu número SEI.
- Confirmar que a quantidade de registros corresponde à quantidade de documentos encontrados.

Se houver divergência, corrigir a consolidação antes de criar o documento.

### 6. Gravar no SEI

Destino:
- Primeiro processo válido do CSV.
- Tipo de documento: "Despacho".
- Descrição: "Consolidação de metadados de e-mails".

Regras:
- Não consultar sei_opcoes para identificar tipos de documento.
- Não pesquisar modelos de Despacho.
- Não utilizar documento_modelo nem texto_padrao.
- Criar diretamente com documento_criar, informando tipo: "Despacho". Não procurar modelos similares.
- Criar um único documento interno no primeiro processo válido do CSV.
- Inserir a tabela consolidada completa em conteudo_html.
- Utilizar HTML válido e simples, com tabela, cabeçalho e uma linha por documento.
- Incluir todos os registros acumulados, não apenas os do primeiro processo ou do primeiro lote.
- Não dividir a relação em vários documentos.
- Não substituir os pseudônimos pelos valores reais manualmente.
- Preservar os rótulos de pseudonimização no HTML enviado à ferramenta: o motor deve realizar a reidratação durante a execução da escrita.

Antes de criar:

- Consultar a situação do primeiro processo, utilizando os dados disponíveis nas ferramentas do SEI.
- Verificar se o processo está sobrestado.
- Se estiver sobrestado, interromper a execução e informar ao usuário que é necessário retirar o sobrestamento do processo de destino.
- Aguardar a confirmação expressa do usuário de que retirou o sobrestamento.
- Após a confirmação, consultar novamente a situação do processo.
- Prosseguir somente se o processo não estiver mais sobrestado.
- Não tentar retirar o sobrestamento automaticamente.
- Não criar o documento em outro processo como alternativa.

Execução da criação:

- Aguardar a aprovação da operação de escrita, quando solicitada.
- Conferir o retorno de documento_criar e o número SEI do documento criado.
- Verificar se a criação e a gravação do conteúdo foram concluídas com sucesso.
- Considerar que a criação do documento e a edição do conteúdo são operações distintas.
- Se a criação ocorrer, mas a edição do conteúdo falhar, informar a falha e não declarar a consolidação como concluída.
- Não criar outro documento automaticamente para substituir uma operação parcialmente concluída.

### 7. Conferência

Antes de concluir:
- Verificar se todos os processos válidos do CSV foram consultados.
- Contabilizar processos consultados.
- Contabilizar documentos de e-mail encontrados.
- Contabilizar documentos lidos com sucesso.
- Identificar documentos com falha de leitura.
- Identificar documentos com campos não encontrados.
- Conferir se a quantidade de registros consolidados corresponde à quantidade de documentos de e-mail encontrados.
- Conferir se todos os processos e lotes contribuíram para a consolidação.
- Conferir se o documento foi criado no primeiro processo válido do CSV.
- Conferir se o tipo criado foi Despacho.
- Conferir se a gravação do conteúdo foi concluída.
- Não apresentar resultados inventados ou inferidos.
- Não declarar sucesso se houver falha na gravação.

### Saída na conversa

Apresentar somente:

1. Número do processo em que o relatório foi criado.
2. Quantidade de processos consultados.
3. Quantidade de e-mails encontrados.
