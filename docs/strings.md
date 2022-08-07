# Processamento de Strings

A resolução de problemas envolvendo cadeia de caracteres (ou simplesmente *strings*) se tornou um importante domínio no estudo de algoritmos. Estes problemas ocorrem, principalmente, no processamento de texto, como verificação ortográfica e busca de substrings específicos ou de padrões mais gerais (*pattern matching*). Por exemplo, o padrão `ABC` ocorre duas vezes na string `ABABCBABC`.

Nesta seção, diferente das outras, por simplicidade alguns algoritmos serão implementados (também) em Python.

## Terminologia

- Uma **substring** é uma sequência de caracteres consecutivos em uma string. Usa-se a notação `s[a...b]` para se referir a uma substring de `s` que começa na posição `a` e termina na posição `b`. Uma string de tamanho $n$ tem $n ( n + 1)/2$ substrings. Por exemplo, as substrings de `ABCD` são `A`, `B`, `C`, `D`, `AB`, `BC`, `CD`, `ABC`, `BCD` e `ABCD`.

- Uma **subsequência** é uma sequência de caracteres (não necessariamente consecutivos) em uma string em sua ordem original. Uma string de tamanho $n$ tem $2^n − 1$ subsequências. Por exemplo, as subsequências de `ABCD` são `A`, `B`, `C`, `D`, `AB`, `AC`, `AD`, `BC`, `BD`, `CD`, `ABC`, `ABD`, `ACD`, `BCD` e `ABCD`.

- Um **prefixo** é uma substring que começa no início de uma string e um **sufixo** é uma substring que termina no final de uma string. Por exemplo, os prefixos de `ABCD` são `A`, `AB`, `ABC` e `ABCD`, e os sufixos de `ABCD` são `D`, `CD`, `BCD` e `ABCD`.

- Uma string $w$ é um **anagrama** de uma string $v$ se existir uma permutação das caracteres que transformam $w$ em $v$. Por exemplo, os anagramas de `alegria` são `alergia`, `alegrai`, `alergia`, `regalia`,`galeria`. O código abaixo ilustra uma forma de gerar todos os anagramas de uma lista de palavras.

```python linenums="1"
def anagrama(palavras):
    d = {} # dicionário com a 'assinatura' das palavras
    for palavra in palavras:
        # agrupa as palavras de acordo com a assinatura 
        s = ''.join(sorted(palavra)) # calcula a assinatura da palavra
        if s in d:
            d[s].append(palavra) # adiciona a palavra a uma assinatura já existente
        else:
            d[s] = [palavra] # cria uma nova assinatura e adiciona sua primeira palavra
    # retorna os anagramas, ignorando grupos de tamanho 1
    return [d[s] for s in d if len(d[s]) > 1]
```

## Expressão Regular

Alguns problemas de processamento de string são facilmente resolvidos usando expressão regular (Regex). Tanto [C++](https://en.cppreference.com/w/cpp/regex) quanto [Python](https://docs.python.org/3/library/re.html) possuem bibliotecas para lidar com expressões regulares.

Material complementar:

- [Regular Expression Basics in C++](https://linuxhint.com/regular-expression-basics-cpp/)
- [Regular Expressions: Regexes in Python (Part 1)](https://realpython.com/regex-python/)
- [Regular Expressions: Regexes in Python (Part 2)](https://realpython.com/regex-python-part-2/)
- [Expressões Regulares](https://ic.unicamp.br/~mc102/aulas/aula15.pdf)

## Processamento de String com Programação Dinâmica

Dois problemas clássicos de processamento de string que usam programação dinâmica são alinhamento de string (distância de edição) e maior subsequência comum.

### Distância de Edição

O problema [Distância de Edição](https://en.wikipedia.org/wiki/Levenshtein_distance) (também conhecido como Alinhamento de String ou *Levenshtein Distance*) é definido da seguinte forma: Dadas duas strings $x$ e $y$, deseja-se saber o mínimo de operações (inserção, exclusão ou substituição) que são necessárias para transformar $x$ em $y$. Por exemplo, a distância de edição entre as palavras inglesas `kitten` e `sitting` é 3, já que com apenas 3 edições conseguimos transformar uma palavra na outra e não há maneira de o fazer com menos de três edições:

1. kitten
2. sitten (substituição de 'k' por 's')
3. sittin (substituição de 'e' por 'i')
4. sitting (inserção de 'g' no final)

Uma primeira estratégia (ingênua) é tentar, recursivamente, todas as possibilidades (inserir, remover, substituir):

$$
D(a, b, n, m) = \begin{cases}
                n, & \text{se $m = 0$}\\
                m, & \text{se $n = 0$}\\
                D(a, b, n - 1, m - 1) & \text{se $a[n - 1] = b[m - 1]$}\\
                1 + \min \begin{cases}
                            D(a, b, n, m - 1) \\
                            D(a, b, n - 1, m) \\
                            D(a, b, n - 1, m - 1)
                         \end{cases} & \text{caso contrário.}
                \end{cases}
$$

Um código que implementa esta relação de recorrência terá complexidade $O(3^m)$. O pior caso acontece quando nenhum dos caracteres de duas strings são iguais. Neste caso, tem-se varias sobreposições de subproblemas. Assim, usar programação dinâmica é o melhor caminho. O código abaixo (extraida [daqui](https://www.geeksforgeeks.org/edit-distance-dp-5/)) mostra uma implementação do algoritmo de distância de edição usando programação dinâmica. A complexidade do código é $O(nm)$.

=== "C++"

    ``` c++ linenums="1"
    int min(int x, int y, int z) { return min(min(x, y), z); }
    int distanciaEdicao(string &a, string &b) {
        int n = a.size(), m = b.size();
        int dp[n+1][m+1];

        for (int i=0; i<=n; i++)
            dp[i][0] = i;
        for (int j=0; j<=m; j++)
            dp[0][j] = j;

        for (int i=1; i<=n; i++) {
            for (int j=1; j<=m; j++) {
                if (a[i-1] != b[j-1]) {
                    dp[i][j] = 1 + min(dp[i-1][j],    // Remover
                                       dp[i][j-1],    // Inserir
                                       dp[i-1][j-1]); // Substituir
                }
                else
                    dp[i][j] = dp[i-1][j-1];
            }
        }
        return dp[n][m];
    }
    ```

=== "Python"

    ``` python linenums="1"
    def distanciaEdicao(a, b):
        n = len(a)
        m = len(b)
        dp = [[0 for x in range(m + 1)] for x in range(n + 1)]
        for i in range(n + 1):
            for j in range(m + 1):
                if i == 0:
                    dp[i][j] = j
                elif j == 0:
                    dp[i][j] = i
                elif a[i-1] != b[j-1]:
                    dp[i][j] = 1 + min(dp[i-1][j],    # Remover
                                    dp[i][j-1],    # Inserir
                                    dp[i-1][j-1])  # Substituir   
                else:
                    dp[i][j] = dp[i-1][j-1]
        return dp[n][m]
    ```

Os links abaixo são fundamentais para entender melhor como o algoritmo funciona.

- [Edit Distance (GeeksforGeeks)](https://www.geeksforgeeks.org/edit-distance-dp-5/)
- [Definion of Minimum Edit Distance](https://web.stanford.edu/class/cs124/lec/med.pdf)

### Maior Subsequência Comum

O problema da **Maior Subsequência Comum** (*Longest Common Subsequence* - LCS) é definido da seguinte forma: Dadas duas strings $A$ e $B$, qual é a subsequência comum mais longa entre elas. Lembrando que uma subsequência é uma sequência que aparece na mesma ordem relativa, mas não necessariamente consecutiva. Por exemplo, `#!c++ A = "ACAATCC"` e `#!c++ B = "AGCATGC"` têm LCS de comprimento 5, ou seja, `#!c++ "ACATC"`.

O código abaixo (extraida [daqui](https://www.geeksforgeeks.org/longest-common-subsequence-dp-4/)) mostra uma implementação do algoritmo para encontrar o tamanho da maior subsequência comum usando programação dinâmica. A complexidade do código é $O(nm)$.

=== "C++"

    ``` c++ linenums="1"
    int lcs(string &a, string &b) {
        int n = a.size(), m = b.size();
        int dp[n+1][m+1] = {0};
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (a[i] == b[j])
                    dp[i + 1][j + 1] = dp[i][j] + 1;
                else
                    dp[i + 1][j + 1] = max(dp[i][j + 1], dp[i + 1][j]);
                    
            }
        }
        return dp[n][m]; // O código em Python mostra como gerar a string contendo o LCS.
    }
    ```
=== "Python"

    ``` python linenums="1"
    def lcs(a, b):
        n = len(a)
        m = len(b)
        dp = [[0 for x in range(m + 1)] for x in range(n + 1)]
        for i in range(n):
            for j in range(m):
                if a[i] == b[j]:
                    dp[i + 1][j + 1] = dp[i][j] + 1
                else:
                    dp[i + 1][j + 1] = max(dp[i][j + 1], dp[i + 1][j])    
        # Gera a solução. 
        # Caso precise apenas do tamanho do string, basta retornar dp[n][m]
        sol = []
        i, j = n, m
        while dp[i][j] > 0:
            if dp[i][j] == dp[i - 1][j]:
                i -= 1
            elif dp[i][j] == dp[i][j - 1]:
                j-= 1
            else:
                i -= 1
                j -= 1
                sol.append(a[i])
        return "".join(sol[::-1])
    ```

Os links abaixo são fundamentais para entender melhor como o algoritmo funciona.

- [Longest Common Subsequence (GeeksforGeeks)](https://www.geeksforgeeks.org/longest-common-subsequence-dp-4/)
- [Longest common subsequence](https://algorithmist.com/wiki/Longest_common_subsequence)

## Busca em String (String Matching)

Busca em string (também conhecido como *string matching*) é um problema de encontrar o indice (ou indices) de uma dada (sub)string (chamada de padrão `P`) de uma string (chamada de texto `T`). Por exemplo, considerando que `#!c++ T = "STEVEN EVENT"`. Se `#!c++ P = "EVE"`, então as respostas são os indices 2 e 7. Se `#!c++ P = "EVENT"`, então a resposta é apenas o índice 7. Se `#!c++ P = "EVENING"`, então o algoritmo deve retornar que não encontrou o padrão `P` no texto `T`.

Um algoritmo ingênuo (veja abaixo) consegue resolver esse problema com complexidade $O(nm)$, onde $n$ é o tamanho do texto `T` e $m$ o tamanho de padrão `P`.

``` c++ linenums="1"
void busca(string &T, string &P) {
    int n = T.size(), m = P.size();
    for (int i = 0; i < n - m; ++i) {
        bool achou = true;
        for (int j = 0; j < m; ++j)
            if (T[i + j] != P[j]) {
                achou = false;
                break;
            }
        if (achou) 
            cout << "P encontrado no indice " << i << endl;
    }
}
```

Com o intuito de melhorar a complexidade do algoritmo anterior, [Knuth, Morris e Pratt](https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm) propuseram um algoritmo mais eficiente (chamado de *KMP Algorithm*) que faz uso das informações obtidas nas comparações dos caracteres anteriores, especialmente aqueles que são iguais. Este [link](http://jovilab.sinaapp.com/visualization/algorithms/strings/kmp) mostra uma animação do funcionamento do Algoritmo KMP. Você pode encontrar a implementação e a explicação detalhada do algoritmo KMP [aqui](https://www.geeksforgeeks.org/kmp-algorithm-for-pattern-searching/), [aqui](https://usaco.guide/PAPS.pdf#page=249) e [aqui](https://usaco.guide/CP2.pdf#page=172).

Os links abaixo detalham mais sobre busca em string e explicam outros algoritmos não descritos aqui.

- ⭐️ [KMP Algorithm for Pattern Searching (GeeksforGeeks)](https://www.geeksforgeeks.org/kmp-algorithm-for-pattern-searching/)
- ⭐️ [Strings](https://github.com/SuprDewd/T-414-AFLV/blob/master/11_strings/aflv_11_strings.pdf)
- ⭐️ [Applications of String Matching Algorithms (GeeksforGeeks)](https://www.geeksforgeeks.org/applications-of-string-matching-algorithms/)
- ⭐️ [String Matching](https://usaco.guide/PAPS.pdf#page=249)
- ⭐️ [Algoritmo KMP para busca de substring](https://www.ime.usp.br/~pf/estruturas-de-dados/aulas/kmp.html)
- [Aho-Corasick Algorithm for Pattern Searching (GeeksforGeeks)](https://www.geeksforgeeks.org/aho-corasick-algorithm-pattern-searching/)
- [Rabin-Karp Algorithm for Pattern Searching (GeeksforGeeks)](https://www.geeksforgeeks.org/rabin-karp-algorithm-for-pattern-searching/)
- [Prefix function. Knuth–Morris–Pratt algorithm](https://cp-algorithms.com/string/prefix-function.html)
- [String-searching algorithm](https://en.wikipedia.org/wiki/String-searching_algorithm)
- [Trie (GeeksforGeeks)](https://www.geeksforgeeks.org/trie-insert-and-search/)

## String Hashing

Em construção...

## Array de Sufixo

Basicamente, array de sufixo (*Suffix Array*) é um array que contém inteiros indicando os índices iniciais dos sufixos de uma determinada string, após os sufixos serem ordenados. Lembrando que o $i$-ésimo sufixo de uma string $s$ de tamanho $n$ é a substring $s[i…n−1]$. Por exemplo, os sufixos de `#!c++ S = "banana"` são:

$$
\begin{array}{|c|l|}
\hline
i & \textbf{sufixo} \\ \hline
0 & banana \\
1 & anana  \\
2 & nana   \\
3 & ana    \\
4 & na     \\
5 & a \\ \hline
\end{array}
$$

Após de ordenar essas strings, tem-se:

$$
\begin{array}{|c|l|}
\hline
i & \textbf{sufixo} \\ \hline
5 & a \\
3 & ana \\
1 & anana   \\
0 & banana   \\
4 & na \\
2 & nana \\ \hline
\end{array}
$$

Portanto, o array de sufixos de $S$ será $[5, 3, 1, 0, 4, 2]$.

Um método simples para construir um array de sufixos é fazer um array de todos os sufixos e então ordenar o array. O código abaixo ilustra este procedimento.

=== "C++"

    ``` c++ linenums="1"
    char S[] = "banana";
    ll n = strlen(S);
    vi SA(n);
    iota(SA.begin(), SA.end(), 0);
    sort(SA.begin(), SA.end(), [&](int a, int b) { // o & permite o acesso a variável externa S
        return strcmp(S + a, S + b) < 0;
    });
    for(auto i: SA)
        cout << i << endl;
    ```

=== "Python"

    ``` python linenums="1"
    S = "banana"
    SA = [(i, S[i:]) for i in range(len(S))]
    SA.sort(key = lambda x: x[1])
    print(SA)
    ```
A ordenação possui complexidade $O(n \log ⁡n)$ e como comparar duas strings é $O(n)$, a complexidade final do algoritmo será $O(n^2 \log ⁡n)$. Pode-se melhor esta complexidade alterando o método de ordenação (usando [Radix Sort](https://www.geeksforgeeks.org/radix-sort/), por exemplo). Essa versão otimizada terá complexidade $O(n \log n)$. Você pode encontrar a explicação e implementação [aqui](https://www.geeksforgeeks.org/suffix-array-set-2-a-nlognlogn-algorithm/), [aqui](https://usaco.guide/CP2.pdf#page=185) e [aqui](https://cp-algorithms.com/string/suffix-array.html#on-log-n-approach).

Os links abaixo exemplificam várias aplicações do array de sufixo:

- [Applications of Suffix Array](https://usaco.guide/CP2.pdf#page=186)
- [Suffix Array - Applications](https://cp-algorithms.com/string/suffix-array.html#applications)
- [Suffix arrays – Applications in contest problems](https://web.stanford.edu/class/cs97si/suffix-array.pdf#page=7)

Material Complementar:

- ⭐️ [Suffix Array](https://codeforces.com/edu/course/2/lesson/2) 🤯
- ⭐️ [Array de Sufixos](https://www.youtube.com/watch?v=YkUu6azN4BI)
- ⭐️ [Suffix Array](https://www.geeksforgeeks.org/suffix-array-set-1-introduction/)
- [Suffix Array](https://cp-algorithms.com/string/suffix-array.html)
- [Suffix Array (TopCoder)](https://www.topcoder.com/thrive/articles/suffix-array-lcp-and-finding-unique-substring)
- [Suffix arrays - a programming contest approach](https://web.stanford.edu/class/cs97si/suffix-array.pdf)

## Palindromo

Palíndromos são palavras ou frases que podem ser lidas da esquerda para a direita ou da direita para a esquerda. Por exemplo, `ovo`, `radar`, `123321`, `a base do teto desaba`, `Socorram-me, subi no ônibus em Marrocos`, `anotaram a data da maratona`.

=== "C++"

    ``` c++ linenums="1"
    bool isPalindrome(string &s, ll l, ll r) {
        ll n = (r - l) + 1;
        FOR(i, 0, n/2)
            if (s[l+i] != s[r-i]) 
                return false;
        return true;
    }
    ```

=== "Python"

    ``` python linenums="1"
    def isPalindrome(s, l, r):
        n = (r - l) + 1
        for i in range(n//2):
            if s[l+i] != s[r-i]:
                return False;
        return True
    #Ou usando fatiamento
    def isPalindrome(s, l, r):
        return s[l:r+1] == s[l:r+1:-1]
    ```

Uma variante comum de problemas de palíndromos envolve contar o número de substrings de uma string $s$ que são palíndromos. Uma solução ingênua (mostrada abaixo) faz uma busca completa com complexidade $O(n^3)$.

=== "C++"

    ``` c++ linenums="1"
    int countPalindrome(string &s) {
        ll n = s.size(), resp = 0;
        for (int i = 0; i < n; ++i)
            for (int j = i + 1; j < n; ++j)
                if (isPalindrome(s, i, j)) 
                    ++resp
        return resp;
    }
    ```

=== "Python"

    ``` python linenums="1"
    def countPalindrome(s):
        n = len(s)
        resp = 0
        for i in range(n):
            for j in range(i + 1, n):
                if isPalindrome(s, i, j):
                    resp += 1
        return resp
    ```

Perceba que muitos subproblemas (substrings) serão sobrepostos. Assim, pode-se criar uma matriz para armazenar o substring $s[l..r]$ de forma que este substring só seja computado uma vez. Desta forma, teremos uma solução que usa programação dinâmica com complexidade $O(n^2)$. Os códigos abaixo (extraido [daqui](https://www.geeksforgeeks.org/count-palindrome-sub-strings-string/)) ilustram esta estratégia.

=== "C++"

    ``` c++ linenums="1"
    int dp[1001][1001];
    bool isPalindrome(string &s, ll i, ll j) {
        if (i > j)
            return 1;
        if (dp[i][j] != -1)
            return dp[i][j];
        if (s[i] != s[j])
            return dp[i][j] = 0;
        return dp[i][j] = isPalindrome(s, i + 1, j - 1);
    }
    int countPalindromes(string &s) {
        ll n = s.size(), resp = 0;
        memset(dp, -1, sizeof(dp));
        for (int i = 0; i < n; ++i)
            for (int j = i + 1; j < n; ++j)
                if (isPalindrome(s, i, j)) 
                    ++resp
        return resp;
    }
    ```

=== "Python"

    ``` python linenums="1"
    dp = [[-1 for i in range(1001)] for j in range(1001)]
    def isPalindrome(s, i, j):
        if i > j:
            return 1
        if dp[i][j] != -1:
            return dp[i][j]
        dp[i][j] = 0
        if s[i] == s[j]:
            dp[i][j] = isPalindrome(s, i + 1, j - 1)
        return dp[i][j]
    def countPalindromes(s):
        n = len(s)
        resp = 0
        for i in range(n):
            for j in range(i + 1, n):
                if isPalindrome(s, i, j):
                    resp += 1
        return resp
    ```

Outro problema comum é encontrar o maior palindromo que pode ser gerado deletando zero ou mais caracteres de uma string. Por exemplo: `#!c++ "ADAM" -> "ADA"` (tamanho 3, deletando `#!c++ "M"`), `#!c++ "RACEF1CARFAST" -> "RACECAR"` (tamanho 7, deletando `#!c++ "F1"` e `#!c++ "FAST"`). Novamente, a solução desse problema pode ser obtida usando programação dinâmica:

- Seja $len(l, r)$ o tamanho do maior palindromo da string $S[l..r]$.
- Casos bases:
    * Se $l = r$, então $len(l, r) = 1$;
    * Se $l + 1 = r$, então $len(l, r) = 2$ se $S[l] = S[r]$ ou 1, caso contrário.
- Recorrencia:
    * Se $S[l] = S[r]$, então $len(l, r) = 2 + len(l + 1, r - 1)$;
    * Senão, $len(l, r) = \max(len(l, r - 1), len(l + 1, r))$

Material Complementar:

- [Manacher's Algorithm](https://www.hackerrank.com/topics/manachers-algorithm)
- [Manacher's Algorithm - Finding all sub-palindromes in $O(n)$](https://cp-algorithms.com/string/manacher.html)
- [Manacher's algorithm and code readability](https://codeforces.com/blog/entry/12143)
- [Palindromic tree: behind the scenes](https://codeforces.com/blog/entry/13959)
- [Palindromic tree](http://adilet.org/blog/palindromic-tree/)
