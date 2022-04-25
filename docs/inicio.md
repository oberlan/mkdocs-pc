# Programação Competitiva

Vamos começar apresentando alguns dos recursos da linguagem de programação C++ que são úteis na programação competitiva. Todos os comandos serão dados considerando o Linux como Sistema Operaciona. Caso esteja usando Windows, use o [CS50 IDE](https://ide.cs50.io/) ou [VSCode for CS50](https://code.cs50.io/).

## Caracteristicas da linguagem

``` c++ linenums="1"
#include <bits/stdc++.h> // (1)

using namespace std; // (2)

int main() {
    // código
    return 0;
}
```

1. Recurso do compilador `g++` que permite incluir toda a biblioteca padrão. Assim, não é necessário incluir separadamente bibliotecas como `iostream`, `vector`, e `algorithm`. [Clique aqui para saber mais](https://gcc.gnu.org/onlinedocs/gcc-9.4.0/libstdc++/api/a00719_source.html).

2. Declara que as classes e funções da biblioteca padrão podem
ser usadas diretamente no código. Sem essa linha teríamos que escrever, por exemplo, `std::cout` ao invés de apenas `cout`.

O código pode ser compilado usando o seguinte comando:

```bash
g++ main.cpp -o programa
```

O comando produz um arquivo binário, chamado `programa`, a partir do código-fonte `main.cpp`. Leia mais [aqui](https://usaco.guide/general/cpp-command?lang=cpp).

Para evitar bugs comuns, **sempre** iremos compilar o código com algumas *flags* de compilação:

```bash
g++ -O2 -std=c++17 -Wshadow -fsanitize=address,undefined -Wall -Wextra -Wno-sign-compare -Wno-unused-parameter -Wno-unused-variable -Wno-unused-but-set-variable main.cpp -o programa
```

Para facilitar a compilação e execução do código, crie um arquivo chamado `cr` e adicione o seguinte código:

```bash
g++ -O2 -std=c++17 -Wshadow -fsanitize=address,undefined -Wall -Wextra -Wno-sign-compare -Wno-unused-parameter -Wno-unused-variable -Wno-unused-but-set-variable $1 -o programa && time ./programa < in 
```

No terminal, execute o comando `chmod +x cr` para dar direitos de execução ao arquivo `cr`. Para usá-lo basta passar como argumento um arquivo `.cpp`, por exemplo, `./cr main.cpp`. O código será compilado e, em caso de sucesso, será gerado um programa chamado `programa` que será executado considerando o arquivo `in` como dado de entrada. Ao usar o arquivo `in`, não precisamos digitar os dados de entrada.

## Entrada e Saída

Na maioria dos *contests*, é necessário ler da entrada padrão (teclado) e escrever algo. Em C++, é usado o `#!c++ cin` para leitura e `#!c++ cout` para saída. Também podem ser usado as funções de C, como `#!c scanf` e `#!c printf`.

A entrada do programa geralmente consiste em números e *strings* separados por espaços e/ou novas linhas. Eles podem ser lidos a partir do `cin` da seguinte forma:

``` c++ linenums="1"
int a, b;
string c;
cin >> a >> b >> c;
```

Considerando que há ao menos um espaço em branco ou uma nova linha entre cada elemento da entrada, esse código sempre funciona.

O `#!c++ cout` pode ser usado da seguinte forma:

``` c++ linenums="1"
cout << a << " " << b << " " << c << "\n"; // (1)
```

1. Um espaço em branco irá separar cada informação. Ao fim, uma linha em branco (`#!c++ \n`) será gerada.

Às vezes, a entrada e a saída podem ser um gargalo em um programa. Por isso, é comum ser adicionado as seguintes linhas no início do código:

``` c++ linenums="1"
ios_base::sync_with_stdio(0); // (1)
cin.tie(0); // (2)
```

1. [std::ios_base::sync_with_stdio](https://en.cppreference.com/w/cpp/io/ios_base/sync_with_stdio)
2. [std::ios::tie](https://en.cppreference.com/w/cpp/io/basic_ios/tie)

!!! danger "Atenção"
    Ao usar o comando `#!c++ ios_base::sync_with_stdio(0);`, será desativado a sincronização entre as função de C++ e C, por isso **não** use as função de entrada e saída de C (`#!c scanf` e `#!c printf`) junto com esse comando.

Alternativamente ao `\n` podemos usar o comando `#!c++ endl`. Entretanto, este comando irá liberar o *buffer* de saída e fará com que o código rode mais lento. Por isso, prefira usar o `\n`.

??? tip "Dica"
    Use sempre o `\n` ao invés do `#!c++ endl`. Use uma macro `#!c++ #define endl '\n'` para não correr o risco de esquecer.

Para se aprofundar mais:

[Input & Output](https://usaco.guide/general/input-output?lang=cpp)

[Fast Input & Output](https://usaco.guide/general/fast-io?lang=cpp)

## Trabalhando com números

* Inteiros: Para evitar [*integer overflow*](https://www.cplusplus.com/articles/DE18T05o/), use sempre `#!c++ long long` (64bits) ao invés de `#!c++ int`.
* Reais: Use `#!c++ double` (64bits) ou `#!c++ long double` (80bits). Esqueça o `#!c++ float` 😅. Além disso, nunca compare dois `#!c++ double` com o operador `#!c++ ==` (é possível que os valores sejam iguais, mas não são devido a erros de precisão). Para verificar se dois `#!c++ double` use o código a seguir:

``` c++ linenums="1"
double a, b;
...
if (abs(a-b) < 1e-9) {
    //a e b são iguais
}
...
```

Para saber mais: [Data Types](https://usaco.guide/general/data-types?lang=cpp)

## Simplificando o código

### Nomes de tipos

Usando o comando `#!c++ typedef` é possível dar um nome mais curto a um tipo de dado. Por exemplo:

``` c++ linenums="1"
typedef long long ll;
typedef vector<int> vi;
typedef vector<ll> vll;
typedef pair<int,int> pi;
typedef pair<ll,ll> pll;
typedef vector<pii> vpi;
typedef vector<pll> vpll;
```

### Macros

Uma macro significa que certas palavras no código serão substituídas antes da compilação. Em C++, as macros são definidas usando a palavra-chave `#!c++ #define`. Veja alguns exemplos:

``` c++ linenums="1"
#define F first
#define S second
#define PB push_back
#define MP make_pair
#define FOR(i,a,b) for(ll i = (a); i < (ll)(b); ++i)
#define INF 0x3f3f3f3f
#define INFLL 0x3f3f3f3f3f3f3f3f
#define all(x) x.begin(),x.end()
#define sz(x) (ll)x.size()
#define MOD 1000000007ll
#define endl '\n'
```

Assim, por exemplo, o código `#!c++ for(long long i = 0; i < n; ++i)` pode ser simplificado por `#!c++ FOR(i, 0, n)`.

## Dicas e truques de C++

A seguir, são listados alguns links com dicas e truques de C++ úteis para programação competitiva. Leia **todos** com atenção:

* [C++ tips and tricks](https://codeforces.com/blog/entry/74684)
* [Top 20 C++ Tricks for Competitive Programming](http://www.codingwithart.com/2020/10/top-20-c-tips-and-tricks-for.html)
* [Truques de programação competitiva para programadores de C++](https://neps.academy/br/blog/truques-de-programacao-competitiva-para-programadores-de-c%2B%2B)
