# Grafos

Um dos assuntos mais frequentes são problemas relacionados a grafos, ou que podem ser representados como grafos, apesar de não ser óbvio à primeira vista.

## Conceitos

Há diversos tipos e atributos de um grafo:

### O que é um grafo?

Um grafo é definido simplesmente por um conjunto de vértices, e outro conjunto de arestas. Essas arestas ligam dois vértices, e podem ter pesos atrelados à elas.

### Grafo simples

Grafo simples é um grafo que não cujas arestas não tem peso, não possui loops, e nem arestas múltiplas. Loops são arestas do tipo `(u,u)`,  ou seja, que saem do vértice `u`, e vão para o vértice `u`. Um grafo com arestas múltiplas é um grafo que tem duas aresta iguais.

### Grafos direcionados

Existem grafos direcionados e não direcionados. No caso dos direcionados, significa que cada aresta tem uma direção, ou seja, se é possível chegar de a em b, não significa que é possível chegar de b em a.

### Ciclos

A definição de ciclo é um pouco diferente dependendo se o grafo é direcionado ou não.

 Caso o grafo não seja direcionado, e hajam dois caminhos diferentes de um vértice `a` para um vértice `b`, então há um ciclo.

 Caso contrário, ou seja, caso o grafo seja direcionado, para que haja um ciclo é necessário que exista um vértice `a` tal que seja possível voltar a `a` partindo dele mesmo.

### Conexo

Um grafo conexo é um grafo tal que para dois vértices quaisquer u e w, sempre existe um caminho de u para w.

### Componentes conexas

Um grafo que não é conexo pode ter várias componentes conexas (grafos conexos tem somente uma componente). Essencialmente, uma componente conexa é um pedaço conexo do grafo. Esse conceito é usado no caso de grafos não-direcionados.

### Componentes fortemente conexas

Já nos casos do grafos direcionados, o termo usado é esse, também chamados por SCCs(Strongly Connectec Components). A definição mais 'formal' é a seguinte: dentro de uma componente conexa de um grafo direcionado, para todo vértice `a` e `b`, deve ser possível de chegar de `b` partindo de `a` e em `a` partindo de `b`.

Basicamente o que a definição acima está dizendo é: uma componente fortemente conexa é um ciclo, mas é importante lembrar que se dois ciclos se juntam, formam um ciclo maior, logo, uma componente maior.
### Árvore

Uma árvore é um tipo de grafo. Para ser uma árvore, o grafo precisa ser conexo, não ter ciclos e ter `n-1` arestas, aonde n é o número de vértices.

### DAG

DAG é uma sigla para `Directed Acyclic Graph`, ou seja, um grafo acíclico e direcionado. É um termo bastante usado.

### Grafos bipartidos

Um grafo bipartido é um grafo tal que é possível dividir seus vértices em dois grupos, de forma que só hajam arestas ligando vértices do primeiro ao segundo grupo.



## Representação de um grafo

Mas como representar um grafo em código?

Na verdade é bem mais simples do que parece. Para cada vértice, temos que manter apenas uma lista das arestas que saem daquele vértice.

Nessa representação, a i-ésima posição no vector de fora `vector<int>`, esse vector representam as arestas que saem daquele vértice. Então, cada vértice tem um número associado a ele.

### Exemplo 
<img src="http://danielamaral.wikidot.com/local--files/agmmo/Grafo.png">

```cpp
vector<vector<int>> graph(7);

graph[4].push_back(6);
graph[6].push_back(4);

graph[4].push_back(5);
graph[5].push_back(4);

graph[4].push_back(3);
graph[3].push_back(4); 

graph[2].push_back(3);
graph[3].push_back(2); 

graph[2].push_back(5);
graph[5].push_back(2); 

graph[4].push_back(6);
graph[6].push_back(4); 

graph[2].push_back(1);
graph[1].push_back(2); 


// lembrando que no caso de grafos não direcionados, quando adicionamos (a,b) precisamos sempre adicionar (b,a) junto.
```

## Representação da grafos implícitos

Considere o seguinte problema para guiar a explicação: http://codeforces.com/problemset/problem/520/B

A principio, nao parece exatamente muito fácil relacionar esse problema com grafos. Onde estão os vértices e as arestas? Na verdade nesse problema há o que chamamos de grafo implícito.

Podemos considerar os vértices como o número mostrado pelo display, e as arestas como os vértices que consigo alcançar apertando os botões, a partir do vértice que estou. Mas nós não vamos criar uma estrutura de lista de adjacências em memória igual ao caso anterior, não precisamos. A única coisa que precisamos é saber quais vértices podemos atingir a partir de um vértice `x`, então quando formos percorrer esse grafo implícito, como todos os vértices seguem essa regra, não precisamos criar as arestas propriamente ditas. Apenas sabemos que é possível atingir os vértices `x-1` e `2x`. 

O mesmo ocorre pra representação de jogos de turno, por exemplo jogo da velha ou damas. Podemos considerar uma configuração do tabuleiro como um vértice, e as arestas ligando para todas as configurações possíveis de atingir segundo as regras de jogada.

## Representação de Grafos em 'Grids'

TODO


# Percorrimento de grafos


## Percorrimento em largura

Uma das formas de percorrer um grafo é fazer um percorrimento em largura. Começamos a explorar os vértices do grafo a partir de um certo vértice `a`. E a ordem que vamos navegando entre os vértices é de forma que os mais próximos a `a` sempre serão visitados antes. Então, primeiro `a` será visitado, depois os vértices que são adjacentes a `a`(distância 1), depois os vértices que estão a duas arestas de distância, e assim por diante. Como os vértices mais próximos são sempre visitados antes, esse algoritmo serve para, por exemplo, dizer qual a distância mínima entre dois vértices no grafo.

Esse comportamento é implementado usando-se uma fila. Primeiro insere-se na fila o vértice inicial, e começa-se a desenfileirar da fila enquando houver algum nodo. Quando um vértice `a` é desenfileirado, enfileram-se todos os vértice adjacentes a `a` que não foram visitados. Caso não marquemos os visitados, e o grafo tiver ciclos, nosso programa não terminará.

O algoritmo a baixo é um BFS que simplesmente percorre o grafo.

```cpp
void bfs(int start){
    
    queue<int> q;
    q.push(start);

    vector<bool> visited(GRAPH_MAX_SIZE,false);
    visited[start] = true;
    while(q.size()){//Enquanto houver vértices na fila

        // Retire o vértice da frente
        int u = q.front();
        q.pop();
        for(int w: graph[u]){ // Para cada vértice adjacente a u
            if(not visited[w]){
                q.push(w);
                visited[w] = true;
            }
        }
    }

}
```
A complexidade desse código é `O(n+m)`, temos no máximo n vértices enfileirados, e no loop interno, cada iteração é considerar uma aresta, como não estamos voltando no grafo, não passaremos por uma aresta mais que duas vezes naquele loop. Então, em todas as chamadas, aquele loop iterará no pior caso m vezes.




## Percorrimento em profundidade

outra forma de percorrer um grafo é fazer percorrimento em profundidade, também de chamado de DFS(Depth-first search). O algoritmo  se chama assim porque funciona de uma forma que sempre vamos 'mergulhar' no grafo o mais fundo que pudermos. Quando não for mais possível ir mais fundo no grafo, voltamos até que seja ir mais fundo novamente, sem repetir vértices já visitados.

A implementação do DFS mais comum é recursiva, por ser mais intuitiva. Assim como o exemplo anterior, esse programa simplesmente percorre o grafo, mas na ordem que um DFS percorre.

```cpp
vector<vector<int>> graph;
vector<bool> visited;     // globais, inicializados na main.

void dfs(int vertex){ // na main chamamos dfs(start), aonde start é o vértice que começamos o dfs
    visited[vertex] = true;

    for(int w: graph[vertex]){
        if(!visited[w]){
            dfs(w);
        }
    }    
}
```
A complexidade desse código é `O(n+m)`, temos no máximo `n` chamadas recursivas, e no loop interno às chamadas, assim como no BFS, estaremos considerando uma aresta e não passaremos nela mais de uma vez. Então no máximo `m` iterações ao longo de todas as chamadas.



# Mais sobre grafos

## Detecção de ciclos

Detectar ciclos em um grafo é algo que é necessário com bastante frequência, muitas vezes porque um caso de borda do problema envolve o grafo ter ciclos e solução para aquele caso deve ser tratada separadamente. A detecção é diferente no caso do grafo ser direcionado ou não-direcionado, isso porque como no caso do não-direcionado, pra toda aresta (a,b) tem-se (b,a), o algoritmo usado em grafos direcionados detectaria um ciclo de tamanho 2, mas não é o caso.


### Direcionado

Para detectar ciclos em grafos direcionados, uma das abordagens possíveis é fazer um 'dfs com cores'. Isso quer dizer que enquanto percorremos nosso grafo, iremos colorindo os vértices de cinza e de preto, sendo que todos os vértices começam com a cor branca. Cada cor tem o seguinte significado:

- Branco: Não visitado
- Cinza: visitado, mas ainda está na pilha de execução.
- Preto: visitado e já saiu da pilha de execução.

A ideia é a seguinte: se enquanto estamos percorrendo o grafo, nos depararmos com um vértice que ainda está na pilha de execução então temos um ciclo. Caso o vértice seja preto, não precisamos percorrê-lo porque isso já foi feito.

```cpp
int BRANCO = 0;
int CINZA = 1;
int PRETO = 2;
int GRAPH_SIZE = 1e5;
vector<vector<int>> graph;

int cor[GRAPH_SIZE+1]; // global, inicializado como 0
bool has_cycle(int node){
    // esse vértice está na pilha, há um ciclo.
    if(cor[node] == CINZA){
        return true;
    }
    // já chamamos essa função pra esse vértice, não precisamos 
    // chamar novamente(se houver um ciclo, já foi levado em conta)
    if(cor[node] == PRETO){
        return false;
    }
    
    bool b = false;

    cor[node] = CINZA;
    // este vértice está na pilha agora
    for(auto v: graph[node]){
        b |= has_cycle(v); // basta que uma instância do dfs detecte ciclo para
        // que esse 'true' se propague pela pilha toda.
    }
    cor[node] = PRETO;
    // este vértice saiu da pilha
    return b;
}

int main(){
    // preenche grafo

    bool cycle = false;
    for(int i = 0; i < GRAPH_SIZE; i++){
        if(cor[i] == BRANCO){// só faz sentido começar o dfs de vértices brancos.
            cycle |= has_cycle(i); 
        }
    }
}

```

A complexidade desse código é a complexidade de um DFS, porque é isso que estamos fazendo no fim das contas.

### Não-direcionado

O algoritmo é mais simples no caso do não direcionado. Também faremos uma DFS, mas na nessa DFS, iremos pra todos os nós adjacentes, exceto o nó que chamou aquele, para que não fiquemos voltando.

Se fizermos isso, e acabarmos num nó que já foi visitado, isso significa que tem um caminho de volta para aquele nó sem ser voltando por onde já passamos, o que configura um ciclo no grafo não-direcionado.

```cpp
int GRAPH_SIZE = 1e5;
vector<vector<int>> graph;
bool visited[GRAPH_SIZE+1]; //inicializado como false

bool has_cycle(int node, int last){
    // voltamos pra um nodo que já visitados, há um ciclo.
    if(visited[node])
        return true;
    visited[node] = true;

    bool b = false;
    for(auto v: graph[node]){
        // visitamos todos os adjacentes, menos o que chamou essa instância da função.
        if(v != last){
            b |= has_cycle(v,node);
        }
    }
    return b;
}

int main(){
    // preenche o grafo

    bool cycle = false;
    for(int i = 0; i < GRAPH_SIZE; i++){
        if(!visited[i]){
            cycle |= has_cycle(i,-1);
        }
    }
}

```

A complexidade desse código também é `O(n+m)`, pelo mesmo motivo do código anterior.


## VideoAulas complementares

https://www.youtube.com/watch?v=zaBhtODEL0w