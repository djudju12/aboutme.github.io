---
layout: project
title: "Invertendo uma Árvore Binária"
date: 2023-10-27
ready: true
# details: ""
src: ""
---

Nesse post eu gostaria de falar sobre árvores binárias, mais especificamente sobre como inverte-las. A motivação para esse post veio de um querido amigo que se deparou com esse problema em uma entrevista de emprego.

Bom, antes de qualquer coisa vou explicar rapidamente o que é uma **árvore binaria**. Para isso vou usar o exemplo do livro _The C Programming Language_.

Image que você tem um input constituído por diversas palavras distribuídas aleatoriamente e você que contar a ocorrência de todas elas. Como você faria isso?

Bom, uma solução possível é utilizar uma busca linear para cada uma das palavras e incrementar um contador se ela for encontrada. Porém, essa solução demoraria muuuito (O(n²)).

Uma outra solução seria manter a lista de palavras sempre ordenadas e buscar as palavras através de uma [busca binária](https://pt.wikipedia.org/wiki/Pesquisa_bin%C3%A1ria). Essa solução é boa, porém a ordenação não deveria ser feita de forma linear, pois isso demoraria tanto quanto a solução anterior.

Pois então, como podemos organizar nossos dados para lidar eficientemente com uma lista de palavras arbitrarias? A resposta é: **Árvores Binárias**.

A árvore conterá um `nodo` por palavra distinta e cada nodo conterá:

- um ponteiro para a palavra
- um contador de ocorrências
- um ponteiro para o nodo filho da esquerda
- um ponteiro para o nodo filho da direita

Nenhum nodo poderá ter mais que dois filhos, mas cada nodo pode ter um ou nenhum filho.

Podemos representar essa estrutura da seguinte forma:
[imagem aq]

Em C:

```c
typedef struct {
    char *word;
    int count;
    Node *left;
    Node *right;
} Node;
```

Cada nodo terá a característica de que todas os nodos filhos à esquerda serão lexicograficamente menores que o nodo pai e todos os filhos à direita serão maiores.

[exemplo]

Para achar um certo nodo, começaremos testando a palavra pela raiz, se forem iguais incrementamos o contador, se for menor então continuaremos procurando à esquerda e se for maior procure à direta.

Se nenhum nodo for encontrado então inserimos o novo nodo no lugar onde a busca terminou.

```c
Node* treeadd(Node *p, char *w)
{
    int cond;

    // p == NULL significa não encontramos a palavra
    if (p == NULL) {
        // new_node() apenas alocará memória para um novo nodo
        p = new_node();
        
        // strdup(w) duplica a string ws
        p->word = strdup(w); 
        p->count = 1;
        p->left = p->right = NULL;

    // strcmp(...) retorna zero se as strings são iguais
    } else if ((cond = strcmp(w, p->word)) == 0) {
        // como as strings são iguais, incrementamos o contador
        p->count++;
    } else if (cond < 0) {
        p->left = treeadd(p->left, w);
    } else {
        p->right = treeadd(p->right, w);
    }

    return p;
}
```

Agora, na média levaríamos O(log n) para achar uma palavra, e no pior caso levaríamos O(n), o que já é um grande avanço.

Agora que já sabemos como criar uma árvore binária, vamos ao que interessa: como inverte-la? 

A resposta é simples:

Começamos invertendo os filhos da raiz, depois os filhos dos filhos da raiz, e assim por diante.
