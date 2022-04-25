# Manipulação de Bits

Todos os dados em um programa de computador são internamente armazenados como números binários, ou seja, uma sequencia de 0's ou 1's. Em C++ um número do tipo `int` é uma variável de 32-bits, ou seja, todo número `int` consiste de uma sequencia de 32 0's ou 1's. Por exemplo, a representação do número `int` 43 é:

```c++
00000000000000000000000000101011
```

Normalmente, é usada a representação de bits com sinal de um número, o que significa que números negativos e positivos podem ser representados. Por exemplo, o `int` de C++ é um tipo com sinal, logo uma variável desse tipo pode armazenar valroes inteiros entre $-2^{31}$ e $2^{31} - 1$. O primeiro bit de uma representação com sinal indica o sinal do número (0 para números não-negativos e 1 para números negativos). O **complemento de dois** é usado, o que significa que o oposto de um número é calculado primeiro invertendo todos os bits do número e depois aumentando o número em um. Por exemplo, a representação do número `int` -43 é:

```c++
11111111111111111111111111010101
```

!!! danger "Atenção"
    Se um número for maior que o limite superior da representação de bits, ocorerrá um [*overflow*](https://www.cplusplus.com/articles/DE18T05o/). Considerando uma variável do tipo `int`, o próximo número depois de $2^{31} - 1$ é $-2^{31}$.

    ```c++
    int v = 2147483647; // (1)
    v++; // (2)
    cout << v << "\n"; // -2147483648 (3)
    ```

    1. $01111111111111111111111111111111_2$.
    2. Deveria ser $2147483648_{10}$, mas esse valor não pode ser representado em bits usando uma variável de 32-bits.
    3. $10000000000000000000000000000000_2$ (lembre-se que é utilizado complemento de dois).

## Operadores sobre Bits

### Operador AND (`&` e `&=`)

Os bits são definidos como 1 no resultado, se os bits correspondentes em ambos os operadores forem 1. Exemplos:

```c++
a      = 5  // 00000101
b      = 9  // 00001001
a & b -> 1  // 00000001

c      = 10 // 00001010
d      = 12 // 00001100
c & d -> 8  // 00001000
```

!!! tip "Dica"
    Com o operador `&`, podemos verificar se um número `x` é par usando `x & 1`. De forma geral, `x` é divisível por $2^k$ se $x \& (2^k − 1) = 0$.

### Operador OR inclusivo (`|` e `|=`)

Os bits são definidos como 1 no resultado, se pelo menos um dos bits correspondentes em ambos os operandos for 1. Exemplos:

```c++
a      = 5  // 00000101
b      = 9  // 00001001
a | b -> 13 // 00001101

c      = 10 // 00001010
d      = 12 // 00001100
c | d -> 14 // 00001110
```

### Operador OR exclusivo (`^` e `^=`)

Os bits são definidos como 1 no resultado, se exatamente um dos bits correspondentes em ambos os operandos for 1. Exemplos:

```c++
a      = 5  // 00000101
b      = 9  // 00001001
a ^ b -> 12 // 00001100

c      = 10 // 00001010
d      = 12 // 00001100
c ^ d -> 6  // 00000110
```

### Operador NOT (`~` e `~=`)

Produz um número onde todos os bits são invertidos, ou seja, todos os bits 0 são definidos como 1 e vice-versa. O resultado do operador NOT depende do tamanho da representação do bit, pois a operação inverte todos os bits. Por exemplo, considerando um número do tipo `int` (32-bits), o resultado será:

```c++
 a   =  5 // 00000000 00000000 00000000 00000101
~a   = -6 // 11111111 11111111 11111111 11111010
```

!!! hint "Dica"
    A formula `~x = -x - 1` é válida. Por exemplo, `~5 = 6`.

### Operador de deslocamento à esquerda (`<<` e `<<=`)

Desloca os bits do primeiro operando à esquerda pelo número de bits especificado pelo segundo operando (deve ser um valor positivo): preenche a partir da direita com zero (0). Exemplos:

```c++
a = 1       // 00000001 -> 1
a = a << 1  // 00000010 -> 2
a = a << 1  // 00000100 -> 4
a = a << 1  // 00001000 -> 8
a = a << 1  // 00010000 -> 16

b = 7       // 00000111
b = b << 1  // 00001110 -> 14

c = 7       // 00000111
c <<= 3     // 00111000 -> 56
```

### Operador de deslocamento à direita (`>>` e `>>=`)

Desloca os bits do primeiro operando à direita pelo número de bits especificado pelo segundo operando (deve ser um valor positivo): preenche a partir da esquerda com zero (0). Exemplos:

```c++
a = 16      // 00010001 -> 16
a = a >> 1  // 00001010 -> 8
a = a >> 1  // 00000100 -> 4
a = a >> 1  // 00000010 -> 2
a = a >> 1  // 00000001 -> 1
a = a >> 1  // 00000000 -> 0

b = 56      // 00111000
b = b >> 3  // 00000111 -> 7

c = 56      // 00111000
c >>= 3     // 00000111 -> 7
```

## Funções adicionais

O compilador `g++` fornece as seguintes funções para contar bits:

- `__builtin_clz(x)`: o número de zeros no início do número `int`;
- `__builtin_ctz(x)`: o número de zeros no final do número `int`;
- `__builtin_popcount(x)`: o número de uns no número `int`;;
- `__builtin_parity(x)`: a paridade (par ou ímpar) de uns de um número `int`.;

Veja um exemplo de utilização dessas funções:

``` c++ linenums="1"
int x = 5328; // 00000000000000000001010011010000
cout << __builtin_clz(x) << "\n"; // 19
cout << __builtin_ctz(x) << "\n"; // 4
cout << __builtin_popcount(x) << "\n"; // 5
cout << __builtin_parity(x) << "\n"; // 1
```

!!! note ""
    Embora as funções acima sejam apenas para números `int`, também existem versões das funções que suportam `long` e `long long` bastando adicionar o sufixo `l` ou `ll` no nome das funções.
    
    - `__builtin_clzl(x)` ou `__builtin_clzll(x)`

    - `__builtin_ctzl(x)` ou `__builtin_ctzll(x)`

    - `__builtin_popcountl(x)` ou `__builtin_popcountll(x)`
    
    - `__builtin_parityl(x)` ou `__builtin_parityll(x)`

## Alguns truques e aplicações

Um número da forma `1 << k` tem um bit na posição `k` e todos os outros bits são zero, então esse número pode ser usado para acessar bits únicos de números. Em particular, o $k$-ésimo bit de um número é 1 exatamente quando `x & (1 << k)` não é zero. Por exemplo, para imprimir a representação de bits de um número `int` pode ser usado o seguinte código:

``` c++ linenums="1"
for (int i = 31; i >= 0; i--) {
    if (x&(1<<i)) cout << "1";
    else cout << "0";
}
```

Outras aplicações:

- `x = x | (1 << k)`: define o $k$-ésimo bit de `x` para 1;
- `x = x & ~(1 << k)`: define o $k$-ésimo bit de `x` para 0;
- `x = x ^ (1 << k)`: inverte o $k$-ésimo bit de `x`;
- `x = x & (x − 1)`: define o último bit 1 de `x` como zero;
- `x = x & −x`: define todos os bits 1 como 0, exceto o último bit 1;
- `x = x | (x − 1)`: inverte todos os bits após o último bit 1;
- `x & (x − 1)`: se `x` for um número positivo, verifica se `x` é uma potência de dois.

## std::bitset

[Bitset](https://en.cppreference.com/w/cpp/utility/bitset) é um contêiner da Standard Template Library do C++ que representa uma sequência de tamanho fixo de $N$ bits. Bitsets podem ser manipulados por operadores lógicos padrão e convertidos para inteiros ou strings. Exemplos:

``` c++ linenums="1"
bitset<8> a(42);        // 00101010
bitset<32> b(42);       // 00000000000000000000000000101010
bitset<32> c("110010"); // 00000000000000000000000000110010
int d = c.to_ulong();   // 50

bitset<4> b1("0110");
bitset<4> b2("0011");
cout << "b1 & b2: " << (b1 & b2) << '\n'; // b1 & b2: 0010
cout << "b1 | b2: " << (b1 | b2) << '\n'; // b1 | b2: 0111
cout << "b1 ^ b2: " << (b1 ^ b2) << '\n'; // b1 ^ b2: 0101

bitset<8> e; //00000000
e.set();     //11111111
e.reset();   //00000000
e.set(3);    //00001000
e.set(3, 0); //00000000
e.flip(0);   //00000001
e.flip(1);   //00000011
e.flip(7);   //10000011
e.flip();    //01111100

bitset<8> f{0b01110010};
cout << f << "\n";       //01110010
cout << (f>>=1) << "\n"; //00111001
cout << (f>>=1) << "\n"; //00011100
cout << (f>>=2) << "\n"; //00000111
cout << (f<<=3) << "\n"; //00111000
cout << f.count() << "\n"; // 3
```

Explore mais o contêiner através da [documentação](https://en.cppreference.com/w/cpp/utility/bitset).

## Links adicionais

- :fontawesome-brands-youtube: [Bitwise Operations tutorial #1 | XOR, Shift, Subsets](https://www.youtube.com/watch?v=xXKL9YBWgCY)
- :fontawesome-brands-youtube: [C++ Bitsets in Competitive Programming](https://www.youtube.com/watch?v=jqJ5s077OKo)
- [C Bitwise Operators: AND, OR, XOR, Complement and Shift Operations](https://www.programiz.com/c-programming/bitwise-operators)
- [Bitwise Operators in C/C++ - GeeksforGeeks](https://www.geeksforgeeks.org/bitwise-operators-in-c-cpp/)
- [Bits manipulation (Important tactics) - GeeksforGeeks](https://www.geeksforgeeks.org/bits-manipulation-important-tactics/)
- [Bitwise Hacks for Competitive Programming - GeeksforGeeks](https://www.geeksforgeeks.org/bitwise-hacks-for-competitive-programming/)
- [Bit Tricks for Competitive Programming - GeeksforGeeks](https://www.geeksforgeeks.org/bit-tricks-competitive-programming/)
- [Bitwise Algorithms - GeeksforGeeks](https://www.geeksforgeeks.org/bitwise-algorithms/)
- [Builtin functions of GCC compiler - GeeksforGeeks](https://www.geeksforgeeks.org/builtin-functions-gcc-compiler/)
- [Bit Manipulation | HackerEarth](https://www.hackerearth.com/practice/notes/bit-manipulation/ )
- [Manipulação de Bits | Neps Academy](https://neps.academy/br/course/matematica-computacional-(codcad)/lesson/manipulacao-de-bits )
- [C++ bitset and its application](https://www.geeksforgeeks.org/c-bitset-and-its-application/)
