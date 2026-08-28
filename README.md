Conferência de Guias RET
Aplicação para conferir guias de IRRF, CSRF e INSS em PDF contra a planilha `Apuracao-retidos.xlsx`.
O que o programa faz
Lê todos os PDFs da pasta selecionada, inclusive em subpastas.
Os PDFs devem estar dentro das subpastas `IRRF`, `CSRF` e `INSS`.
Extrai de cada guia:
Agente;
Empresa;
Imposto;
Código de receita;
Período de apuração;
Valor da guia.
Lê a aba `Base Guias` da planilha `Apuracao-retidos.xlsx`.
Cruza os dados usando a chave:
Agente + Imposto + Empresa + Período de Apuração + Valor
Compara o valor do PDF com o valor da planilha.
Valida se o código identificado no PDF é permitido para o imposto.
Gera o arquivo `resultado_conferencia_retidos.xlsx` na pasta onde está a planilha de entrada.
Regras de resultado Situação | Status Final
Guia localizada, valor igual e código válido | `ok`
Guias múltiplas com soma igual e códigos válidos | `ok` — Informações corretas (múltiplas guias)
Guia localizada e valor diferente | `revisar` — Valor da guia divergente
Guia com código incompatível | `revisar`
Linha da planilha sem PDF correspondente | `revisar` — Apenas planilha
PDF sem linha correspondente na planilha | `revisar` — Apenas PDF
PDF com dados insuficientes para cruzamento | `revisar`
A conferência considera o valor correto somente quando a diferença é exatamente `0,00` e o código da guia é válido para o imposto identificado.
Códigos de receita aceitos
IRRF: `1708`, `3280` ou `8045`;
CSRF: `5952`;
INSS: `1162`.
Para INSS, o programa procura as linhas da composição da guia, verifica os códigos encontrados e soma os valores das linhas identificadas. Caso não consiga localizar as linhas, tenta utilizar o valor total do documento.
Normalizações aplicadas
Agente e empresa: remove acentos, espaços, hífens, pontos, barras e outros caracteres especiais; transforma letras em maiúsculas;
Símbolo `&`: tratado como a letra `E`; por exemplo, `W&W`, `W e W` e `WEW` são considerados equivalentes;
CNPJ: comparado somente pelos dígitos e exibido formatado como `00.000.000/0000-00`;
Imposto: aceita variações como `PCC`, `PIS COFINS CSLL` e `PIS/COFINS/CSLL` como `CSRF`;
Código de receita: utiliza o código de quatro dígitos identificado no PDF;
Período: normalizado internamente para `AAAA-MM`;
Valores: arredondados para duas casas decimais.
Utilização
Basta abrir o executável e selecionar:
A pasta raiz dos PDFs, que deve conter as subpastas `IRRF`, `CSRF` e `INSS`;
O arquivo Excel `Apuracao-retidos.xlsx` ou a pasta onde ele está localizado.
