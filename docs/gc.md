# Geometria Computacional[^1]

[^1]: Os textos dessa página são traduções e adaptações encontrados nesses links: [1](https://usaco.guide/CPH.pdf) e [2](https://usaco.guide/CP2.pdf)

A **Geometria Computacional** é mais um tópico que aparece com frequência em programação competitiva. Por exemplo, calcular a área de um polígo; verificar se um determinado ponto está dentro ou fora de um polígono; verificar se existe interseção entre dois segmentos de reta; etc. Assim, nesta seção, serão apresentados alguns algoritmos e estratégias para responder essas e outras perguntas.

Leituras fortemente recomendadas:

- [Geometry](https://github.com/SuprDewd/T-414-AFLV/blob/master/12_geometry/aflv_12_geometry.pdf)
- [Capítulo 7](https://usaco.guide/CP2.pdf#page=191) do livro "Competitive Programming"
- [Capítulo 29](https://usaco.guide/CPH.pdf#page=275) do livro "Competitive Programmer’s Handbook"
- [Basic Geometry](https://cp-algorithms.com/geometry/basic-geometry.html)

## Pontos

O objeto mais simples em geometría computacional é o **Ponto**. No espaço Euclidiano 2D, pontos normalmente são representados usando `#!c++ struct` em C/C++ com dois membros: as coordenadas $x$ e $y$. Sempre que possível use coordenadas inteiras (`#!c++ long long`), caso contrário use `#!c++ double`. O código abaixo ilustra um `#!c++ struct` considerando coordenadas reais e algumas funções úteis.

``` c++ linenums="1"
#define EPS 1e-9
#define DEG2RAD(d) (d)*M_PI / 180.0
#define RAD2DEG(r) (r)*180.0 / M_PIs

struct Ponto {
    double x, y;
    Ponto(double _x = 0, double _y = 0):x(_x),y(_y){}
    Ponto operator+(const Ponto &p) const {
        return Ponto(x + p.x, y + p.y);
    }
    Ponto operator-(const Ponto &p) const {
        return Ponto(x - p.x, y - p.y);
    }
    Ponto operator*(double k) const {
        return Ponto(x*k, y*k);
    }
    bool operator<(const Ponto &p) const {
        if (fabs(x - p.x) > EPS) return x < p.x;
        return y < p.y;
    }
    bool operator==(const Ponto &p) const {
        return (fabs(x - p.x) < EPS) && (fabs(y - p.y) < EPS);
    }
    double norma() {
        return hypot(x, y); // (1)
    }
    Ponto normaliza() {
        return Ponto(x,y)*(1.0/norm());
    }
    double angulo() { return atan2(y, x); }
    double anguloPolar() {
        double a = atan2(y, x);
        return a < 0 ? a + 2*acos(-1.0) : a;
    }
};

double distancia(const Ponto &p1, const Ponto &p2) {
    return hypot(p1.x - p2.x, p1.y - p2.y); 
}

// Rataciona o ponto 'p' em 'theta' graus no sentido 
// horário (counter clockwise - CCW) em torno da origem (0, 0)
Ponto rotaciona(const Ponto &p, double theta) {
    double rad = DEG2RAD(theta);
    return Ponto(p.x*cos(rad) - p.y*sin(rad), p.x*sin(rad) + p.y*cos(rad));
}

// Retorna o produto interno de 'p1' e 'p2'
double inner(const Ponto &p1, const Ponto &p2) {
    return p1.x*p2.x + p1.y*p2.y;
}

// Retorna o produto vetorial de 'p1' e 'p2'
double cross(const Ponto &p1, const Ponto &p2) {
    return p1.x*p2.y - p1.y*p2.x;
}

// Verifica se o ponto 'r' está no lado esquerdo da linha 'pq'
bool ccw(const Ponto &p, const Ponto &q, const Ponto &r) {
    return cross(q-p, r-p) > 0;
}

// Verifica se o ponto 'r' está na mesma linha que a linha 'pq'
bool colinear(const Ponto &p, const Ponto &q, const Ponto &r) {
    return fabs(cross(p-q, r-p)) < EPS;
}
```

1. `#!c++ hypot(x, y)` retorna `#!c++ sqrt(x*x + y*y)`. Para saber mais clique [aqui](https://en.cppreference.com/w/cpp/numeric/math/hypot).

## Linhas

Uma *Linha* no espaço euclidiano 2D é o conjunto de pontos cujas coordenadas satisfazem uma dada equação linear $ax + by + c = 0$. Linhas são normalmente representadas com um `#!c++ struct` em C/C++ com três membros: os três coeficientes $a$, $b$ e $c$.

``` c++ linenums="1"
struct Linha {
    double a, b, c;
    Linha(double _a = NAN, double _b = NAN, double _c = NAN) : a(_a), b(_b), c(_c) {}
};

Linha pontosParaLinha(const Ponto &p1, const Ponto &p2) {
    if (fabs(p1.x - p2.x) < EPS && fabs(p1.y - p2.y) < EPS)
        return Linha();
    else if (fabs(p1.x - p2.x) < EPS)
        return Linha(1.0, 0.0, -p1.x);
    else {
        double a = -(p1.y - p2.y) / (p1.x - p2.x); 
        double b = 1.0;
        double c = -(a * p1.x) - p1.y;
        return Linha(a, b, c);
    }
}

bool saoParalelas(const Linha &l1, const Linha &l2) {
    return (fabs(l1.a-l2.a) < EPS) && (fabs(l1.b-l2.b) < EPS);
}

bool saoIguais(const Linha &l1, const Linha &l2) {
    return saoParalelas(l1 ,l2) && (fabs(l1.c - l2.c) < EPS);
}

// Verifica se há interseção entre as linhas 'l1' e 'l2'. 
// Se houver, salva o ponto de interseção em 'p'.
bool intersecao(const Linha &l1, const Linha &l2, Ponto &p) {
    if (saoParalelas(l1, l2)) return false;
    p.x = (l2.b * l1.c - l1.b * l2.c) / (l2.a * l1.b - l1.a * l2.b);
    if (fabs(l1.b) > EPS) p.y = -(l1.a * p.x + l1.c);
    else                  p.y = -(l2.a * p.x + l2.c);
    return true;
}
```

Um **segmento de linha** é uma linha de tamanho finito limitado por dois pontos. Já um **vetor** é um segmento de linha com uma direção. Normalmente, um vetor é representado por um `#!c++ struct` em C/C++ com dois membros: a magnitude $x$ e $y$ do vetor. Se necessário o vetor pode ser escalado e movido.

``` c++ linenums="1"
struct Vetor {
    double x, y;
    Vetor(double _x = NAN, double _y = NAN):x(_x), y(_y){}
};

// Converte dois pontos no vetor a->b
Vetor paraVetor(const Ponto &a, const Ponto &b) {
  return Vetor(b.x-a.x, b.y-a.y);
}

Vetor scale(const Vetor &v, double s) {
  return Vetor(v.x*s, v.y*s);          
}                                      

// Move 'p' de acordo com 'v' e retorna um novo ponto
Ponto translate(const Ponto &p, const Vetor &v) {  
  return Ponto(p.x+v.x, p.y+v.y); 
} 

// Retorna o produto vetorial dos vetores 'a' e 'b'
double cross(const Vetor &a, const Vetor &b) { 
    return a.x*b.y - a.y*b.x;
}

// Retorna o produto escalar dos vetores 'a' e 'b'
double dot(const Vetor &a, const Vetor &b) { 
    return (a.x*b.x + a.y*b.y); 
}

// Retorna o valor ao quadrado do vetor normalizado
double norm_sq(const Vetor &v) { 
    return v.x*v.x + v.y*v.y;
}

// Retorna a distância de 'p' até a linha definida pelos pontos 'a' e 'b'.
// O ponto mais próximo é armazenado no parâmetro 'c'.
double distToLine(const Ponto &p,  const Ponto &a,  const Ponto &b,  Ponto &c) {
    vec ap = paraVetor(a, p), ab = paraVetor(a, b);
    double u = dot(ap, ab) / norm_sq(ab);
    // Formula: c = a + u*ab
    c = translate(a, scale(ab, u)); 
    return distancia(p, c);              
}

// Retorna a distância de 'p' ao segmento de linha 'ab' definido pelos pontos 'a' e 'b'.
// O ponto mais próximo é armazenado no parâmetro 'c'.
double distToLineSegment(const Ponto &p, onst  Ponto &a, const  Ponto &b, Ponto &c) {
    vec ap = paraVetor(a, p), ab = paraVetor(a, b);
    double u = dot(ap, ab) / norm_sq(ab);
    if (u < 0.0) {                 
        c = Ponto(a.x, a.y);
        return dist(p, a);           
    }
    if (u > 1.0) {                 
        c = Ponto(b.x, b.y);
        return dist(p, b);           
    }
    return distToLine(p, a, b, c); 
}
```

## Círculos

Leia o conteúdo da seção 7.2.3 deste [livro](https://usaco.guide/CP2.pdf#page=197).

## Triângulos

Leia o conteúdo da seção 7.2.4 deste [livro](https://usaco.guide/CP2.pdf#page=199).

## Quadriláteros

Leia o conteúdo da seção 7.2.5 deste [livro](https://usaco.guide/CP2.pdf#page=201).

## Polígonos

Em construção...
<!-- ### Representação

### Área de um polígono

### Verificando se um polígono é convexo

### Verificando se um ponto está interno a um polígono -->

## Fecho Convexo (Convex Hull)

Em construção...

## Material Complementar

Além dos links recomendados no ínicio dessa seção, os links abaixo complementam o seu conhecimento pelo assunto.

- [Geometria Computacional (IME-USP)](https://www.ime.usp.br/~freitas/gc/)
- [Geometric Routines (Igor's Code Archive)](http://shygypsy.com/tools/geometry.cpp)
- [Geometry: 2D points and lines [Tutorial]](https://codeforces.com/blog/entry/48122)
- [Geometric Algorithms (GeeksforGeeks)](https://www.geeksforgeeks.org/geometric-algorithms/)
- [Handbook of geometry for competitive programmers](https://vlecomte.github.io/cp-geo.pdf)
