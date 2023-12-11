# Especificação Funcional - Cadastro de Clientes ![logo](https://github.com/SMELGES-Infinit/Treinamento---Infinitfy-2023/assets/122098536/5946817a-099a-430f-899e-3e1fc20a8d95)

## Introdução

O processo de Cadastro de Clientes visa automatizar e facilitar o cadastro de novos clientes no sistema SAP, garantindo precisão nos dados e eficiência operacional. Para isso, serão desenvolvidos serviços OData (CDS Views) para busca de códigos de cidade e exibição de dados de clientes, além de interfaces para envio e processamento de arquivos contendo informações de clientes.

## Serviço OData (CDS View) ZCDS_{INICIAIS}_GETCITYC: Buscar código de cidade

### Objetivo
Criar um serviço OData (CDS View) para buscar o código da cidade na tabela "T005H", simplificando o cadastro de endereços dos clientes no SAP.

### Regra de Seleção
- SPRAS = E
- LAND1 = COUNTRY (JSON)
- REGIO = uf (ViaCEP)
- BEZEI = logradouro (ViaCEP, com limitação para 20 caracteres)

### Retorno
- CITYC

---

## Serviço OData (CDS View) ZCDS_{INICIAIS}_GETCUSTOMERS: Buscar dados de clientes

### Objetivo
Desenvolver um serviço OData (CDS View) para exibir dados de clientes (CUSTOMER) e seus endereços (ADDRESS).

---

## Interface {INICIAIS}_PostCustomerFile - Envio do JSON para o servidor FTP

### Objetivo
Implementar uma interface (IFlow) para enviar um JSON contendo dados de clientes ao servidor FTP, no formato .CSV, denominado "cadastro_clientes".

#### Diretório de Escrita
- /{NOME_CANDIDATO}/NAO_PROCESSADOS

---

## Interface {INICIAIS}_ProcessCustomerFile - Leitura do arquivo no servidor FTP e cadastro de clientes

### Objetivo
Criar uma interface para ler o arquivo .CSV "cadastro_clientes" no servidor FTP. Durante o processamento, consultar o Webservice ViaCEP usando o campo "CEP" para buscar detalhes do endereço.
- Em caso de CEP inválido, inserir o registro em um arquivo de log.
- Utilizar o serviço ZCDS_{INICIAIS}_GETCITYC para obter o código da cidade (CITYC).
- Chamar a RFC BAPI_CUSTOMER_CREATE para cadastrar o cliente.
- Registrar os resultados em um arquivo de log no formato .txt.

#### Diretório de Leitura
- /{NOME_CANDIDATO}/NAO_PROCESSADOS

#### Diretório de Movimentação
- /{NOME_CANDIDATO}/PROCESSADOS

#### Arquivo de Log
- Tipo: .txt
- Formato: Uma linha por registro com data/hora (yyyy-MM-dd'T'HH:mm:ss) e informação sobre o sucesso ou erro.
- Nome: {INICIAIS}_log_{timestamp}.txt

Exemplo:
2023-12-11'T'16:00:00 INFO Cliente cadastrado sob Nº 200
2023-12-11'T'16:01:00 ERROR Erro ao cadastrar cliente: CEP inválido

---

## Tabelas

### CUSTOMER
- NAME: Nome
- NAME2: Sobrenome
- CNPJ: Nº CNPJ
- CPF: Nº CPF

### ADDRESS
- TITLE: Tipo de endereço (TSAD3-TITLE)
- NAME: Descrição 1
- NAME2: Descrição 2
- HOUSE_NUM: Casa (número ou código)
- BUILDING: Prédio (número ou código)
- FLOOR: Andar (prédio)
- ROOMNUMBER: Número da sala (prédio)
- COUNTRY: País (T005-LAND1)
- LANG: Idioma (T002-SPRAS)
- TEL_NUMBER: Telefone
- CELL_NUMBER: Celular

### Webservice ViaCEP
- [ViaCEP Webservice](https://viacep.com.br/)
- Campos:
  - cep (ADDRES-POST_CODE)
  - logradouro (ADDRES-STREET)
  - complemento (ADDRES-COMPLEMENT)
  - localidade (ADDRES-CITY/T005H-CITYC)
  - bairro (ADDRES-NEIGHBORHOOD)
  - uf (ADDRES-REGION)
