# Ordenação[^1]

[^1]: O texto dessa página são traduções e adaptações encontrados aqui: [1](https://usaco.guide/PAPS.pdf), [2](https://usaco.guide/CPH.pdf) e [3](https://usaco.guide/silver/binary-search)

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

### Material complementar

- [Competitive Programmer’s Handbook](https://usaco.guide/CPH.pdf#page=35)
- [Sorting (CS Academy)](https://csacademy.com/lesson/sorting/)
- [std::sort](https://en.cppreference.com/w/cpp/algorithm/sort)
- [std::partial_sort](https://en.cppreference.com/w/cpp/algorithm/partial_sort)
- [std::stable_sort](https://en.cppreference.com/w/cpp/algorithm/stable_sort)
- [std::nth_element](https://en.cppreference.com/w/cpp/algorithm/nth_element)
