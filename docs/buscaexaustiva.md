# Busca Exaustiva (Recursão + Backtracking)[^1]

[^1]: Os textos dessa página são traduções e adaptações encontrados nesse link: [1](https://usaco.guide/CPH.pdf)

Uma **busca exaustiva** ou **busca completa** tem por objetivo gerar todas as soluções possíveis para um determinado problema usando **força bruta** e então selecionar a melhor solução ou contar o número de soluções, dependendo do problema.

A busca exaustiva é uma boa técnica se houver tempo suficiente para gerar todas as soluções, pois geralmente é fácil de implementar e sempre fornece a resposta correta. Entretanto, se o número de soluções for muito grande ou a geração das soluções for demorada, outras técnicas, como **algoritmos gulosos** ou **programação dinâmica**, podem ser necessárias.

## Gerando subconjuntos

Nesse problema, deseja-se gerar todos os subconjuntos de $n$ elementos ou de um conjunto específico. Por exemplo, os subconjuntos de $\{0, 1, 2\}$ são: $\emptyset$, $\{0\}$, $\{1\}$, $\{2\}$, $\{0,1\}$, $\{0,2\}$, $\{1,2\}$ e $\{0,1,2\}$. Existem dois métodos comuns para gerar subconjuntos: realizar uma busca recursiva ou explorar a representação de bits de inteiros.

**Método 1**

Uma maneira elegante de gerar todos os subconjuntos de um conjunto é usar recursão. A função abaixo gera os subconjuntos do conjunto $\{0,1,\dots ,n - 1\}$. A função utiliza um vetor booleano para definir se o elemento $i$ pertence ou não ao subconjunto.

```c++ linenums="1"
typedef long long ll;
vector<bool> subconj;

void imprime(ll n) {
    cout << "{ ";
    for(ll i = 0; i < n; i++) 
        if(subconj[i]) cout << i << " ";
    cout << "}\n";
}

void geraSubconjuntos(ll n, ll i = 0) {
    if (i == n) {
        imprime(n);
        return;
    }

    subconj[i] = false; // Elemento i não está no subconjunto
    geraSubconjuntos(n, i + 1);
    subconj[i] = true; // Elemento i está no subconjunto
    geraSubconjuntos(n, i + 1);
}

int main() {
    ll n;
    cin >> n;
    subconj.resize(n + 1, false);
    geraSubconjuntos(n);
    return 0;
}
```

**Método 2**

Outra maneira de gerar subconjuntos é baseada na representação de bits de inteiros. Cada subconjunto de um conjunto de $n$ elementos pode ser representado como uma sequência de $n$ bits, que corresponde a um inteiro entre $0 \dots 2^n −1$. Os bits 1 da sequência de bits indicam quais elementos estão incluídos no subconjunto. A convenção usual é que o último bit corresponde ao elemento $0$, o penúltimo bit corresponde ao elemento $1$ e assim por diante. Por exemplo, a representação de bit de $25$ é $11001$, que corresponde ao subconjunto $\{0,3,4\}$.

O código abaixo gera os subconjuntos de um conjunto de $n$ elementos baseado nesse método.

```c++ linenums="1"
void imprime(vector<ll> subconj) {
    cout << "{ ";
    for(auto i: subconj)
        cout << i << " ";
    cout << "}\n";
}

void geraSubconjuntos(ll n) {
    for (ll b = 0; b < (1<<n); b++) {
        vector<ll> subconj;
        for (ll i = 0; i < n; i++) {
            if (b&(1<<i)) subconj.push_back(i);
        }
        imprime(subconj);
    }
}

int main() {
    ll n;
    cin >> n;
    geraSubconjuntos(n);
    return 0;
}
```

### Exemplos

- Gerar todos os subconjuntos de um conjunto de valores lidos

```c++ linenums="1"
typedef long long ll;

vector<bool> subconj;
vector<ll> valores;

void imprime(ll n) {
    cout << "{ ";
    for(ll i = 0; i < n; i++) 
        if(subconj[i]) cout << valores[i] << " ";
    cout << "}\n";
}

void geraSubconjuntos(ll n, ll i = 0) {
    if (i == n) {
        imprime(n);
        return;
    }

    subconj[i] = false; // Elemento i não está no subconjunto
    geraSubconjuntos(n, i + 1);
    subconj[i] = true; // Elemento i está no subconjunto
    geraSubconjuntos(n, i + 1);
}

int main() {
    ll n;
    cin >> n;
    subconj.resize(n + 1, false);
    valores.resize(n);
    for(ll i = 0; i < n; i++)
        cin >> valores[i];
    geraSubconjuntos(n);
    return 0;
}
```

- Gerar todos os subconjuntos de um conjunto de valores lidos cuja soma seja menor ou igual a um limitante

```c++ linenums="1"
typedef long long   ll;

vector<bool> subconj;
vector<ll> valores;
ll limite;

void imprime(ll n) {
    cout << "{ ";
    for(ll i = 0; i < n; i++) 
        if(subconj[i]) cout << valores[i] << " ";
    cout << "}\n";
}

ll soma(ll n) {
    ll s = 0;
    for (ll i = 0; i < n; i++)
        if (subconj[i])
            s += valores[i];
    return s;
}

void geraSubconjuntos(ll n, ll i = 0) {
    if (i == n) {
        if (soma(n) <= limite)
            imprime(n);
        return;
    }

    subconj[i] = true; // Elemento i está no subconjunto
    geraSubconjuntos(n, i + 1);
    subconj[i] = false; // Elemento i não está no subconjunto
    geraSubconjuntos(n, i + 1);
}

int main() {
    ll n;
    cin >> n;
    cin >> limite;
    subconj.resize(n + 1, false);
    valores.resize(n);
    for(ll i = 0; i < n; i++)
        cin >> valores[i];

    geraSubconjuntos(n);

    return 0;
}
```

- Mesmo que o anterior, mas "corta" o *backtracking* assim que chega em um subconjunto cuja `soma` seja maior que o `limite`. Assim, não espera terminar de gerar o subconjunto para testar a soma, por isso é bem mais rápido.

```c++ linenums="1"
typedef long long ll;

vector<bool> subconj;
vector<ll> valores;
ll limite;

void imprime(ll n) {
    cout << "{ ";
    for(ll i = 0; i < n; i++) 
        if(subconj[i]) cout << valores[i] << " ";
    cout << "}\n";
}

ll soma(ll n) {
    ll s = 0;
    for (ll i = 0; i < n; i++)
        if (subconj[i])
            s += valores[i];
    return s;
}

void geraSubconjuntos(ll n, ll i = 0) {
    if (soma(n) > limite)
        return;

    if (i == n) {
        imprime(n);
        return;
    }

    subconj[i] = true; // Elemento i está no subconjunto
    geraSubconjuntos(n, i + 1);
    subconj[i] = false; // Elemento i não está no subconjunto
    geraSubconjuntos(n, i + 1);
}

int main() {

    ll n;
    cin >> n;
    cin >> limite;
    subconj.resize(n + 1, false);
    valores.resize(n);
    for(ll i = 0; i < n; i++)
        cin >> valores[i];

    geraSubconjuntos(n);

    return 0;
}
```

- Mesmo que o anterior, mas não recalcula a soma dos elementos (chamada da função `soma` que é $O(n)$).

```c++ linenums="1"
typedef long long ll;

vector<bool> subconj;
vector<ll> valores;
ll limite;

void imprime(ll n) {
    cout << "{ ";
    for(ll i = 0; i < n; i++) 
        if(subconj[i]) cout << valores[i] << " ";
    cout << "}\n";
}

void geraSubconjuntos(ll n, ll i = 0, ll soma = 0 ) {
    if (soma > limite)
        return;

    if (i == n) {
        imprime(n);
        return;
    }

    subconj[i] = true; // Elemento i está no subconjunto
    geraSubconjuntos(n, i + 1, soma + valores[i]);
    subconj[i] = false; // Elemento i não está no subconjunto
    geraSubconjuntos(n, i + 1, soma);
}

int main() {

    ll n;
    cin >> n;
    cin >> limite;
    subconj.resize(n + 1, false);
    valores.resize(n);
    for(ll i = 0; i < n; i++)
        cin >> valores[i];

    geraSubconjuntos(n);

    return 0;
}
```

## Gerando permutações

Considere agora o problema de gerar todas as permutações de um conjunto de $n$ elementos. Por exemplo, as permutações de $\{0,1,2\}$ são $(0,1,2)$, $(0,2,1)$, $(1,0,2)$, $(1,2,0)$, $(2,0 ,1$) e $(2,1,0)$. Novamente, existem duas abordagens: usar a recursão ou percorrer as permutações iterativamente.

**Método 1**

Assim como no problema de gerar os subconjuntos, as permutações também podem ser geradas usando recursão.

```c++ linenums="1"
typedef long long ll;

vector<ll> permutacao;
vector<bool> pertence;

void imprime(ll n) {
    cout << "( ";
    for(ll i = 0; i < n; i++) 
        cout << permutacao[i] << " ";
    cout << ")\n";
}

void geraPermutacao(ll n) {
    if (permutacao.size() == n) {
        imprime(n);
        return;
    }

    for (ll i = 0; i < n; i++) {
        if(pertence[i]) 
            continue;
        pertence[i] = true;      // Inclui o elemento i na permutação
        permutacao.push_back(i);
        geraPermutacao(n);       // Gera o restante da permutação

        pertence[i] = false;     // Remove o elemento i da permutação
        permutacao.pop_back();
    }
}

int main() {

    ll n;
    cin >> n;
    pertence.resize(n, false);
    geraPermutacao(n);

    return 0;
}
```

**Método 2**

Outra alternativa é usar a função da biblioteca padrão do C++ [`next_permutation`](https://en.cppreference.com/w/cpp/algorithm/next_permutation), que a cada chamada constrói a próxima permutação em ordem crescente.

```c++ linenums="1"
typedef long long ll;

vector<ll> permutacao;

void imprime() {
    cout << "( ";
    for(auto x: permutacao) 
        cout << x << " ";
    cout << ")\n";
}

int main() {

    ll n;
    cin >> n;
    permutacao.resize(n, false);
    iota(permutacao.begin(), permutacao.end(), 0); // (1)
    do {
        //Processa a permutação
        imprime();
    } while (next_permutation(permutacao.begin(),permutacao.end()));

    return 0;
}
```

1. [std:iota](https://en.cppreference.com/w/cpp/algorithm/iota)

### Exemplos

- Gera todas as permutações de a partir de valores lidos

```c++ linenums="1"
typedef long long ll;

vector<ll> permutacao, valores;
vector<bool> pertence;

void imprime(ll n) {
    cout << "( ";
    for(ll i = 0; i < n; i++) 
        cout << valores[permutacao[i]] << " ";
    cout << ")\n";
}

void geraPermutacao(ll n) {
    if (permutacao.size() == n) {
        imprime(n);
        return;
    }

    for (ll i = 0; i < n; i++) {
        if(pertence[i]) 
            continue;
        pertence[i] = true;      // Inclui o elemento i na permutação
        permutacao.push_back(i);
        geraPermutacao(n);       // Gera o restante da permutação

        pertence[i] = false;     // Remove o elemento i da permutação
        permutacao.pop_back();
    }
}

int main() {

    ll n;
    cin >> n;
    pertence.resize(n, false);
    valores.resize(n);
    for (ll i = 0; i < n; i++)
        cin >> valores[i];
    geraPermutacao(n);

    return 0;
}
```

- Gera todas as permutações de ${0, 1, 2, 3, \dots, n-1}$ em que 2 e 3 não aparecem seguidos.

```c++ linenums="1"
typedef long long ll;

vector<ll> permutacao;
vector<bool> pertence;

void imprime(ll n) {
    cout << "( ";
    for(ll i = 0; i < n; i++) 
        cout << permutacao[i] << " ";
    cout << ")\n";
}

bool seguidos() {
    for (ll i = 0; i < permutacao.size(); i++)
        if (permutacao[i] == 2 && permutacao[i + 1] == 3)
            return true;
    return false;
}

void geraPermutacao(ll n) {
    if (permutacao.size() == n) {
        if (!seguidos())
            imprime(n);
        return;
    }

    for (ll i = 0; i < n; i++) {
        if(pertence[i]) 
            continue;
        pertence[i] = true;      // Inclui o elemento i na permutação
        permutacao.push_back(i);
        geraPermutacao(n);       // Gera o restante da permutação

        pertence[i] = false;     // Remove o elemento i da permutação
        permutacao.pop_back();
    }
}

int main() {

    ll n;
    cin >> n;
    pertence.resize(n, false);

    geraPermutacao(n);

    return 0;
}
```

- Mesmo que o anterior, mas corta o backtracking tão logo 2 e 3 apareçam seguidos

```c++ linenums="1"
typedef long long ll;

vector<ll> permutacao;
vector<bool> pertence;

void imprime(ll n) {
    cout << "( ";
    for(ll i = 0; i < n; i++) 
        cout << permutacao[i] << " ";
    cout << ")\n";
}

bool seguidos() {
    for (ll i = 0; i < permutacao.size(); i++)
        if (permutacao[i] == 2 && permutacao[i + 1] == 3)
            return true;
    return false;
}

void geraPermutacao(ll n) {
    if (seguidos()) 
        return;

    if (permutacao.size() == n) {
        imprime(n);
        return;
    }

    for (ll i = 0; i < n; i++) {
        if(pertence[i]) 
            continue;
        pertence[i] = true;      // Inclui o elemento i na permutação
        permutacao.push_back(i);
        geraPermutacao(n);       // Gera o restante da permutação

        pertence[i] = false;     // Remove o elemento i da permutação
        permutacao.pop_back();
    }
}

int main() {

    ll n;
    cin >> n;
    pertence.resize(n, false);

    geraPermutacao(n);

    return 0;
}
```

## Backtracking

**Backtracking** (também chamado de **tentativa e erro** ou **força bruta**) é uma técnica que usa recursividade para gerar/enumerar/percorrer sistematicamente todas as alternativas no espaço de soluções. O método constrói soluções candidatas incrementalmente e abandona (*backtrack*) uma solução parcial quando identifica que tal solução parcial não levará a uma solução do problema. Como dito, é um método de tentativa e erro: tenta um caminho, se não der certo, volta e tenta outro. Assim, a ideia por trás de um algoritmo backtracking é que ele busca uma solução para um problema entre **todas** as opções disponíveis.

<!-- construir uma solução de um problema de forma incremental, removendo as soluções que não satisfaçam as restrições do problema. Sempre que uma solução inválida é encontrada o algoritmo retrocede e continua.  -->

Existem três tipos de problemas que são comumente resolvidos com Backtracking:

1. Problema de Decisão – Neste, busca-se uma solução viável.
2. Problema de Otimização – Neste, deseja-se a melhor solução.
3. Problema de Enumeração – Neste, busca-se encontrar todas as soluções viáveis.

**Algoritmo Geral**

O código abaixo ilustra a ideia geral de um algoritmo backtracking. Note que essa ideia foi usada nos algoritmos para gerar [subconjuntos](#gerando-subconjuntos) e [permutações](#gerando-subconjuntos)

```c++ linenums="1"
// int a[] - solução parcial, de a[0] até a[k-1]
// int k - posição para inserir o próximo passo
// int n - tamanho máximo da solução
void backtrack(int a[], int k, int n) {
    // É importante cortar a busca tão logo se descubra que não levará a uma solução
    if ( /* não há como continuar */ )    // Corte
        return;

    if ( /* terminou */) {                // se chegou numa possível solução
        /* processa solucao */
        return;
    }
    for ( /* toda alternativa */ )        // para toda alternativa
        if ( /* alternativa viável */ ) { // se pode incluí-la
            a[k] = /* alternativa */;     // inclui na solução parcial
            backtrack(a, k+1, n);         // gera o restante
        }
}
```

### Exemplos

- Procura a saída de um labirinto, sendo `.` espaço livre e `#` as paredes

```c++ linenums="1"
/*
Exemplo de entrada:
7 8
########
#..#...#
## ...#..
#.#.##.#
#....#.#
##.#..##
########
5 3

Saída:

Saída encontrada:
########
#..#ooo#
## .oo#os
#.#o##.#
#.oo.#.#
##.#..##
########
*/

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

ll nl, nc, li, ci;
vector<vector<char>> mapa;
bool achouSaida = false;

void imprime() {
    for (ll l = 0; l < nl; l++, cout << "\n")
        for (ll c = 0; c < nc; c++)
            cout << mapa[l][c];
}

void caminha(ll l, ll c) {
    // Se achou solução, não precisa continuar
    if (achouSaida)
        return;
    // Verifica se 'l' e 'c' são valores válidos
    if (l < 0 || c < 0 || l >= nl || c >= nc)
        return;
    // Verifica se a posição não é parede
    if (mapa[l][c] != '.')
        return;
    // Verifica se chegou em uma das extremidades do mapa.
    // Se chegou, achou a saída.
    if (l == 0 || c == 0 || l == nl - 1 || c == nc - 1) {
        mapa[l][c] = 's';
        achouSaida = true;
        return;
    }
    // Se não retornou ainda, é porque o processo deve continuar
    mapa[l][c] = 'o';  // Marca o mapa, indicando que foi passado nessa posição
    caminha(l, c - 1); // Tenta caminhar para trás
    caminha(l + 1, c); // Tenta caminhar para baixo
    caminha(l, c + 1); // Tenta caminhar para frente
    caminha(l - 1, c); // Tenta caminhar para cima

    // Se não achou saída, desmarca no mapa
    if (!achouSaida)
        mapa[l][c] = '.';
}

int main() {

    cin >> nl >> nc;
    mapa.resize(nl, vector<char>(nc));
    for(ll l = 0; l < nl; l++)
        for(ll c = 0; c < nc; c++)
            cin >> mapa[l][c];
    cin >> li >> ci;
    caminha(li - 1, ci - 1);

    if (achouSaida) {
        cout << "Saída encontrada:" << endl;
        imprime();
    }
    else
        cout << "Solução não encontrada" << endl;

    return 0;
}
```

- Conta o número de regiões. Semelhante ao do labrinto, mas caminha nos `#` marcando toda uma região cercada por "água", simbolizada por `.`.

```c++ linenums="1"
/*
Exemplo de entrada:
7 8
..#...#.
..#####.
#...#...
##......
###...#.
.....###
..#...#.


Saída:

Número de regiões: 4
..1...1.
..11111.
2...1...
22......
222...3.
.....333
..4...3.
*/
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

ll nl, nc;
vector<vector<string>> mapa;

void imprime() {
    for (ll l = 0; l < nl; l++, cout << "\n")
        for (ll c = 0; c < nc; c++)
            cout << mapa[l][c];
}

void caminha(ll l, ll c, ll nr) {
    // Verifica se 'l' e 'c' são valores válidos
    if (l < 0 || c < 0 || l >= nl || c >= nc)
        return;
    // Se a posição não for uma região, retorne
    if (mapa[l][c] != "#")
        return;

    // Se não retornou ainda, é porque o processo deve continuar
    mapa[l][c] = to_string(nr);  // Marca no mapa o número da região
    caminha(l, c - 1, nr);       // Tenta caminhar para trás
    caminha(l + 1, c, nr);       // Tenta caminhar para baixo
    caminha(l, c + 1, nr);       // Tenta caminhar para frente
    caminha(l - 1, c, nr);       // Tenta caminhar para cima

}

int main() {

    cin >> nl >> nc;
    mapa.resize(nl, vector<string>(nc));
    char v;
    for (ll l = 0; l < nl; l++)
        for (ll c = 0; c < nc; c++) {
            cin >> v; // (1)
            mapa[l][c] = v;
        }

    ll numRegioes = 0;
    // Procura pelas regiões ainda não marcadas
    for (ll l = 0; l < nl; l++) 
        for (ll c = 0; c < nc; c++) {
            if (mapa[l][c] == "#") { // Encontrou uma região
                numRegioes++;
                caminha(l, c, numRegioes); // Marca a região
            }
        }
    
    cout << "Número de regiões: " << numRegioes << endl;
    imprime();

    return 0;
}
```

1. Se for feito `cin >> mapa[l][c];`, a linha toda será atribuida a posição `mapa[l][c]`.

### Material complementar

- [Backtracking Algorithms (GeeksforGeeks)](https://www.geeksforgeeks.org/backtracking-algorithms/)


<!-- https://neps.academy/br/codcad -->