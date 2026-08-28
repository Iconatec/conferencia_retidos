# Conferência de Guias Retidos

Aplicação para conferir guias de IRRF, CSRF e INSS em PDF contra a planilha `Apuracao-retidos.xlsx`.

## O que o programa faz

1. Lê todos os PDFs da pasta selecionada, inclusive em subpastas.
   - Os PDFs devem estar dentro das subpastas `IRRF`, `CSRF` e `INSS`.
2. Extrai de cada guia:
   - Agente
   - Empresa
   - Imposto
   - Código de receita
   - Período de apuração
   - Valor da guia
3. Lê a aba `Base Guias` da planilha `Apuracao-retidos.xlsx`.
4. Cruza os dados usando a chave:

Agente + Imposto + Empresa + Período de Apuração + Valor

5. Compara o valor do PDF com o valor da planilha.
6. Valida se o código identificado no PDF é permitido para o imposto.
7. Gera o arquivo `resultado_conferencia_retidos.xlsx` na pasta onde está a planilha de entrada.

## Regras de resultado

| Situação | Status Final |
- Guia localizada, valor igual e código válido | `ok` |
- Guias múltiplas com soma igual e códigos válidos | `ok` — Informações corretas (múltiplas guias) |
- Guia localizada e valor diferente | `revisar` — Valor da guia divergente |
- Guia com código incompatível | `revisar` |
- Linha da planilha sem PDF correspondente | `revisar` — Apenas planilha |
- PDF sem linha correspondente na planilha | `revisar` — Apenas PDF |
- PDF com dados insuficientes para cruzamento | `revisar` |

A conferência considera o valor correto somente quando a diferença é exatamente `0,00` e o código da guia é válido para o imposto identificado.

## Códigos de receita aceitos

| Imposto | Códigos aceitos |
- IRRF | `1708`, `3280` ou `8045` |
- CSRF | `5952` |
- INSS | `1162` |

Para INSS, o programa procura as linhas da composição da guia, verifica os códigos encontrados e soma os valores das linhas identificadas. Caso não consiga localizar as linhas, tenta utilizar o valor total do documento.

## Normalizações aplicadas

- Agente e empresa: remove acentos, espaços, hífens, pontos, barras e outros caracteres especiais; transforma letras em maiúsculas
- Símbolo `&`: tratado como a letra `E`; por exemplo, `W&W`, `W e W` e `WEW` são considerados equivalentes
- CNPJ: comparado somente pelos dígitos e exibido formatado como `00.000.000/0000-00`
- Imposto: aceita variações como `PCC`, `PIS COFINS CSLL` e `PIS/COFINS/CSLL` como `CSRF`
- Código de receita: utiliza o código de quatro dígitos identificado no PDF
- Período: normalizado internamente para `AAAA-MM`
- Valores: arredondados para duas casas decimais

## Estrutura das pastas

A pasta raiz selecionada deve conter as subpastas abaixo:
Pasta das guias/
```text
Pasta das guias/
├── IRRF/
│   └── arquivos PDF
├── CSRF/
│   └── arquivos PDF
└── INSS/
    └── arquivos PDF
```

Os nomes dos PDFs devem seguir o padrão:
agente - imposto - empresa - periodo.pdf

Exemplo:
5 - IRRF - W&W PARTICIPAÇÕES S.A. - 07.2026.pdf

## Planilha de entrada

O arquivo Excel deve possuir uma aba chamada `Base Guias` e conter as seguintes colunas:

| Coluna | Descrição |

- `Agente` | Identificador utilizado na chave de cruzamento |
- `CNPJ` | CNPJ da empresa, exibido no resultado |
- `Valor Guia` | Valor esperado da guia |
- `Imposto` | IRRF, CSRF, INSS ou uma variação reconhecida |
- `Periodo` ou `Período` | Período de apuração da guia |
- `Empresa` | Nome da empresa utilizado na chave de cruzamento |

## Arquivo de saída

O resultado é salvo como:
resultado_conferencia_retidos.xlsx
O arquivo é criado na mesma pasta da planilha Excel de entrada e contém as colunas:

Agente
CNPJ
Valor Guia
Imposto
Periodo
Empresa
Valor PDF
Diferenca
Codigo PDF
Status Final
Situacao

Na coluna `Status Final`:
- `ok` recebe preenchimento verde
- `revisar` recebe preenchimento vermelho

## Utilização

1. Abra o executável.
2. Selecione a pasta raiz dos PDFs.
3. Confirme que a pasta contém as subpastas `IRRF`, `CSRF` e `INSS`.
4. Selecione o arquivo Excel `Apuracao-retidos.xlsx`, ou informe a pasta onde ele está localizado quando utilizar a versão em Python.
5. Clique em **Conferir guias**.
6. Ao final do processamento, abra o arquivo `resultado_conferencia_retidos.xlsx`.

