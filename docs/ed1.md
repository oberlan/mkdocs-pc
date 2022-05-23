# Estrutura de Dados e STL[^1]

[^1]: O texto dessa página são traduções e adaptações encontrados aqui: [1](https://usaco.guide/CPH.pdf), [2](https://usaco.guide/PAPS.pdf), [3](https://hackingcpp.com/), [4](https://en.cppreference.com/w/cpp/container)

Estrutura de dados (ED) é a forma como os dados são armazenados na memória do computador com o objetivo de tornar o processamento mais fácil e eficiente. Cada uma possui suas próprias vantagens e desvantagens, por isso, é crucial conhecer diferentes estruturas de dados (básicas e avançadas) para  conseguir definir qual as mais apropriada para um determinado problema. Em programação competitiva, com o objetivo de economizar uma grande quantidade de tempo ao implementar um algoritmo, também é muito importante saber quais EDs estão disponíveis na biblioteca padrão, como usá-las e qual a complexidade de cada operação da ED.

A seguir são apresentados as estruturas de dados presentes na biblioteca padrão do C++ comumente usadas em competições.

Leitura recomendada:

- [C++ Standard Library Containers](https://hackingcpp.com/cpp/std/containers.html)
- [More Operations on Sorted Sets](https://usaco.guide/silver/intro-sorted-sets?lang=cpp)
- [C++ Sets with Custom Comparators](https://usaco.guide/silver/custom-cpp-stl?lang=cpp)
- [C++ Containers library](https://en.cppreference.com/w/cpp/container)

## vector

Um `vector` é um array dinâmico que permite adicionar e remover elementos de forma eficiente no final da estrutura. Por exemplo, o código a seguir cria um vetor vazio e adiciona três elementos a ele:

```c++ linenums="1"
vector<int> v;
v.push_back(3); // [3]
v.push_back(2); // [3,2]
v.push_back(5); // [3,2,5]
```

Observe que os elementos são inseridos no fim. Então, os elementos podem ser acessados como em um array comum:

```c++ linenums="1"
cout << v[0] << "\n"; // 3
cout << v[1] << "\n"; // 2
cout << v[2] << "\n"; // 5
```

Outra maneira de criar um vetor é fornecer uma lista de seus elementos:

```c++ linenums="1"
vector<int> v = {2,4,2,5,1};
```

Também pode-se fornecer o número de elementos e seus valores iniciais:

```c++ linenums="1"
vector<int> a(8);    // tamanho 8, valor inicial: 0
vector<int> b(8, 2); // tamanho 8, valor inicial: 2
```

A função `size()` retorna o número de elementos no `vector`. Por exemplo:

```c++ linenums="1"
for (int i = 0; i < v.size(); i++) {
    cout << v[i] << "\n";
}
```

Uma alternativa mais simples é a seguinte:

```c++ linenums="1"
for (auto x : v) {
    cout << x << "\n";
}
```

A função `back` retorna o último elemento de um vetor e a função `pop_back` remove o último elemento:

```c++ linenums="1"
vector<int> v = {2,4,2,5,1};
cout << v.back() << "\n"; // 1
v.pop_back();
cout << v.back() << "\n"; // 5
```

A figura abaixo mostra mais operações e uso do `vector`. 

<figure markdown>
<img src="https://hackingcpp.com/cpp/std/vector.png" alt="std::vector" style="display: block; margin: 0 auto;"/>
  <figcaption style="font-size=8pt">Fonte: <a href="https://hackingcpp.com/cpp/std/vector.html">Hacking C++</a></figcaption>
</figure>

Complemente sua leitura e seu conhecimento:

- [std::vector](https://hackingcpp.com/cpp/std/vector.html)
- [std::vector](https://en.cppreference.com/w/cpp/container/vector)

## deque

Um `deque` é um array dinâmico que pode ser manipulado eficientemente em ambas as extremidades da estrutura. Como um `vector`, um `deque` fornece as funções `push_back` e `pop_back`, mas também fornece as funções `push_front` e `pop_front` que não estão disponíveis em um `vector`. veja uma exemplo:

As principais funções da `deque` são:

- `push_front(x)`: adiciona o elemento `x` no início da estrutura;
- `push_back(x)`: adiciona o elemento `x` no fim da estrutura;
- `pop_front()`: remove o primeiro elemento da estrutura;
- `pop_back()`: remove o último elemento da estrutura;
- `front()`: retorna o primeiro elemento da estrutura;
- `back()`: retorna o último elemento da estrutura;
- `size()`: retorna o número de elementos da estrutura.

O código a seguir ilustra a utilização da estrutura:

```c++ linenums="1"
deque<int> d;
d.push_back(5);  // [5]
d.push_back(2);  // [5,2]
d.push_front(3); // [3,5,2]
d.pop_back();    // [3,5]
d.pop_front();   // [5]
```

As operações de um `deque` funcionam em tempo médio $O(1)$. A figura abaixo mostra mais operações e uso do `deque`.

<figure markdown>
<img src="https://hackingcpp.com/cpp/std/deque.png" alt="std::deque" style="display: block; margin: 0 auto;"/>
  <figcaption style="font-size=8pt">Fonte: <a href="https://hackingcpp.com/cpp/std/sequence_containers.html#deque">Hacking C++</a></figcaption>
</figure>

Complemente sua leitura e seu conhecimento:

- [std::deque](https://hackingcpp.com/cpp/std/sequence_containers.html#deque)
- [std::deque](https://en.cppreference.com/w/cpp/container/deque)

## queue

A estrutura da `queue` (fila) corresponde a uma fila simples da vida real e segue a regra *First In First Out* (FIFO). Suas principais operações são: inserir um elemento no fim da fila, acessar e remover o primeiro elemento da fila. Essas operações possuem complexidade em $O(1)$.

As principais funções da `queue` são:

- `push(x)`: adiciona o elemento `x` no fim da fila;
- `pop()`: remove o primeiro elemento da fila;
- `front()`: retorna o primeiro elemento da fila;
- `size()`: retorna o número de elementos da fila.

O código a seguir ilustra a utilização da estrutura:

```c++ linenums="1"
queue<int> q;
q.push(2);                 // [2]
q.push(5);                 // [2,5]
cout << q.size() << "\n";  // 2
cout << q.front() << "\n"; // 2
q.pop();                   // [5]
cout << q.back() << "\n";  // 5
```

Complemente sua leitura e seu conhecimento:

- [std::queue](https://en.cppreference.com/w/cpp/container/queue)
- [Queue Data Structure (GeeksforGeeks)](https://www.geeksforgeeks.org/queue-data-structure/)

## stack

Uma `stack` (pilha) é uma estrutura muito semelhante a uma fila, mas que segue a regra *Last In First Out* (LIFO). Ou seja, ao inserir um elemento na pilha, ele é adicionado no topo e esse é o elemento que se tem acesso. Suas principais operações são: inserir um elemento no topo da pilha, acessar e remover o elemento do topo da pilha. Essas operações possuem complexidade em $O(1)$.

As principais funções da `stack` são:

- `push(x)`: adiciona o elemento `x` no topo da pilha;
- `pop()`: remove o elemento do topo da pilha;
- `top()`: retorna o elemento do topo da pilha;
- `size()`: retorna o número de elementos da pilha.

Veja um exemplo de utilização da estrutura:

```c++ linenums="1"
stack<int> s;
s.push(2);                // [2]
s.push(5);                // [2,5]
cout << s.size() << "\n"; // 2
cout << s.top() << "\n";  // 5
s.pop();                  // [2]
cout << s.top() << "\n";  // 2
```

Complemente sua leitura e seu conhecimento:

- [std::stack](https://en.cppreference.com/w/cpp/container/stack)
- [Stack Data Structure (GeeksforGeeks)](https://www.geeksforgeeks.org/stack-data-structure/)
- [Stacks](https://usaco.guide/gold/stacks?lang=cpp)

## priority_queue

Uma fila de prioridade (`priority_queue`) é uma estrutura semelhante a uma fila ou pilha, mas ao invés de inserções e remoções acontecerem em uma das extremidades da estrutura, o **maior** (ou **menor**) elemento é sempre retornado durante o acesso/remoção. Inserções e remoções possuem complexidade $O(\log n)$ e o acesso ao elemento de maior prioridade é $O(1)$. Uma fila de prioridade geralmente é implementada usando uma estrutura chamada [heap](https://www.geeksforgeeks.org/heap-data-structure/) que é muito mais simples do que uma árvore binária balanceada usado em um [set](#set-e-multiset).

As principais funções da `priority_queue` são:

- `push(x)`: adiciona o elemento `x` na fila de prioridade;
- `pop()`: remove o elemento de maior prioridade;
- `top()`: retorna o elemento de maior prioridade;
- `size()`: retorna o número de elementos da fila de prioridade.

O código a seguir ilustra a utilização da estrutura:

```c++ linenums="1"
priority_queue<int> q;
q.push(3);
q.push(5);
q.push(7);
q.push(2);
cout << q.top() << "\n"; // 7
q.pop();
cout << q.top() << "\n"; // 5
q.pop();
q.push(6);
cout << q.top() << "\n"; // 6
q.pop();
```

Se for necessário criar uma fila de prioridade que suporte encontrar e remover o menor elemento, pode-se fazer da seguinte forma:

```c++ linenums="1"
bool cmp(const int& a, const int& b) {
    return a > b;
}
priority_queue<int, vector<int>, cmp> pq; // (1)
```

1. Note o uso da função de comparação `cmp`. Essa função deve receber dois argumentos do tipo armazenado na fila de prioridade e retorna `#!c++ true` se o primeiro for considerado menor que o segundo.

Para o exemplo anterior, outra alternativa é usar a função [`greater`](https://en.cppreference.com/w/cpp/utility/functional/greater):

```c++ linenums="1"
priority_queue<int, vector<int>, greater<int>> pq;
```

Complemente sua leitura e seu conhecimento:

- [Priority Queue in C++ Standard Template Library (STL)](https://www.geeksforgeeks.org/priority-queue-in-cpp-stl/)
- [std::priority_queue](https://en.cppreference.com/w/cpp/container/priority_queue)

## set e multiset

Semelhante a um conjunto matemático, um `set` é uma coleção de **elementos únicos**, ou seja, todos os seus elementos são distintos. A estrutura é baseada em uma árvore binária balanceada (*red-black tree*) e acessar seus elementos é $O(\log n)$. Consequentemente, não é possível acessar os elementos do `set` usando o operador `[]`, como acontece em um `vector`. Além disso, os elementos são mantidos **ordenados**.

As principais funções do `set` (e `multiset`) são:

- `insert(x)`: adiciona o elemento `x` no conjunto. Se ele já estiver no conjunto, nada é feito;
- `erase(x)`: remove o elemento `x`;
- `count(x)`: retorna o número de elemento cuja chave seja `x`;
- `find(x)`: retorna um iterador para o elemento com chave `x`;
- `size()`: retorna o número de elementos do conjunto.

O código a seguir ilustra a utilização da estrutura `set`:

```c++ linenums="1"
set<int> s;
s.insert(3);
s.insert(2);
s.insert(5);
cout << s.count(3) << "\n"; // 1
cout << s.count(4) << "\n"; // 0
s.erase(3);
s.insert(4);
cout << s.count(3) << "\n"; // 0
cout << s.count(4) << "\n"; // 1
if (s.find(7) == s.end()){
    cout << "7 não está no set" << "\n";
}
auto menor = s.begin();
auto maior = s.end(); maior--; // (1) 
cout << "O menor elemento do conjunto é: " << *menor << "\n";
cout << "O maior elemento do conjunto é: " << *maior << "\n";

set<int> c1 = {2,5,6,8};
cout << c1.size() << "\n"; // 4
for (auto x: c1) 
    cout << x << "\n";

set<int> c2;
c2.insert(5);
c2.insert(5);
c2.insert(5);
cout << c2.count(5) << "\n"; // 1 (2)
```

1. Como `end()` aponta para um elemento após o último elemento, deve-se diminuir o iterador em uma unidade.
2. Lembre-se que em um `set` todos seus elementos são distintos. Assim, a função `count` sempre retornar ou 0 (elemento não está no `set`) ou 1 (o elemento está no `set`).

A estrutura `set` também fornece as funções `lower_bound(x)` e `upper_bound(x)` que retornam um iterador para o menor elemento em um `set` cujo valor é pelo menos ou maior que `x`, respectivamente. Em ambas as funções, se o elemento solicitado não existir, o valor de retorno é `end()`.

Um `multiset` é um conjunto que pode conter várias cópias do mesmo elemento. Por exemplo, o código abaixo adiciona três cópias do valor 5 ao `multiset`:

```c++ linenums="1"
multiset<int> s;
s.insert(5);
s.insert(5);
s.insert(5);
cout << s.count(5) << "\n"; // 3
```

A função `erase` remove todas as cópias de um valor do `multiset`:

```c++ linenums="1"
s.erase(5);
cout << s.count(5) << "\n"; // 0
```

Caso seja necessário remover apenas uma cópia, pode-se fazer da seguinte forma:

```c++ linenums="1"
s.erase(s.find(5));
cout << s.count(5) << "\n"; // 2
```

A figura abaixo mostra mais operações e uso do `set` e `multiset`.

<figure markdown>
<img src="/img/set.png" alt="std::set" style="width:100%;display: block; margin: 0 auto;"/>
  <figcaption style="font-size=8pt">Fonte: <a href="https://hackingcpp.com/cpp/std/set.png">Hacking C++</a></figcaption>
</figure>

Complemente sua leitura e seu conhecimento:

- [Standard Associative Containers](https://hackingcpp.com/cpp/std/associative_containers.html)
- [Set in C++ Standard Template Library (STL)](https://www.geeksforgeeks.org/set-in-cpp-stl/)
- [Multiset in C++ Standard Template Library (STL)](https://www.geeksforgeeks.org/multiset-in-cpp-stl/)
- [std::set](https://en.cppreference.com/w/cpp/container/set)
- [std::multiset](https://en.cppreference.com/w/cpp/container/multiset)

## map e multimap

Um `map` é um conjunto que consiste em pares de valores-chave. Um `map` também pode ser visto como um *array* comum. Enquanto as chaves em um array são sempre inteiros consecutivos $0, 1, \dots , n − 1$, onde $n$ é o tamanho do *array*, as chaves em um `map` podem ser de qualquer tipo de dados e não precisam ser valores consecutivos. Como no `set`, o `map` é baseado em em uma árvore binária balanceada (*red-black tree*) e acessar seus elementos é $O(\log n)$.

O código a seguir cria um `map` cujas chaves são strings e os valores são `#!c++ int`:

```c++ linenums="1"
map<string,int> m;
m["Paulo"] = 22;
m["Daniel"] = 38;
m["Bia"] = 19;
cout << m["Daniel"] << "\n"; // 38
```

Se o valor de uma chave for solicitado, mas que não existe no `map`, a chave será adicionada automaticamente ao `map` com um valor padrão do tipo (se for uma classe o construtor padrão é chamado). Por exemplo, no código a seguir, a chave `#!c++ "Ana"` com valor 0 é adicionada ao`map`.

```c++ linenums="1"
map<string,int> m;
cout << m["Ana"] << "\n";
```

Veja mais alguns exemplos de utilização da estrutura:

```c++ linenums="1"
map<string,int> m;
m["Paulo"] = 22;
m["Daniel"] = 38;
m["Bia"] = 19;

if (m.find("Ana") == m.end())
    cout << "Não temos a idade da Ana registrada" << "\n";

cout << "Paulo tem " << m["Paulo"] << " anos\n";
cout << "Bia tem " << m["Bia"] << " anos\n";

for (auto x : m) 
    cout << x.first << " " << x.second << "\n";

m.erase("Paulo");
cout << "Paulo tem " << m["Paulo"] << " anos\n";

auto ultimo = --m.end();
cout << ultimo->first << " tem " << ultimo->second << " anos\n";
```

A figura abaixo mostra mais operações e uso do `map` e `multimap`.

<figure markdown>
<img src="/img/map.png" alt="std::map" style="width:100%;display: block; margin: 0 auto;"/>
  <figcaption style="font-size=8pt">Fonte: <a href="https://hackingcpp.com/cpp/std/map.png">Hacking C++</a></figcaption>
</figure>

Complemente sua leitura e seu conhecimento:

- [Standard Associative Containers](https://hackingcpp.com/cpp/std/associative_containers.html)
- [Map in C++ Standard Template Library (STL)](https://www.geeksforgeeks.org/map-associative-containers-the-c-standard-template-library-stl/)
- [Multimap in C++ Standard Template Library (STL)](https://www.geeksforgeeks.org/multimap-associative-containers-the-c-standard-template-library-stl/)
- [std::map](https://en.cppreference.com/w/cpp/container/map)
- [std::multimap](https://en.cppreference.com/w/cpp/container/multimap)

## unordered_set e unordered_multiset

Um `unordered_set` é um contêiner associativo que contém um conjunto de objetos exclusivos. Já um `unordered_multiset` permite cópia dos elementos. Ambas as estruturas são baseadas em tabela hash e suas operações possuem complexidade, em média, em tempo $O(1)$. Internamente, os elementos não seguem nenhuma ordem. Veja um exemplo de uso das estruturas:

```c++ linenums="1"
unordered_set<int> s = {10, 5, -1, 20, 15, 5, 19};

cout << s.count(10) << "\n"; 
cout << s.count(4) << "\n"; 
s.erase(10);
s.insert(4);
cout << s.count(10) << "\n"; 
cout << s.count(4) << "\n";
if (s.find(7) == s.end())
    cout << "7 não está no set" << "\n";


for (auto x: s) 
    cout << x << " ";
cout << endl;

unordered_multiset<int> ms = {10, 5, -1, 20, 15, 5, 19};
for (auto x: ms) 
    cout << x << " ";
cout << endl;
```

A figura abaixo mostra mais operações e uso do `unordered_set` e `unordered_multiset`.

<figure markdown>
<img src="/img/unordered_set.png" alt="std::unordered_set" style="width:100%;display: block; margin: 0 auto;"/>
  <figcaption style="font-size=8pt">Fonte: <a href="https://hackingcpp.com/cpp/std/unordered_set.png">Hacking C++</a></figcaption>
</figure>

Complemente sua leitura e seu conhecimento:

- [Standard Associative Containers](https://hackingcpp.com/cpp/std/associative_containers.html)
- [std::unordered_set](https://en.cppreference.com/w/cpp/container/unordered_set)
- [std::unordered_multiset](https://en.cppreference.com/w/cpp/container/unordered_multiset)

## unordered_map e unordered_multimap

é um conjunto que consiste em pares de valores-chave

Um `unordered_map` é um contêiner associativo que contém pares de valores-chave com chaves exclusivas. Já um `unordered_multimap` permite múltiplas cópias das chaves. Ambas as estruturas são baseadas em tabela hash e suas operações possuem complexidade, em média, em tempo $O(1)$. Internamente, os elementos não seguem nenhuma ordem. Veja um exemplo de uso das estruturas:

```c++ linenums="1"
unordered_map<string,int> m;
m["Paulo"] = 22;
m["Daniel"] = 38;
m["Bia"] = 19;
m["Bia"] = 28; // Atualiza o valor da chave "Bia"

for (auto [chave, valor] : m) 
    cout << chave << " -> " << valor << "\n";

cout << endl;
unordered_multimap<string,int> mm;
mm.insert(make_pair("Paulo", 22)); // (1)
mm.insert(make_pair("Paulo", 43));
mm.insert(make_pair("Bia", 19));
mm.insert(make_pair("Bia", 22));


for (auto [chave, valor] : mm) 
    cout << chave << " -> " << valor << "\n";
```

1. Não é possível usar o `operator[]` como no `map` e `unordered_map`.

A figura abaixo mostra mais operações e uso do `unordered_map` e `unordered_multimap`.

<figure markdown>
<img src="/img/unordered_map.png" alt="std::unordered_map" style="width:100%;display: block; margin: 0 auto;"/>
  <figcaption style="font-size=8pt">Fonte: <a href="https://hackingcpp.com/cpp/std/unordered_map.png">Hacking C++</a></figcaption>
</figure>

Complemente sua leitura e seu conhecimento:

- [Standard Associative Containers](https://hackingcpp.com/cpp/std/associative_containers.html)
- [std::unordered_map](https://en.cppreference.com/w/cpp/container/unordered_map)
- [std::unordered_multimap](https://en.cppreference.com/w/cpp/container/unordered_multimap)
