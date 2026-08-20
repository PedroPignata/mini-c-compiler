# Relatório — Análise do Mini C Compiler

## 1. Identificação do estudante

**Nome:** Pedro Rafael Santos Pignata  
**Disciplina:** Compiladores  
**Instituição:** IDP  

---

## 2. Identificação do projeto original

O projeto analisado nesta atividade é o **Mini C Compiler**, desenvolvido em linguagem C com finalidade educacional.

O código-base utilizado foi obtido a partir do projeto original:

**Projeto original:** `ironrinox/mini-c-compiler`

Para a realização da atividade, o projeto foi clonado e posteriormente publicado em um repositório individual, no qual estão sendo adicionados os testes, documentação e demais alterações realizadas durante o trabalho.

**Repositório individual:** `PedroPignata/mini-c-compiler`

Os créditos e a licença do projeto original foram preservados.

---

## 3. Objetivo da atividade

O objetivo da atividade é compreender o funcionamento do Mini C Compiler, acompanhando o caminho percorrido por um programa desde a leitura do arquivo-fonte até sua execução.

O fluxo principal observado no projeto é:

```text
Arquivo-fonte
     ↓
Leitura do arquivo
     ↓
Lexer
     ↓
Tokens
     ↓
Parser
     ↓
AST
     ↓
Interpretador
     ↓
Resultado
```

Além da análise do código existente, foram realizados testes válidos e inválidos para compreender o funcionamento do lexer, parser e interpretador, bem como identificar limitações existentes no projeto.

---

# 4. Preparação do ambiente

A atividade foi desenvolvida em ambiente Linux, utilizando o terminal e o Visual Studio Code.

Foram utilizados:

- Git;
- GCC;
- terminal Linux;
- Visual Studio Code;
- GitHub;
- GitHub CLI (`gh`).

O projeto original foi inicialmente clonado através do Git.

Posteriormente, foi criado um repositório individual no GitHub para armazenar o desenvolvimento da atividade.

Foram configurados dois remotes:

```text
origin   → repositório individual
upstream → repositório original
```

Dessa forma, `origin` aponta para o repositório individual e `upstream` mantém a referência para o projeto original.

---

# 5. Procedimento de compilação e execução

A compilação foi realizada na raiz do projeto utilizando:

```bash
gcc src/main.c src/lexer.c src/parser.c src/interpreter.c src/utils.c -Iinclude -o mini-c
```

Esse comando compila os arquivos-fonte presentes em `src/`, utiliza os headers presentes em `include/` e gera o executável `mini-c`.

A execução pode ser realizada informando um arquivo contendo o programa a ser analisado:

```bash
./mini-c examples/test.txt
```

Durante os testes também foram utilizados arquivos próprios:

```bash
./mini-c testes/01_lexico_valido.txt
```

O programa apresenta durante sua execução:

1. código-fonte lido;
2. tokens reconhecidos;
3. AST construída;
4. resultado da interpretação.

O executável `mini-c` foi adicionado ao `.gitignore`, pois é um arquivo gerado pela compilação e não precisa ser versionado.

---

# 6. Arquitetura e responsabilidades dos arquivos

## `src/main.c`

Ponto de entrada do programa. Coordena todo o fluxo principal:

```text
read_file()
→ lex()
→ parse()
→ interpret()
```

## `src/utils.c`

Implementa `read_file()`, responsável pela abertura e leitura do arquivo-fonte.

## `include/lexer.h`

Define os tipos de tokens, a estrutura `Token` e a estrutura `TokenList`.

## `src/lexer.c`

Implementa o analisador léxico, transformando os caracteres do código-fonte em tokens.

Principais funções:

- `create_token()`
- `lex()`
- `print_tokens()`

## `include/parser.h`

Define os tipos de nós da AST e a estrutura `ASTNode`.

## `src/parser.c`

Implementa o analisador sintático e a construção da AST.

Principais funções:

- `create_node()`
- `parse()`
- `parse_statement()`
- `parse_expression()`
- `print_ast()`

## `include/interpreter.h`

Define a estrutura da tabela de símbolos e as funções utilizadas pelo interpretador.

## `src/interpreter.c`

Executa a AST e gerencia as variáveis.

Principais funções:

- `init_symbol_table()`
- `lookup_symbol()`
- `set_symbol()`
- `eval_expression()`
- `exec_statement()`
- `interpret()`

## `examples/test.txt`

Arquivo utilizado pelo projeto para demonstrar a execução de um programa.

## `testes/`

Diretório utilizado durante a atividade para armazenar os casos de teste válidos, inválidos e os testes de precedência e associatividade.

---

# 7. Análise do ponto de entrada

O ponto de entrada do programa está em:

```c
int main(int argc, char* argv[])
```

O `main()` recebe os argumentos fornecidos através do terminal e coordena as diferentes etapas do programa.

Um trecho importante é:

```c
char* source_code = read_file(argv[1]);

TokenList tokens = lex(source_code);

ASTNode* ast = parse(&tokens);

interpret(ast);
```

Esse trecho representa praticamente todo o pipeline principal do projeto.

## 7.1 Como o caminho do arquivo-fonte é recebido?

O caminho é recebido através dos argumentos da linha de comando.

`argv[1]` contém o primeiro argumento fornecido depois do nome do executável.

Por exemplo:

```bash
./mini-c testes/teste1.txt
```

Nesse caso:

```text
argv[0] = ./mini-c
argv[1] = testes/teste1.txt
```

---

## 7.2 O que acontece quando nenhum arquivo é informado?

O programa verifica:

```c
if (argc < 2)
```

Caso nenhum caminho seja informado, uma mensagem de erro e um exemplo de utilização são apresentados.

Em seguida, o programa retorna:

```c
EXIT_FAILURE
```

e sua execução é encerrada.

---

## 7.3 Qual função lê o arquivo?

A função:

```c
read_file()
```

implementada em `src/utils.c`.

---

## 7.4 Qual função realiza a análise léxica?

A função:

```c
lex()
```

implementada em `src/lexer.c`.

---

## 7.5 Qual função constrói a AST?

A função:

```c
parse()
```

implementada em `src/parser.c`.

---

## 7.6 Qual função executa a AST?

A função:

```c
interpret()
```

implementada em `src/interpreter.c`.

---

## 7.7 Em que ordem essas funções são chamadas?

A sequência principal é:

```text
main()
  ↓
read_file()
  ↓
lex()
  ↓
parse()
  ↓
interpret()
```

Portanto, primeiro o programa lê o código-fonte, depois realiza a análise léxica, constrói a AST e finalmente interpreta essa árvore.

---

# 8. Análise da leitura do arquivo

A leitura é realizada pela função:

```c
read_file()
```

presente em `src/utils.c`.

Seu funcionamento pode ser resumido em:

```text
fopen()
  ↓
fseek() / ftell()
  ↓
malloc()
  ↓
fread()
  ↓
adiciona '\0'
  ↓
fclose()
  ↓
return buffer
```

## 8.1 Por que é necessário reservar memória?

Antes da leitura, o programa descobre o tamanho do arquivo.

Depois utiliza:

```c
malloc(length + 1)
```

para reservar memória suficiente para armazenar todo o conteúdo do arquivo mais o caractere final `\0`.

Essa memória precisa ser alocada dinamicamente porque o tamanho do arquivo só é conhecido durante a execução.

---

## 8.2 Qual é o papel do caractere `\0`?

Em C, uma string é representada por uma sequência de caracteres terminada por:

```c
'\0'
```

Por isso, após `fread()`, o programa executa:

```c
buffer[read_size] = '\0';
```

Isso permite que o conteúdo lido seja tratado corretamente como uma string pelas demais funções.

---

## 8.3 O que acontece quando o arquivo não existe?

O programa tenta abrir o arquivo através de:

```c
fopen(filename, "r")
```

Caso `fopen()` falhe, o ponteiro retornado é nulo.

O programa então executa:

```c
perror("Error opening file");
exit(EXIT_FAILURE);
```

encerrando a execução.

---

## 8.4 Quem é responsável por liberar a memória reservada?

A memória é criada dentro de `read_file()`, mas o ponteiro é devolvido ao `main()`.

Depois que o código-fonte já foi utilizado, o `main()` executa:

```c
free(source_code);
```

Portanto, o `main()` é responsável por liberar essa memória.

---

# 9. Análise léxica

O analisador léxico está principalmente em:

```text
include/lexer.h
src/lexer.c
```

O lexer transforma a sequência de caracteres do arquivo-fonte em uma sequência de tokens.

Os tokens definidos no projeto são:

```text
T_NUMBER
T_PLUS
T_MINUS
T_MULT
T_DIV
T_LET
T_IDENTIFIER
T_EQUAL
T_PRINT
T_SEMICOLON
T_LPAREN
T_RPAREN
T_EOF
```

A função principal é:

```c
TokenList lex(const char* source)
```

Durante a análise:

- `isspace()` identifica e ignora espaços;
- `isdigit()` identifica números;
- `isalpha()` inicia o reconhecimento de palavras;
- `isalnum()` permite continuar lendo letras e números;
- `strcmp()` diferencia palavras reservadas;
- `switch` reconhece operadores e pontuações.

As palavras:

```text
let
print
```

são tratadas como palavras reservadas.

Os demais nomes reconhecidos são transformados em `T_IDENTIFIER`.

---

## 9.1 Teste léxico válido

Entrada utilizada:

```c
let valor1 = 123 + 4;
print(valor1);
```

Tokens obtidos:

```text
LET
IDENT(valor1)
EQUAL
NUMBER(123)
PLUS
NUMBER(4)
SEMICOLON
PRINT
IDENT(valor1)
SEMICOLON
EOF
```

Resultado da execução:

```text
127
```

O teste foi executado corretamente.

---

## 9.2 Teste léxico inválido

Entrada:

```c
let valor = 10 @ 2;
```

Resultado:

```text
Unknown character: @
```

### Caractere responsável

```text
@
```

### Arquivo responsável

```text
src/lexer.c
```

### Função responsável

```text
lex()
```

### Classificação

**Erro léxico**, pois o caractere não pertence ao conjunto de símbolos reconhecidos pelo lexer.

---

## 9.3 Como o lexer diferencia `let` de um identificador comum?

Depois de construir a palavra dentro do `buffer`, o lexer utiliza `strcmp()`.

Se:

```c
strcmp(buffer, "let") == 0
```

é criado um token `T_LET`.

Se a palavra for `print`, é criado `T_PRINT`.

Caso contrário, é criado um token `T_IDENTIFIER`.

---

## 9.4 Como um número com vários dígitos é construído?

O lexer utiliza:

```c
value = value * 10 + (source[i] - '0');
```

Por exemplo, para `123`:

```text
0 * 10 + 1 = 1
1 * 10 + 2 = 12
12 * 10 + 3 = 123
```

Dessa forma, vários caracteres numéricos são transformados em um único valor inteiro.

---

## 9.5 Identificadores como `nota1` são aceitos?

Sim.

O primeiro caractere precisa passar por `isalpha()`.

Depois disso, o restante é lido utilizando `isalnum()`, permitindo letras e números.

Portanto:

```text
nota1
valor2
x10
```

podem ser identificadores.

---

## 9.6 Identificadores iniciados por número são aceitos?

Não como um único identificador.

Como o lexer testa números através de `isdigit()`, uma entrada iniciada por número começa sendo reconhecida como um token numérico.

Portanto, algo como:

```text
1nota
```

não será reconhecido como um único `T_IDENTIFIER`.

---

## 9.7 O lexer registra linha e coluna?

Não.

A implementação atual percorre o código utilizando apenas o índice:

```c
int i
```

Não existem campos de linha e coluna na estrutura `Token`.

---

## 9.8 Existe limite para a quantidade de tokens?

Sim.

A lista é inicialmente criada com:

```c
malloc(128 * sizeof(Token))
```

A implementação atual não possui lógica para aumentar esse espaço dinamicamente.

---

## 9.9 O que pode ocorrer se o programa gerar mais de 128 tokens?

Como o lexer continua adicionando elementos ao vetor sem redimensioná-lo, poderá ocorrer escrita fora da região de memória reservada.

Isso representa comportamento indefinido e pode provocar corrupção de memória ou falha do programa.

---

# 10. Análise sintática

O parser está implementado principalmente em:

```text
include/parser.h
src/parser.c
```

As funções principais analisadas foram:

```c
parse()
parse_statement()
parse_expression()
create_node()
```

O parser recebe os tokens produzidos pelo lexer e constrói uma AST.

Entre as estruturas reconhecidas estão:

```text
let IDENTIFICADOR = EXPRESSAO ;
print ( EXPRESSAO ) ;
```

Além de expressões contendo números, identificadores, parênteses e operadores aritméticos.

---

## 10.1 Teste sintático válido

Entrada:

```c
let resultado = (10 + 5) * 2;
print(resultado);
```

Resultado esperado:

```text
30
```

Resultado observado:

```text
30
```

Situação:

**Aprovado.**

---

## 10.2 Ausência de `=`

Entrada:

```c
let resultado 10 + 5;
```

Resultado observado:

```text
Syntax error: expected '=' at pos=2
```

Posição informada:

```text
2
```

A detecção ocorre em `parse_statement()`, quando o parser verifica se depois do identificador existe um token `T_EQUAL`.

Classificação:

**Erro sintático.**

---

## 10.3 Ausência de `;`

Entrada:

```c
let resultado = 10 + 5
```

Resultado:

```text
Syntax error: expected ';' at pos=6
```

Posição:

```text
6
```

A detecção ocorre em `parse_statement()`.

Classificação:

**Erro sintático.**

---

## 10.4 Parêntese incompleto

Entrada:

```c
print((10 + 5);
```

Resultado:

```text
Syntax error: expected ')' at pos=7
```

Posição:

```text
7
```

A verificação do fechamento de parênteses ocorre durante a análise sintática da expressão.

Classificação:

**Erro sintático.**

---

# 11. Análise da AST

A AST é definida em:

```text
include/parser.h
```

Os tipos de nós disponíveis são:

```c
AST_NUMBER
AST_BINARY_OP
AST_VAR
AST_ASSIGN
AST_PRINT
```

Cada `ASTNode` possui:

```c
ASTNodeType type;
int value;
char name[32];
ASTNode* left;
ASTNode* right;
```

Foi utilizado o programa:

```c
let x = 5 + 3;
print(x);
```

A atribuição produz uma estrutura semelhante a:

```text
AST_ASSIGN(x)
  AST_BINARY_OP(+)
    AST_NUMBER(5)
    AST_NUMBER(3)
```

## 11.1 Qual nó representa a atribuição?

```text
AST_ASSIGN
```

---

## 11.2 Onde o nome `x` é armazenado?

No campo:

```c
name
```

do nó `AST_ASSIGN`.

---

## 11.3 Quais nós representam os números?

Os nós:

```text
AST_NUMBER(5)
AST_NUMBER(3)
```

---

## 11.4 Qual nó representa a soma?

```text
AST_BINARY_OP(+)
```

---

## 11.5 Como `left` e `right` são utilizados?

Em uma operação binária, `left` aponta para o operando da esquerda e `right` para o operando da direita.

Por exemplo:

```text
       +
      / \
     5   3
```

corresponde aproximadamente a:

```text
AST_BINARY_OP(+)
├── left  → AST_NUMBER(5)
└── right → AST_NUMBER(3)
```

Nos nós de atribuição e impressão, o ponteiro `left` é utilizado para armazenar a expressão associada ao comando.

---

# 12. Análise da tabela de símbolos

A tabela de símbolos está definida em:

```text
include/interpreter.h
```

Cada símbolo possui:

```c
char name[32];
int value;
```

Por exemplo:

```text
x → 5
idade → 30
resultado → 15
```

A tabela suporta até:

```text
128 símbolos
```

As principais funções são:

### `init_symbol_table()`

Inicializa a tabela definindo sua quantidade de elementos como zero.

### `set_symbol()`

Adiciona uma variável ou atualiza seu valor caso ela já exista.

### `lookup_symbol()`

Procura uma variável pelo nome e devolve seu valor.

Caso a variável não seja encontrada, é apresentado um erro de execução.

---

# 13. Análise do interpretador

O interpretador está implementado em:

```text
src/interpreter.c
```

O fluxo principal pode ser representado como:

```text
interpret()
     ↓
exec_statement()
     ↓
eval_expression()
     ↓
lookup_symbol() / set_symbol()
```

## `interpret()`

Percorre os comandos existentes na AST.

## `exec_statement()`

Executa cada comando.

Para `AST_ASSIGN`, calcula a expressão e armazena o resultado na tabela.

Para `AST_PRINT`, calcula a expressão e apresenta o valor.

## `eval_expression()`

Avalia expressões recursivamente.

Pode avaliar:

- números;
- variáveis;
- soma;
- subtração;
- multiplicação;
- divisão.

---

## 13.1 Variável não definida

Foi testado:

```c
print(x);
```

Resultado:

```text
Runtime error: undefined variable 'x'
```

A falha é detectada durante a execução quando `lookup_symbol()` não encontra `x`.

Ela pode ser classificada como um **erro semântico detectado durante a execução**.

A variável não é detectada pelo parser porque, sintaticamente, utilizar um identificador em uma expressão é permitido. O parser verifica a estrutura da linguagem, mas não verifica se aquele nome possui um valor definido na tabela de símbolos.

Essa verificação ocorre posteriormente no interpretador.

---

## 13.2 Divisão por zero

Foi testado:

```c
let x = 10 / 0;
print(x);
```

Resultado:

```text
Runtime error: division by zero
```

O erro é detectado por `eval_expression()` antes da realização da divisão.

Pode ser classificado como **erro de execução**.

---

# 14. Classificação do projeto

Apesar do nome **Mini C Compiler**, a versão analisada funciona atualmente como um **interpretador**.

O projeto não:

- gera código de máquina;
- gera assembly;
- gera bytecode;
- gera outro programa em C;
- produz um executável correspondente ao programa escrito na linguagem Mini C.

O GCC compila o próprio interpretador escrito em C, mas isso não significa que o Mini C esteja compilando os programas fornecidos a ele.

Quando executamos:

```bash
./mini-c programa.txt
```

o programa realiza:

```text
Código-fonte
→ Lexer
→ Tokens
→ Parser
→ AST
→ Interpretador
→ Resultado
```

A principal evidência está na chamada:

```c
interpret(ast);
```

A função `interpret()` percorre diretamente a AST e executa seus comandos.

Portanto, tecnicamente, a implementação atual deve ser classificada como um **interpretador baseado em AST**, e não como compilador ou transpiler.

---

# 15. Resultados dos testes

Durante a análise foram criados diferentes arquivos dentro do diretório `testes/`.

## Testes léxicos e sintáticos

| ID | Entrada/Teste | Resultado esperado | Resultado obtido | Situação |
|---|---|---|---|---|
| T01 | Programa válido | Execução normal | Execução normal | Aprovado |
| T02 | Caractere `@` | Erro léxico | `Unknown character: @` | Aprovado |
| T03 | Ausência de `=` | Erro sintático | `expected '=' at pos=2` | Aprovado |
| T04 | Ausência de `;` | Erro sintático | `expected ';' at pos=6` | Aprovado |
| T05 | Parêntese incompleto | Erro sintático | `expected ')' at pos=7` | Aprovado |
| T06 | Variável não definida | Erro de execução | `undefined variable 'x'` | Aprovado |
| T07 | Divisão por zero | Erro de execução | `division by zero` | Aprovado |

Também foram realizados outros testes exploratórios envolvendo:

- atribuição simples;
- soma;
- multiplicação;
- utilização de parênteses;
- múltiplas variáveis;
- expressões mais complexas.

---

# 16. Análise de precedência e associatividade

A atividade solicita verificar se o parser atual respeita a precedência e a associatividade convencionais dos operadores.

Foram criados três testes específicos.

---

## 16.1 Teste A — Precedência

Entrada:

```c
print(2 * 3 + 4);
```

Resultado esperado:

```text
10
```

Resultado obtido:

```text
14
```

AST:

```text
AST_PRINT
  AST_BINARY_OP(*)
    AST_NUMBER(2)
    AST_BINARY_OP(+)
      AST_NUMBER(3)
      AST_NUMBER(4)
```

A AST corresponde a:

```text
2 * (3 + 4)
```

e não a:

```text
(2 * 3) + 4
```

Portanto, o parser apresentou um **problema de precedência**.

**Situação: Reprovado.**

---

## 16.2 Teste B — Associatividade

Entrada:

```c
print(10 - 3 - 2);
```

Resultado esperado:

```text
5
```

Resultado obtido:

```text
9
```

AST:

```text
AST_PRINT
  AST_BINARY_OP(-)
    AST_NUMBER(10)
    AST_BINARY_OP(-)
      AST_NUMBER(3)
      AST_NUMBER(2)
```

A estrutura construída corresponde a:

```text
10 - (3 - 2)
```

produzindo:

```text
9
```

Entretanto, pela associatividade convencional da subtração, a expressão deveria ser:

```text
(10 - 3) - 2
```

produzindo:

```text
5
```

Portanto, foi identificado um **problema de associatividade**.

**Situação: Reprovado.**

---

## 16.3 Teste C — Precedência

Entrada:

```c
print(2 + 3 * 4);
```

Resultado esperado:

```text
14
```

Resultado obtido:

```text
14
```

AST:

```text
AST_PRINT
  AST_BINARY_OP(+)
    AST_NUMBER(2)
    AST_BINARY_OP(*)
      AST_NUMBER(3)
      AST_NUMBER(4)
```

Neste caso, a estrutura produzida corresponde a:

```text
2 + (3 * 4)
```

e o resultado coincide com a aritmética convencional.

**Situação: Aprovado.**

---

## 16.4 Conclusão sobre precedência e associatividade

Os testes demonstram que o parser não implementa corretamente as regras gerais de precedência e associatividade.

O comportamento está relacionado à implementação simplificada de `parse_expression()`, que trata `+`, `-`, `*` e `/` dentro da mesma lógica e utiliza chamadas recursivas para analisar o operando da direita.

Por isso, determinadas expressões acabam sendo agrupadas à direita.

Isso explica resultados como:

```text
2 * 3 + 4
```

ser interpretado como:

```text
2 * (3 + 4)
```

e:

```text
10 - 3 - 2
```

como:

```text
10 - (3 - 2)
```

Um terceiro teste mostrou que algumas expressões podem apresentar o resultado correto, como:

```text
2 + 3 * 4 = 14
```

Porém, isso não significa que a implementação possua uma regra correta de precedência. Nesse caso específico, a estrutura criada pela recursão coincide com a precedência aritmética esperada.

Também foi testada a expressão:

```c
let x = (10 + 2) * 3 - 4 / 2;
print(x);
```

O resultado obtido foi:

```text
12
```

enquanto, utilizando as regras convencionais de precedência e associatividade, o resultado seria:

```text
34
```

Esse teste complementar reforça a limitação encontrada no parser.

---

# 17. Descrição da melhoria individual

## 17.1 Melhoria escolhida

A melhoria individual escolhida foi:

**Correção da precedência dos operadores.**

Essa melhoria corresponde à possibilidade nº 9 apresentada no enunciado da atividade.

## 17.2 Problema identificado

Durante os testes de precedência realizados na análise da implementação original, foi identificado que o parser não respeita corretamente a precedência convencional dos operadores aritméticos.

Por exemplo, foi executado:

```c
print(2 * 3 + 4);

# 18. Limitações encontradas

Durante a análise da implementação original foram identificadas algumas limitações.

## 18.1 Precedência dos operadores

O parser não implementa de maneira geral a precedência convencional entre `*`, `/`, `+` e `-`.

Isso foi demonstrado pelo teste:

```c
print(2 * 3 + 4);
```

que deveria produzir `10`, mas produziu `14`.

## 18.2 Associatividade

Operadores como subtração podem ser agrupados incorretamente à direita.

O teste:

```c
print(10 - 3 - 2);
```

produziu `9`, enquanto o resultado convencional é `5`.

## 18.3 Lista de tokens com tamanho fixo

O lexer reserva inicialmente espaço para 128 tokens:

```c
malloc(128 * sizeof(Token))
```

mas não implementa redimensionamento do vetor.

Programas suficientemente grandes podem ultrapassar essa capacidade.

## 18.4 Ausência de linha e coluna nos tokens

Os tokens não armazenam informações sobre a linha e a coluna em que foram encontrados.

Isso reduz a precisão das mensagens de erro.

## 18.5 Tabela de símbolos limitada

A tabela de símbolos possui capacidade fixa para 128 variáveis.

## 18.6 Detecção de variável indefinida somente em execução

O parser aceita normalmente o uso de um identificador mesmo que ele ainda não possua um valor definido.

A falha somente é detectada posteriormente por `lookup_symbol()` durante a interpretação.

---

# 19. Conclusão

**Seção a ser finalizada após a implementação e validação da melhoria individual.**

Até o momento, a análise permitiu acompanhar o funcionamento do projeto desde a leitura do arquivo-fonte até a execução da AST.

Também foi possível diferenciar as responsabilidades do lexer, parser e interpretador, compreender a utilização da tabela de símbolos e identificar erros léxicos, sintáticos e de execução.

Os testes de precedência e associatividade também permitiram identificar limitações concretas da implementação atual do parser.

A conclusão definitiva será complementada após a implementação da melhoria individual.

---

# 20. Referências

IRONRINOX. **Mini C Compiler**. GitHub.

Material da disciplina de Compiladores disponibilizado pelo professor.

Atividade Individual — Análise do Mini C Compiler. Disciplina de Compiladores, 2026.

---

# Registro de uso de ferramentas de IA

Durante a realização da atividade foi utilizada uma ferramenta de inteligência artificial como apoio para organização do estudo, compreensão de trechos do código, revisão das análises e estruturação da documentação.

Os códigos, testes e resultados utilizados no trabalho foram conferidos e executados no ambiente local, permanecendo sob responsabilidade do estudante a compreensão e a validação do conteúdo apresentado.