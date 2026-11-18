Nome: cancelar empenho
<br>Atalho:/cancelar-empenho
<br>Quando usar: Use quando o usuário pedir despacho de cancelamento de empenho por inexecução contratual a partir de um CSV anexado com processos, empenhos, fornecedores e documentos SEI.

# Despacho: Cancelamento de empenho por inexecução contratual

## Objetivo
Ler o arquivo CSV anexado à conversa e gerar, para cada processo listado,
um despacho usando o modelo padrão abaixo, substituindo as variáveis
pelos valores das colunas correspondentes.

## Dados fixos (não consultar, não perguntar)
- Tipo de documento: "Despacho"
- Nome do arquivo / título do documento: "CANCELAMENTO EMPENHO",
- Siga a skill redacao-oficial.
- Leia o CSV uma única vez e processe todas as linhas em sequência.

## Passo a passo

1. Abra o CSV anexado à conversa.
   - Detecte o separador (vírgula ou ponto e vírgula) e a codificação
     (UTF-8 ou Latin-1).
   - Ignore espaços extras e linhas vazias.

2. Localize as colunas (sem diferenciar maiúsculas/minúsculas e acentos):
   - PROCESSO       → número do processo (identifica cada despacho)
   - EMPENHO        → substitui ##empenho##
   - BENEFICIÁRIO   → substitui ##fornecedor##
   - documentoSei   → substitui ##documentoSei##

3. Se alguma coluna obrigatória não existir, PARE e informe ao usuário
   quais colunas faltam e quais foram encontradas. Não invente valores.

4. Para CADA linha do CSV (cada valor da coluna PROCESSO):
   - Gere um despacho com o modelo abaixo.
   - Substitua TODAS as ocorrências de cada variável
     (##empenho## aparece duas vezes).
   - Preserve os valores exatamente como estão no CSV
     (não reformate números de empenho nem de documento SEI).
   - Se algum campo da linha estiver vazio, não gere o despacho dessa
     linha e liste-a ao final como pendente.

5. Formato da saída:
   - Antes de cada despacho, escreva uma linha:
     `Processo: <valor de PROCESSO> | Tipo: Despacho | Arquivo: CANCELAMENTO EMPENHO`
   - Separe os despachos com uma linha `---`.
   - Ao final, informe: total de despachos gerados e lista de linhas
     puladas, com o motivo.

6. Só gere arquivo (.docx ou .csv) se o usuário pedir.

## Modelo de texto (não altere a redação)

A ASSISTENTE EM ADMINISTRAÇÃO DA UNIVERSIDADE FEDERAL FLUMINENSE, no uso de
suas atribuições legais e regulamentares, examinando os autos do Processo em
epígrafe, encaminha processo ao DCO/CAF, para cancelamento do empenho
##empenho## e análise de possível abertura de processo de Apuração de
irregularidade em contratos e licitações frente ao fornecedor ##fornecedor##,
em cumprimento ao Art. 155 da Lei 14133/2021 em atendimento ao princípio da
legalidade.

Motivo: inexecução do contrato e não execução dos serviços empenhados em
##empenho##.

Anexo de cobranças no documento "##documentoSei##".

## Regras
- Não altere a redação do modelo, apenas substitua as variáveis.
- Não omita processos do CSV.
- Não invente dados ausentes.
