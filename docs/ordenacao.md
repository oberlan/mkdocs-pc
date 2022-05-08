# Ordenação e Busca[^1]

[^1]: O texto dessa página são traduções e adaptações encontrados aqui: [1](https://usaco.guide/PAPS.pdf), [2](https://usaco.guide/CPH.pdf) e [3](https://usaco.guide/silver/binary-search)

## Ordenação

A maioria das linguagens de programação modernas implementa uma função de ordenação eficiente. Em C++, tem-se a função [`sort`](https://en.cppreference.com/w/cpp/algorithm/sort) da biblioteca [`<algorithm>`](https://en.cppreference.com/w/cpp/algorithm). Veja alguns exemplos de uso desta função:

```c++ linenums="1"
int n = 7;
int a[] = {4,2,5,3,5,8,3};
sort(a,a+n); // (1)

vector<int> v = {4,2,5,3,5,8,3};
sort(v.begin(), v.end()); // (2)

vector<pair<int, string>> alunos = {{789, "Paulo"}, {456, "Ana"}, {123, "Paulo"}};
sort(alunos.begin(), alunos.end()); // (3)
for (auto a: alunos)
    cout << a.first << " " << a.second << endl; // (4)
```

1. [2, 3, 3, 4, 5, 5, 8]
2. [2, 3, 3, 4, 5, 5, 8]
3. Por padrão, um `pair` sempre é ordenado pelo campo `first`.
4. `{123, Paulo} {456, Ana} {789, Paulo}`

A ordenação padrão é a não-decrescente, mas pode-se obter a ordem inversa da seguinte forma:

```c++ linenums="1"
vector<pair<int, string>> alunos = {{789, "Paulo"}, {456, "Ana"}, {123, "Paulo"}};
sort(alunos.rbegin(), alunos.rend()); 
for (auto a: alunos) // (1)
    cout << a.first << " " << a.second << endl; 
```

1. `{789, Paulo} {456, Ana} {123, Paulo}`

Para ordernarmos um `#!c++ struct` ou `#!c++ class` ou outra coleção de objetos (por exemplo, um `pair` pelo campo `second`), tem-se duas alternativas: ($i$) definir uma função de comparação ou ($ii$) fazer a sobrecarga do operador `<`.

Agora suponha que seja necessário ordenar objetos do tipo `Pessoa`:

```c++ linenums="1"
class Pessoa {
public: 
    string nome, sobrenome;
    int idade;
    Pessoa(){ // (1)
        this->nome = "";
        this->sobrenome = "";
        this->idade = 0;
    }
    Pessoa(string _nome, string _sobrenome, int _idade){
        this->nome = _nome;
        this->sobrenome = _sobrenome;
        this->idade = _idade;
    }
    void imprime() {
        cout << "(" << nome << ", " << sobrenome << ", " << idade << ")\n";
    }
};
```

1. Construtor padrão usado quando não passamos nenhum argumento para a classe. Sempre faça o construtor padrão. Caso contrário, não será possivel fazer, por exemplo: `#!c++ Pessoa p; //Chama o construtor padrão`.

Para ordernarmos um `#!c++ struct` ou `#!c++ class` ou outra coleção de objetos (por exemplo, um `pair` pelo campo `second`), tem-se duas alternativas: ($i$) definir uma função de comparação ou ($ii$) fazer a sobrecarga do operador `<`.

### Usando uma função de comparação

A primeira alternativa é definir uma função que compara dois objetos e retorna `#!c++ true` caso o primeiro seja considerado menor que o segundo e `#!c++ false`, caso contrário. Por exemplo,

```c++ linenums="1"
bool compara(const Pessoa &p1, const Pessoa &p2) { // (1)
        if (p1.nome < p2.nome)
            return true;
        else if (p1.nome == p2.nome) {
            if (p1.sobrenome < p2.sobrenome)
                return true;
            else if (p1.sobrenome == p2.sobrenome)
                return p1.idade > p2.idade;
            else
                return false;
        }
        else
            return false;
}
```

1. O `#!c++ const` diz ao compilador que os objetos passados para a função não serão alterados internamento. Já o `#!c++ &` representa [**passagem por referência**](https://www.learncpp.com/cpp-tutorial/pass-by-lvalue-reference/), uma alternativa a [passagem por ponteiro](https://www.learncpp.com/cpp-tutorial/pass-by-address/). Dessa forma, não passamos uma cópia dos objetos e sim referências (endereços) dos mesmos, o que torna o código mais eficiente. Por quê?

Com a função de comparação definida, basta passá-la como argumento na função `sort`:

```c++ linenums="1"
int main() {
    Pessoa p1("Paulo", "Roberto", 35);
    Pessoa p2("Paulo", "Alberto", 30);
    Pessoa p3("Paulo", "Roberto", 40);

    vector<Pessoa> lista;
    lista.push_back(p1); // (1)
    lista.push_back(p2);
    lista.push_back(p3);
    for (auto p: lista)
        p.imprime();
    cout << "\n";
    sort(lista.begin(), lista.end(), compara); // (2) 
    for (auto p : lista)
        p.imprime();

    return 0;
}
```

1. Inclui o elemento no fim do `vector`. Leia mais sobre `vector` [aqui](https://en.cppreference.com/w/cpp/container/vector).
2. Usa a função `compara` para fazer a ordenação.

Também é possivel usar uma [função lambda](https://www.learncpp.com/cpp-tutorial/introduction-to-lambdas-anonymous-functions/) como função de comparação. Por exemplo, para ordenar um `pair` pelo campo `second` (ou usando-o como critério de desempate), pode-se fazer:

```c++ linenums="1"
bool compara(const pair<int, int> &p1, const pair<int, int> &p2) {
    if (p1.first < p2.first)
        return true;
    else if (p1.first == p2.first)
        return p1.second < p2.second;
    else
        return false;
}

int main() {
    vector<pair<int, int>> pontos = {{0, 2}, {0, 1}, {0, 0}, {0, -5}, {1, 2}, {-1, 10}};

    sort(pontos.begin(), pontos.end(), compara); // (1)
    for (auto a: pontos) // (2)
        cout << "(" << a.first << ", " << a.second << ") ";

    sort(pontos.begin(), pontos.end(), [](const pair<int, int> &p1, const pair<int, int> &p2) { // (3)
        if (p1.first < p2.first)
            return true;
        else if (p1.first == p2.first)
            return p1.second < p2.second;
        else
            return false;
    }); 
    for (auto a: pontos) 
        cout << "(" << a.first << ", " << a.second << ") ";

    return 0;
}
```

1. Usando a função `compara`
2. `(-1, 10) (0, -5) (0, 0) (0, 1) (0, 2) (1, 2)`
3. Usando função lambda

### Fazando a sobrecarga do operador `<`

Ao invés de ser difinidas funções de comparações, pode-se fazer a sobrecarga do operador `<` (`#!c++ operator<`). Isso é comum ao se usar `#!c++ class` ou `#!c++ struct`. Veja como ficaria ao ser considerado a classe `Pessoa`:

```c++ linenums="1"
class Pessoa {
public: 
    string nome, sobrenome;
    int idade;
    Pessoa(){ 
        this->nome = "";
        this->sobrenome = "";
        this->idade = 0;
    }
    Pessoa(string _nome, string _sobrenome, int _idade){
        this->nome = _nome;
        this->sobrenome = _sobrenome;
        this->idade = _idade;
    }
    bool operator<(const Pessoa &p){ // (1)
        if (nome < p.nome)
            return true;
        else if (nome == p.nome) {
            if (sobrenome < p.sobrenome)
                return true;
            else if (sobrenome == p.sobrenome)
                return idade > p.idade;
            else
                return false;
        }
        else
            return false;

    }
    void imprime() {
        cout << "(" << nome << ", " << sobrenome << ", " << idade << ")\n";
    }
};

int main() {
    Pessoa p1("Paulo", "Roberto", 35);
    Pessoa p2("Paulo", "Alberto", 30);
    Pessoa p3("Paulo", "Roberto", 40);

    vector<Pessoa> lista;
    lista.push_back(p1); 
    lista.push_back(p2);
    lista.push_back(p3);
    for (auto p: lista)
        p.imprime();
    cout << "\n";
    sort(lista.begin(), lista.end()); // (2) 
    for (auto p : lista)
        p.imprime();

    return 0;
}
```

1. Sobrecarga/definição do operador `<` para a classe `Pessoa`. Dessa forma, é possivel fazer a comparação `#!c++ p1 < p2`, considerando que `p1` e `p2` são objetos do tipo `Pessoa`.
2. Usa o operador `<` para fazer a ordenação.

### Complemente sua leitura e seu conhecimento

- [Competitive Programmer’s Handbook](https://usaco.guide/CPH.pdf#page=35)
- [Sorting (CS Academy)](https://csacademy.com/lesson/sorting/)
- [std::sort](https://en.cppreference.com/w/cpp/algorithm/sort)
- [std::partial_sort](https://en.cppreference.com/w/cpp/algorithm/partial_sort)
- [std::stable_sort](https://en.cppreference.com/w/cpp/algorithm/stable_sort)
- [std::nth_element](https://en.cppreference.com/w/cpp/algorithm/nth_element)

## Busca

### Busca sequencial

A forma mais intuitiva para procurar um elemento em um *array* é usar um *loop* que percorre todos os elementos do array e parando assim que o elemento buscado é encontrado. No pior caso, é necessário percorrer todos os elementos, logo a complexidade será $O(n)$. O código abaixo verifica se `x` está no *array*:

```c++ linenums="1"
for (int i = 0; i < n; i++) {
    if (array[i] == x) {
        // x encontrado na posição i
    }
}
```

Em C++, pode-se usar a função [`search`](https://en.cppreference.com/w/cpp/algorithm/search).

### Busca binária

Se os elementos do *array* estiverem ordenados, pode-se usar uma estratégia diferente e mais eficiente para realizar a busca: verifique se o elemento do meio do *array* é o elementos buscado, se for, a busca termina. Caso não seja, verique se o elemento do meio é menor que elemento buscaso, se for, repita o processo considerando apenas a segunda metade do *array*. Senão, considere a primeira metade do *array*. Assim, a cada passo da busca, o tamanho do *array* é reduzido a metade, logo, a complexidade do algoritmo é $O(\log n)$.

```c++ linenums="1"
int buscaBinaria(vector<int> v, int x) {
    int ini = 0, fim = v.size()-1;
    while (ini <= fim) {
        int meio = ini + (fim - ini) / 2; // (1)
        if (v[meio] == x) return meio;
        else if (v[meio] < x) ini = meio + 1;
        else fim = meio - 1;
    }
    return -1; // (2)
}
```

1. Evite usar `meio = (ini + fim) / 2;`, já que `ini + fim` pode gerar [*integer overflow*](https://www.cplusplus.com/articles/DE18T05o/).
2. `x` não está no vetor `v`.

Em C++, pode-se usar a função [std::binary_search](https://en.cppreference.com/w/cpp/algorithm/binary_search). As funções abaixo também são úteis e baseadas na busca binária:

- [std::lower_bound](https://en.cppreference.com/w/cpp/algorithm/lower_bound): retorna um ponteiro para o primeiro elemento do *array* cujo valor é pelo menos `x`;
- [std::upper_bound](https://en.cppreference.com/w/cpp/algorithm/upper_bound): retorna um ponteiro para o primeiro elemento do *array* cujo valor é maior que `x`;

As funções assumem que o *array* está ordenado. Se o valor procurado não for encontrado, é retornado um ponteiro para o elemento após o último elemento do *array*. Por exemplo, o código a seguir verifica se o *array* contém um elemento com valor `x`:

```c++ linenums="1"
vector<int> v = {1, 2, 3, 5, 8, 10, 20};
int x = 10;
auto k = lower_bound(v.begin(), v.end(), x);
if (k != v.end() && *k == x) {
    // x encontrado no indice k
    cout << "Valor encontrado na posição: " << k - v.begin() << endl; // (1)
}
```

1. Pode-se usar a função [std::distance](https://en.cppreference.com/w/cpp/iterator/distance) ao invés de `k - v.begin()`, ou seja, `distance(v.begin(), k)`.

Complemente sua leitura e seu conhecimento:

- :fontawesome-brands-youtube: [Binary Search tutorial (C++ and Python)](https://www.youtube.com/watch?v=GU7DpgHINWQ)
- [Busca Binária](https://github.com/UnBalloon/programacao-competitiva/tree/master/Busca%20Bin%C3%A1ria)
- [Binary Search](https://usaco.guide/silver/binary-search)
- [Binary Search (ITMO Academy)](https://codeforces.com/edu/course/2/lesson/6) 🤯
- [Binary Search (CS Academy)](https://csacademy.com/lesson/binary_search/)

#### Busca binária em funções monotônicas[^2]

[^2]: Conteúdo extraído de [Busca Binária](https://github.com/UnBalloon/programacao-competitiva/tree/master/Busca%20Bin%C3%A1ria)

Considere uma função booleana $f(x)$ e se deseja encontrar o valor máximo (ou mínimo) de $x$ tal que $f(x)$ seja `#!c++ true`. Da mesma forma que a busca binária só funciona se o *array* estiver ordenado, só é possivel aplicar a busca binária em uma função [monótona](https://pt.wikipedia.org/wiki/Fun%C3%A7%C3%A3o_mon%C3%B3tona), ou seja, é sempre não-decrescente ou sempre não-crescente.

Seja `check(x)` uma função que verifica uma propriedade de `x`. Se para todo `x`, `#!c++ check(x) = true` implica `#!c++ check(x+1) = true`, ou para todo `x`, `#!c++ check(x) = false` implica `#!c++ check(x+1) = false`, então a função `check` é monótona.

Suponha a função `check` abaixo que verifica se um elemento é maior ou igual a `x`.. Se `x = 11` e o vetor `v = [1,2,3,5, 8, 11, 12, 14, 16]`, então teremos o seguinte vetor de saída ao aplicarmos `check` em `v`: `[0,0,0,0,0,1,1,1,1,1]`.

```c++ linenums="1"
bool check(int val) {
    return val >= x;
}
```

Dessa forma, a função `check` para essa situação é monótona e isso é relevante porque se um valor do vetor satisfizer a condição, todos os valores a direita também vão satisfazê-la, e de forma análoga, todos os valores a esquerda de um índice que não satisfaz a condição, também não vão satisfazer, e é isso que nos permite aplicar busca binária. Além disso, a função `check` só se torna monótona nesse exemplo quando o vetor está ordenado, por isso a busca binária só é feita em vetores ordenados.

Como encontrar o menor valor que torna `check` verdadeiro? R. inicia-se o processo "chutanto" um intervalo onde a resposta com certeza estará. Para cada intevalo, checa-se o meio e, dependendo da resposta, descarta-se os elementos a direita ou a esquerda, mas sempre divide-se o tamanho do intervalo por 2, até que o intervalo tenha tamanho 1. Veja uma solução:

```c++ linenums="1"
int l = a;// sei que a resposta não é menos que a
int r = b;// sei que a resposta não é mais que b

while(r > l+1){// repita enquanto o intervalo tiver tamanho > 2
    int mid = l + (r - l)/2;
    if(check(mid)){ // mid é válido
        r = mid; // como queremos minimizar a resposta, e mid é uma resposta válida
                 //descartamos tudo a direita de mid (mas não mid)
    }
    else{
        l = mid + 1; // Se mid não é válido, descartamos ele e tudo abaixo.
    }
}
// Ao final desse laço, a resposta pode estar em l ou r.
// Queremos minimizar a resposta, então se l for válido,
// ficaremos com l, e caso contrário,  com r
int ans = r;
if(check(l)) ans = l;
```

Exemplo: Encontrar o maior valor de $x \in [0, 10]$ tal que $x^2 \leq 30$.

```c++ linenums="1"
bool check(int val) {
    return val*val <= 30;
}
int lastTrue(int ini, int fim) {
    ini--; // Se nenhum valor no intervalor for true, retorna ini - 1
    while (ini < fim) {
        int m = ini + (fim - ini) / 2;
        if (check(m)) ini = m; // (1)
        else fim = m - 1; // (2)
    }
    return ini;
}
```

1. Se `#!c++ check(m)` é `#!c++ true`, então todos os números menores que `m` também serão `#!c++ true`.
2. Se `#!c++ check(m)` é `#!c++ false`, então todos os números maiores que `m` também serão `#!c++ false`.

#### Two-Pointers

Na técnica chamada [Two-Pointers](https://www.geeksforgeeks.org/two-pointers-technique/) dois "aponstadores" caminham pelo vetor. Normalmente, esses apontadores são ``colocados'' nas extremidades opostas do vetor e caminham um em direção ao outro, como mostra a figura abaixo.

<figure markdown>
<img src="/img/two-pointer.png" alt="Two-Pointers" style="width:450px;display: block; margin: 0 auto;"/>
  <figcaption style="font-size=8pt">Fonte: <a href="https://afteracademy.com/blog/what-is-the-two-pointer-technique">AfterAcademy</a></figcaption>
</figure>

Você consegue pensar em como usar esse técnica para resolver o problema de inverter os elementos de um vetor sem usar um vetor auxiliar? A ideia é simples: coloque cada apontador (digamos `i` e `j`) em uma extremidade do vetor,  ou seja, `i = 0` e `j = n - 1`, e, a cada iteração, troque os elementos que estão nas posições `i` e `j` (ou seja, `v[i] <-> v[j]`). Após a troca, incremente o apontador `i` e decremente o apontador `j`. Repita esse processo enquanto `i < j`. A figura abaixo ilustra parte desse processo.

<figure markdown>
<img src="/img/twoPointers.png" alt="Two-Pointers" style="width:350px;display: block; margin: 0 auto;"/>
  <figcaption style="font-size=8pt">Fonte: <a href="https://afteracademy.com/blog/what-is-the-two-pointer-technique">AfterAcademy</a></figcaption>
</figure>

O código abaixo ilustra essa estratégia (note a simplicidade):

```c++ linenums="1"
void inverte(vector<int> &v) { // (1)
    int i = 0;
    int j = v.size() - 1;
    while ( i < j ) {
        swap(v[i], v[j]); // (2)
        i++;
        j--;
    }
}
```

1. A [std::reverse](https://en.cppreference.com/w/cpp/algorithm/reverse) também pode ser usada com o mesmo objetivo. O intuito é mostrar com a técnica Two-Pointers funciona.
2. [std::swap](https://en.cppreference.com/w/cpp/algorithm/swap)

Complemente sua leitura e seu conhecimento:

- [Two Pointers Method (ITMO Academy)](https://codeforces.com/edu/course/2/lesson/9) 🤯
- [Two Pointers Technique](https://www.geeksforgeeks.org/two-pointers-technique/)
- [What is the Two pointer technique?](https://afteracademy.com/blog/what-is-the-two-pointer-technique)
