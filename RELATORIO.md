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

Para a realização da atividade, o projeto foi inicialmente clonado para o ambiente local e posteriormente publicado em um repositório individual, no qual foram adicionados testes, documentação e a melhoria implementada durante o trabalho.

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

Além da análise do código existente, foram realizados testes válidos e inválidos para compreender o funcionamento do lexer, parser e interpretador.

Também foram realizados testes específicos para avaliar precedência e associatividade de operadores.

Durante essa análise foi identificada uma limitação no parser relacionada à precedência dos operadores aritméticos.

Como melhoria individual, foi implementada a **correção da precedência dos operadores**, reorganizando o parser em diferentes níveis de análise de expressões.

---

# 4. Preparação do ambiente

A atividade foi desenvolvida em ambiente Linux, utilizando principalmente o terminal e o Visual Studio Code.

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

O arquivo `.gitignore` também foi atualizado para ignorar o executável `mini-c`, gerado durante a compilação.

---

# 5. Procedimento de compilação e execução

A compilação foi realizada na raiz do projeto utilizando:

```bash
gcc src/main.c src/lexer.c src/parser.c src/interpreter.c src/utils.c -Iinclude -o mini-c
```

Esse comando compila os arquivos-fonte presentes em `src/`, utiliza os headers presentes em `include/` e gera o executável:

```text
mini-c
```

A execução pode ser realizada informando um arquivo contendo o programa a ser analisado:

```bash
./mini-c examples/test.txt
```

Durante a atividade também foram utilizados arquivos próprios de teste:

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

É o ponto de entrada do programa.

Coordena o fluxo principal:

```text
read_file()
→ lex()
→ parse()
→ interpret()
```

---

## `src/utils.c`

Implementa `read_file()`, responsável pela abertura e leitura do arquivo-fonte.

---

## `include/lexer.h`

Define:

- tipos de tokens;
- estrutura `Token`;
- estrutura `TokenList`.

---

## `src/lexer.c`

Implementa o analisador léxico, transformando os caracteres do código-fonte em tokens.

Principais funções:

- `create_token()`;
- `lex()`;
- `print_tokens()`.

---

## `include/parser.h`

Define os tipos de nós da AST e a estrutura:

```c
ASTNode
```

---

## `src/parser.c`

Implementa o analisador sintático e a construção da AST.

Após a melhoria implementada, suas principais funções relacionadas às expressões são:

- `create_node()`;
- `parse()`;
- `parse_statement()`;
- `parse_expression()`;
- `parse_additive()`;
- `parse_multiplicative()`;
- `parse_primary()`;
- `print_ast()`.

As funções `parse_additive()`, `parse_multiplicative()` e `parse_primary()` foram utilizadas na melhoria de precedência implementada durante a atividade.

---

## `include/interpreter.h`

Define a estrutura da tabela de símbolos e funções utilizadas pelo interpretador.

---

## `src/interpreter.c`

Executa a AST e gerencia as variáveis.

Principais funções:

- `init_symbol_table()`;
- `lookup_symbol()`;
- `set_symbol()`;
- `eval_expression()`;
- `exec_statement()`;
- `interpret()`.

---

## `examples/test.txt`

Arquivo utilizado pelo projeto para demonstrar a execução de um programa.

---

## `testes/`

Diretório criado durante a atividade para armazenar:

- testes válidos;
- testes inválidos;
- testes léxicos;
- testes sintáticos;
- testes de execução;
- testes de precedência;
- testes de associatividade;
- teste da melhoria individual.

---

# 7. Análise do ponto de entrada

O ponto de entrada do programa está na função:

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

---

## 7.1 Como o caminho do arquivo-fonte é recebido?

O caminho é recebido através dos argumentos da linha de comando.

`argv[1]` contém o primeiro argumento fornecido depois do nome do executável.

Exemplo:

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

e a execução é encerrada.

---

## 7.3 Qual função lê o arquivo?

A função:

```c
read_file()
```

implementada em:

```text
src/utils.c
```

---

## 7.4 Qual função realiza a análise léxica?

A função:

```c
lex()
```

implementada em:

```text
src/lexer.c
```

---

## 7.5 Qual função constrói a AST?

A função:

```c
parse()
```

implementada em:

```text
src/parser.c
```

---

## 7.6 Qual função executa a AST?

A função:

```c
interpret()
```

implementada em:

```text
src/interpreter.c
```

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

presente em:

```text
src/utils.c
```

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

---

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

Por isso, após `fread()`, o programa adiciona esse caractere ao final do buffer.

Isso permite que o conteúdo lido seja tratado corretamente como uma string pelas demais funções.

---

## 8.3 O que acontece quando o arquivo não existe?

O programa tenta abrir o arquivo através de:

```c
fopen(filename, "r")
```

Caso `fopen()` falhe, o ponteiro retornado é nulo.

O programa utiliza uma mensagem de erro e encerra a execução.

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

Os tokens definidos no projeto incluem:

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
- `switch` reconhece operadores e símbolos.

As palavras:

```text
let
print
```

são tratadas como palavras reservadas.

Os demais nomes reconhecidos são transformados em:

```text
T_IDENTIFIER
```

---

## 9.1 Teste léxico válido

Entrada utilizada:

```c
let valor1 = 123 + 4;
print(valor1);
```

Tokens observados:

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

O programa foi interpretado corretamente.

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

Depois de construir a palavra dentro de um buffer, o lexer utiliza `strcmp()`.

Se:

```c
strcmp(buffer, "let") == 0
```

é criado um token:

```text
T_LET
```

Se a palavra for `print`, é criado:

```text
T_PRINT
```

Caso contrário, é criado:

```text
T_IDENTIFIER
```

---

## 9.4 Como um número com vários dígitos é construído?

O lexer utiliza a lógica:

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

Portanto:

```text
1nota
```

não é reconhecido como um único `T_IDENTIFIER`.

---

## 9.7 O lexer registra linha e coluna?

Não.

A implementação atual percorre o código utilizando apenas um índice.

Não existem campos de linha e coluna na estrutura `Token`.

---

## 9.8 Existe limite para a quantidade de tokens?

Sim.

A lista é criada com espaço para:

```c
malloc(128 * sizeof(Token))
```

A implementação atual não possui lógica de redimensionamento desse vetor.

---

## 9.9 O que pode ocorrer se o programa gerar mais de 128 tokens?

O lexer poderá tentar escrever fora da região de memória originalmente reservada.

Isso constitui comportamento indefinido e pode provocar:

- corrupção de memória;
- falhas;
- comportamento inesperado do programa.

---

# 10. Análise sintática

O parser está implementado principalmente em:

```text
include/parser.h
src/parser.c
```

O parser recebe os tokens produzidos pelo lexer e constrói uma AST.

Entre as estruturas utilizadas pela linguagem estão:

```text
let identificador = expressão ;
print expressão ;
```

Uma expressão pode conter:

- números;
- identificadores;
- operadores aritméticos;
- expressões entre parênteses.

Os parênteses fazem parte da análise das expressões.

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

**Situação: Aprovado.**

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

**Classificação: erro sintático.**

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

A detecção ocorre em:

```text
parse_statement()
```

**Classificação: erro sintático.**

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

Após a melhoria implementada, essa responsabilidade está concentrada em:

```text
parse_primary()
```

**Classificação: erro sintático.**

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

---

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

Exemplo:

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

O projeto também utiliza o campo `right` no encadeamento de comandos, o que contribui para algumas limitações na representação e impressão completa da AST.

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

Exemplos conceituais:

```text
x → 5
idade → 30
resultado → 15
```

A tabela possui capacidade fixa para:

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

Caso a variável não seja encontrada, é apresentado um erro durante a execução.

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

---

## `interpret()`

Percorre os comandos existentes na AST.

---

## `exec_statement()`

Executa cada comando.

Para `AST_ASSIGN`, calcula a expressão e armazena o resultado na tabela de símbolos.

Para `AST_PRINT`, calcula a expressão e apresenta o valor.

---

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

Pode ser classificada como um **erro semântico detectado durante a execução**.

A variável não é rejeitada pelo parser porque, sintaticamente, utilizar um identificador em uma expressão é válido.

A verificação sobre a existência daquele identificador ocorre posteriormente no interpretador.

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

**Classificação: erro de execução.**

---

# 14. Classificação do projeto

Apesar do nome **Mini C Compiler**, a versão analisada funciona atualmente como um **interpretador**.

O projeto não:

- gera código de máquina;
- gera assembly;
- gera bytecode;
- gera outro programa em C;
- produz um executável correspondente ao programa escrito na linguagem Mini C.

O GCC compila o próprio programa escrito em C, mas isso não significa que o Mini C esteja compilando os programas fornecidos a ele.

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

Portanto, tecnicamente, a implementação atual deve ser classificada como um **interpretador baseado em AST**, e não como um compilador ou transpiler.

---

# 15. Resultados dos testes

Durante a análise foram criados diferentes arquivos dentro do diretório:

```text
testes/
```

Foram executados testes válidos, inválidos e específicos para a análise da precedência e da melhoria implementada.

| ID | Entrada/Teste | Resultado esperado | Resultado obtido | Situação |
|---|---|---|---|---|
| T01 | Programa original do repositório | `10` | `10` | Aprovado |
| T02 | Caractere desconhecido `@` | Erro léxico | `Unknown character: @` | Aprovado |
| T03 | Ausência de `=` | Erro sintático | `expected '=' at pos=2` | Aprovado |
| T04 | Ausência de `;` | Erro sintático | `expected ';' at pos=6` | Aprovado |
| T05 | Parêntese incompleto | Erro sintático | `expected ')' at pos=7` | Aprovado |
| T06 | Variável não definida | Erro de execução | `undefined variable 'x'` | Aprovado |
| T07 | Divisão por zero | Erro de execução | `division by zero` | Aprovado |
| T08 | Precedência: `2 * 3 + 4` | `10` | `10` após a correção | Aprovado |
| T09 | Associatividade: `10 - 3 - 2` | `5` | `5` após a correção | Aprovado |
| T10 | Melhoria individual de precedência | `34` | `34` | Aprovado |

As saídas completas dos testes T01 a T10 foram armazenadas no diretório `evidencias/`, permitindo consultar os resultados obtidos durante a execução.

Também foram realizados testes exploratórios envolvendo:

- atribuição simples;
- soma;
- multiplicação;
- utilização de parênteses;
- variável não definida;
- divisão por zero;
- múltiplas variáveis;
- expressões mais complexas.

---

# 16. Análise de precedência e associatividade

Antes da implementação da melhoria, foram executados testes específicos para verificar se o parser original respeitava a precedência e a associatividade convencionais.

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

Resultado obtido antes da correção:

```text
14
```

AST original:

```text
AST_PRINT
  AST_BINARY_OP(*)
    AST_NUMBER(2)
    AST_BINARY_OP(+)
      AST_NUMBER(3)
      AST_NUMBER(4)
```

A AST correspondia a:

```text
2 * (3 + 4)
```

e não a:

```text
(2 * 3) + 4
```

Portanto, foi identificado um **problema de precedência**.

**Situação na implementação original: Reprovado.**

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

Resultado obtido antes da correção:

```text
9
```

AST original:

```text
AST_PRINT
  AST_BINARY_OP(-)
    AST_NUMBER(10)
    AST_BINARY_OP(-)
      AST_NUMBER(3)
      AST_NUMBER(2)
```

A estrutura construída correspondia a:

```text
10 - (3 - 2)
```

produzindo:

```text
9
```

Entretanto, pela associatividade convencional da subtração, a expressão deve ser interpretada como:

```text
(10 - 3) - 2
```

produzindo:

```text
5
```

Portanto, também foi identificado um **problema de associatividade**.

**Situação na implementação original: Reprovado.**

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

Nesse caso, a estrutura produzida correspondia a:

```text
2 + (3 * 4)
```

e o resultado coincidia com a aritmética convencional.

**Situação: Aprovado.**

Entretanto, esse resultado isolado não significava que o parser possuía regras corretas de precedência.

A recursão utilizada pela implementação original apenas produzia, nesse caso específico, um agrupamento compatível com o esperado.

---

## 16.4 Teste complementar complexo

Também foi executado:

```c
let x = (10 + 2) * 3 - 4 / 2;
print(x);
```

Antes da melhoria, o resultado obtido foi:

```text
12
```

Entretanto, aplicando as regras convencionais:

```text
(10 + 2) * 3 - 4 / 2
12 * 3 - 2
36 - 2
34
```

o resultado esperado era:

```text
34
```

Esse teste reforçou que o parser original não implementava corretamente a precedência dos operadores.

---

## 16.5 Causa do problema

A implementação original de `parse_expression()` tratava os operadores:

```text
+
-
*
/
```

no mesmo nível.

Além disso, ao encontrar um operador, a função chamava novamente:

```c
parse_expression()
```

para construir o operando da direita.

Essa estratégia provocava agrupamentos à direita e não estabelecia níveis distintos de prioridade entre operadores.

---

# 17. Descrição da melhoria individual

## 17.1 Melhoria escolhida

A melhoria individual escolhida foi:

**Correção da precedência dos operadores.**

Essa melhoria corresponde à possibilidade nº 9 apresentada no enunciado da atividade.

---

## 17.2 Problema identificado

O teste:

```c
print(2 * 3 + 4);
```

deveria resultar em:

```text
10
```

mas a implementação original produzia:

```text
14
```

A AST original agrupava a expressão como:

```text
2 * (3 + 4)
```

em vez de:

```text
(2 * 3) + 4
```

Outro exemplo foi:

```c
let x = (10 + 2) * 3 - 4 / 2;
print(x);
```

que produzia:

```text
12
```

quando o resultado correto deveria ser:

```text
34
```

---

## 17.3 Comportamento desejado

O parser deveria respeitar a precedência aritmética convencional:

```text
* e / → maior prioridade
+ e - → menor prioridade
```

Assim:

```text
2 * 3 + 4
```

deve ser interpretado como:

```text
(2 * 3) + 4
```

e:

```text
2 + 3 * 4
```

deve ser interpretado como:

```text
2 + (3 * 4)
```

---

## 17.4 Arquivo alterado

A melhoria foi implementada principalmente no arquivo:

```text
src/parser.c
```

---

## 17.5 Implementação realizada

A função original:

```c
parse_expression()
```

tratava todos os operadores no mesmo nível.

A implementação foi reorganizada em diferentes níveis de análise:

- `parse_primary()`;
- `parse_multiplicative()`;
- `parse_additive()`;
- `parse_expression()`.

A nova hierarquia é:

```text
parse_expression()
        ↓
parse_additive()
        ↓
parse_multiplicative()
        ↓
parse_primary()
```

---

### `parse_primary()`

Responsável por reconhecer os elementos básicos de uma expressão:

- números;
- variáveis;
- expressões entre parênteses.

---

### `parse_multiplicative()`

Responsável por:

```text
*
/
```

Esses operadores são tratados antes de soma e subtração.

---

### `parse_additive()`

Responsável por:

```text
+
-
```

Cada operando utilizado por essa função já é obtido através de `parse_multiplicative()`.

Dessa forma, multiplicações e divisões são construídas antes das somas e subtrações.

---

### `parse_expression()`

Permanece sendo o ponto de entrada da análise de expressões:

```c
ASTNode* parse_expression(TokenList* tokens, int* pos) {
    return parse_additive(tokens, pos);
}
```

---

## 17.6 Decisão de implementação

Foi adotada uma separação em níveis de precedência.

A lógica pode ser representada como:

```text
expressão
    ↓
soma/subtração
    ↓
multiplicação/divisão
    ↓
números, variáveis e parênteses
```

Além de corrigir a precedência, a utilização de laços `while` para consumir operadores do mesmo nível passou a construir essas operações progressivamente pela esquerda.

Como consequência, o teste:

```c
10 - 3 - 2
```

também passou a produzir o agrupamento convencional:

```text
(10 - 3) - 2
```

---

## 17.7 Testes após a melhoria

Após a modificação, os três testes utilizados anteriormente foram executados novamente.

### Teste A

```c
print(2 * 3 + 4);
```

Resultado:

```text
10
```

AST:

```text
AST_PRINT
  AST_BINARY_OP(+)
    AST_BINARY_OP(*)
      AST_NUMBER(2)
      AST_NUMBER(3)
    AST_NUMBER(4)
```

**Situação após a melhoria: Aprovado.**

---

### Teste B

```c
print(10 - 3 - 2);
```

Resultado:

```text
5
```

AST:

```text
AST_PRINT
  AST_BINARY_OP(-)
    AST_BINARY_OP(-)
      AST_NUMBER(10)
      AST_NUMBER(3)
    AST_NUMBER(2)
```

**Situação após a melhoria: Aprovado.**

---

### Teste C

```c
print(2 + 3 * 4);
```

Resultado:

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

**Situação após a melhoria: Aprovado.**

---

## 17.8 Comparação antes e depois

| Expressão | Antes | Depois | Resultado esperado |
|---|---:|---:|---:|
| `2 * 3 + 4` | 14 | 10 | 10 |
| `10 - 3 - 2` | 9 | 5 | 5 |
| `2 + 3 * 4` | 14 | 14 | 14 |
| `(10 + 2) * 3 - 4 / 2` | 12 | 34 | 34 |

Os resultados mostram que a nova implementação corrigiu o comportamento identificado durante a análise.

---

## 17.9 Teste específico da melhoria — T10

Foi criado:

```text
testes/10_melhoria_precedencia.txt
```

Conteúdo:

```c
let x = (10 + 2) * 3 - 4 / 2;
print(x);
```

Após a melhoria, a AST foi construída como:

```text
AST_ASSIGN(x)
  AST_BINARY_OP(-)
    AST_BINARY_OP(*)
      AST_BINARY_OP(+)
        AST_NUMBER(10)
        AST_NUMBER(2)
      AST_NUMBER(3)
    AST_BINARY_OP(/)
      AST_NUMBER(4)
      AST_NUMBER(2)
```

Resultado:

```text
34
```

Portanto:

**T10 — Aprovado.**

---

## 17.10 Testes de regressão

Após a implementação também foram novamente executados os testes anteriores do projeto.

Continuaram funcionando corretamente:

- atribuição simples;
- soma;
- precedência em `5 + 3 * 2`;
- utilização de parênteses;
- erro de variável indefinida;
- divisão por zero;
- ausência de `;`;
- ausência de `=`;
- utilização de múltiplas variáveis.

Isso indica que a modificação realizada não quebrou os comportamentos anteriormente testados.

---

# 18. Limitações encontradas

Mesmo após a melhoria implementada, o projeto ainda apresenta algumas limitações.

---

## 18.1 Quantidade fixa de tokens

O lexer reserva espaço para:

```c
malloc(128 * sizeof(Token))
```

mas não implementa redimensionamento do vetor.

Programas com uma quantidade suficientemente grande de tokens podem ultrapassar essa capacidade.

---

## 18.2 Ausência de linha e coluna nos tokens

Os tokens não armazenam informações sobre linha e coluna.

As mensagens de erro utilizam principalmente a posição dentro da lista de tokens.

Isso dificulta a localização do erro em programas maiores.

---

## 18.3 Tabela de símbolos limitada

A tabela de símbolos possui capacidade fixa para:

```text
128 variáveis
```

Não há redimensionamento dinâmico.

---

## 18.4 Variável indefinida detectada somente em execução

O parser aceita o uso de um identificador mesmo que ele ainda não possua valor definido.

Por exemplo:

```c
print(x);
```

é sintaticamente aceito.

Somente durante a interpretação ocorre:

```text
Runtime error: undefined variable 'x'
```

Portanto, não existe uma etapa semântica separada que verifique previamente esse tipo de problema.

---

## 18.5 Impressão incompleta de alguns tokens

O lexer reconhece tokens de parênteses:

```text
T_LPAREN
T_RPAREN
```

porém a função responsável por imprimir os tokens não apresenta esses símbolos na saída observada durante os testes.

Por exemplo:

```c
print(x);
```

produz na listagem:

```text
PRINT
IDENT(x)
SEMICOLON
EOF
```

sem apresentar explicitamente os tokens correspondentes a `(` e `)`.

---

## 18.6 Impressão incompleta de múltiplos comandos da AST

Em programas contendo múltiplas instruções, a execução pode ocorrer corretamente, porém `print_ast()` não apresenta necessariamente todos os comandos encadeados.

Foi observado, por exemplo:

```c
let x = 5;
let y = 10;
print(x + y);
```

Resultado:

```text
15
```

Entretanto, a saída visual da AST apresentou apenas a primeira atribuição:

```text
AST_ASSIGN(x)
  AST_NUMBER(5)
```

Portanto, existe uma limitação na visualização completa da árvore/lista de comandos.

---

## 18.7 Estrutura simplificada para encadeamento de comandos

O campo `right` dos nós também é utilizado pelo parser para realizar o encadeamento dos comandos.

Ao mesmo tempo, em nós de operação binária, `right` representa o operando da direita.

Essa reutilização torna a representação da AST mais simples, mas também pode dificultar a separação entre estrutura de expressão e estrutura de sequência de comandos.

Uma implementação mais robusta poderia utilizar uma estrutura específica para representar uma lista de statements.

---

# 19. Conclusão

A atividade permitiu compreender de forma prática as principais etapas envolvidas na implementação de uma pequena linguagem.

Foi possível acompanhar todo o fluxo:

```text
arquivo-fonte
→ leitura
→ lexer
→ tokens
→ parser
→ AST
→ interpretador
→ resultado
```

A análise do lexer permitiu compreender como números, identificadores, palavras reservadas, operadores e símbolos são transformados em tokens.

A análise do parser demonstrou como esses tokens são transformados em uma árvore sintática abstrata.

O estudo do interpretador permitiu observar como a AST é avaliada e como a tabela de símbolos é utilizada para armazenar e recuperar valores de variáveis.

Os testes inválidos também ajudaram a diferenciar diferentes tipos de falha:

- erro léxico;
- erro sintático;
- erro semântico detectado em execução;
- erro de execução.

A análise mostrou ainda que, apesar do nome Mini C Compiler, a implementação estudada funciona atualmente como um **interpretador baseado em AST**, pois executa diretamente a árvore sintática e não gera código de máquina, assembly, bytecode ou outro programa equivalente.

Um dos principais problemas encontrados foi a falta de uma implementação adequada das regras de precedência dos operadores.

Na implementação original, a expressão:

```c
print(2 * 3 + 4);
```

produzia:

```text
14
```

em vez de:

```text
10
```

Além disso:

```c
let x = (10 + 2) * 3 - 4 / 2;
print(x);
```

produzia:

```text
12
```

em vez de:

```text
34
```

Como melhoria individual, o parser foi reorganizado utilizando diferentes níveis de análise:

```text
parse_expression()
→ parse_additive()
→ parse_multiplicative()
→ parse_primary()
```

Após a modificação, os testes passaram a respeitar a precedência convencional.

O teste complexo passou de:

```text
12
```

para:

```text
34
```

Também foi observado que a nova estratégia corrigiu a associatividade à esquerda dos operadores tratados, fazendo:

```c
10 - 3 - 2
```

passar de:

```text
9
```

para:

```text
5
```

Os testes anteriores também foram novamente executados e continuaram funcionando, indicando que a melhoria não causou regressões nos comportamentos já analisados.

Por fim, a atividade permitiu não apenas executar o projeto, mas compreender suas principais estruturas, identificar limitações reais da implementação e modificar o parser de forma fundamentada a partir dos resultados obtidos nos testes.

---

# 20. Referências

IRONRINOX. **Mini C Compiler**. GitHub.

Repositório original:

```text
ironrinox/mini-c-compiler
```

Repositório individual da atividade:

```text
PedroPignata/mini-c-compiler
```

Material da disciplina de Compiladores disponibilizado pelo professor.

Atividade Individual — Análise do Mini C Compiler. Disciplina de Compiladores, 2026.

---

# Registro de uso de ferramentas de IA

Durante a realização da atividade foi utilizada uma ferramenta de inteligência artificial como apoio para:

- organização do estudo;
- compreensão de trechos do código;
- discussão sobre lexer, parser, AST e interpretador;
- análise dos resultados dos testes;
- revisão da documentação;
- apoio na estruturação do relatório.

A ferramenta foi utilizada como apoio ao processo de aprendizagem.

Os comandos, códigos, alterações e testes apresentados no trabalho foram executados e verificados no ambiente local.

Os resultados apresentados no relatório foram comparados com as saídas reais do programa, permanecendo sob responsabilidade do estudante a compreensão, validação e apresentação do conteúdo desenvolvido.

Repositório do Git: https://github.com/PedroPignata/mini-c-compiler