# Estrutura de Dados

Nesta parte, serão apresentados duas estruturas baseadas em árvores e uma implementação de *set* do GNU C++.  

## GNU C++ Policy-Based Sets

O compilador GNU g++ também suporta algumas estruturas de dados que não fazem parte da biblioteca padrão do C++. Essas estruturas são chamadas de *estruturas de dados baseadas em políticas* ([Policy-Based Data Structures - PBDS](http://gcc.gnu.org/onlinedocs/libstdc++/manual/policy_data_structures_design.html)). Para usar essas estruturas é necessário incluir o seguinte código:

```c++ linenums="1"
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>
using namespace __gnu_pbds;
```

Alternativamente, pode-se simplificar o código anterior da seguinte forma:

```c++ linenums="1"
#include <bits/extc++.h> 
using namespace __gnu_pbds;
```

Após isso, é possível definir um **conjunto ordenado indexado** (entre as disponíveis, essa é a principal estrutura usada em competições de programação). Esta estrutura é como o [*set*](/ed1/#set-e-multiset), mas com a possibilidade de acessar os elementos de acordo com a sua posição (índice), como em um *array*. O código abaixo, define um conjunto ordenado indexado usando *template*.

```c++ linenums="1"
template <class T> 
using indexed_set = tree<T, null_type, less<T>, rb_tree_tag, tree_order_statistics_node_update>;
```

Veja alguns exemplos de uso da definição anterior:

```c++ linenums="1"
indexed_set<int> setInt;                // Conjunto ordenado indexado de inteiros
indexed_set<double> setDouble;          // Conjunto ordenado indexado de doubles
indexed_set<pair<int, int>> setPairInt; // Conjunto ordenado indexado de pares de inteiros
indexed_set<Pessoa> setPessoa;          // Conjunto ordenado indexado de Pessoas (precisa do operator<)
```

Além das operações comuns do [*set*](/ed1/#set-e-multiset) (`insert`, `erase`, `size`, `clear`, `find`, `begin`, `end`), ainda existem duas funções muito úteis e que justificam o uso do PBDS:

- `find_by_order(k)`: retorna um iterador para o $k$-ésimo menor elemento (contando a partir de zero) do conjunto em tempo $O(\log n)$;
- `order_of_key(x)`: retorna ao número de itens **estritamente menores** que o `x` em tempo $O(\log n)$.

Veja um exemplo completo de utilização do PBDS (Fonte: [pbds.cpp](https://github.com/stevenhalim/cpbook-code/blob/master/ch2/nonlineards/pbds.cpp)):

```c++ linenums="1"
#include <bits/stdc++.h>
#include <bits/extc++.h> // pbds
using namespace std;
using namespace __gnu_pbds;

template <class T> 
using indexed_set = tree<T, null_type, less<T>, rb_tree_tag, tree_order_statistics_node_update>;

int main() {
  int n = 9;
  vector<int> A = {71, 10, 2, 4, 23, 7, 10, 15, 23, 50, 65, 71};
  indexed_set<int> iset;
  for (int i = 0; i < A.size(); ++i)             // O(n log n)
      iset.insert(A[i]);
  cout << "Set size: " << iset.size() << "\n";   // 9

  for (auto x: iset)
    cout << x << " ";
  cout << "\n";

  // O(log n) select
  cout << *iset.find_by_order(0) << "\n";        // 1-smallest = 2
  cout << *iset.find_by_order(n-1) << "\n";      // 9-smallest/largest = 71
  cout << *iset.find_by_order(4) << "\n";        // 5-smallest = 15
  // O(log n) rank
  cout << iset.order_of_key(2) << "\n";          // index 0 (rank 1)
  cout << iset.order_of_key(71) << "\n";         // index 8 (rank 9)
  cout << iset.order_of_key(15) << "\n";         // index 4 (rank 5)

  iset.erase(2);
  cout << "Set size: " << iset.size() << "\n";   // 8

  return 0;
}
```

Complemente sua leitura e seu conhecimento:

- [Ordered Set and GNU C++ PBDS (GeeksforGeeks)](https://www.geeksforgeeks.org/ordered-set-gnu-c-pbds/)
- [C++ STL: Policy based data structures](https://codeforces.com/blog/entry/11080)
- [PBDS Examples](https://opensource.apple.com/source/llvmgcc42/llvmgcc42-2336.9/libstdc++-v3/testsuite/ext/pb_ds/example/)

## Fenwick (Binary Indexed) Tree

Em construção...

## Segment Tree

Em construção...
