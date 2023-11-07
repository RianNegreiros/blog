# HackerRank: Compare The Triplets em Go

Alice and Bob each created one problem for HackerRank. A reviewer rates the two challenges, awarding points on a scale from 1 to 100 for three categories: problem clarity, originality, and difficulty.
The rating for Alice's challenge is the triplet a = (a[0], a[1], a[2]), and the rating for Bob's challenge is the triplet b = (b[0], b[1], b[2]).
The task is to find their comparison points by comparing a[0] with b[0], a[1] with b[1], and a[2] with b[2].
If a[i] > b[i], then Alice is awarded 1 point.
If a[i] < b[i], then Bob is awarded 1 point.
If a[i] = b[i], then neither person receives a point.
Comparison points is the total points a person earned.
Given a and b, determine their respective comparison points.

```go
func compareTriplets(a []int32, b []int32) []int32 {
    var triplets []int32
    x := 0
    y := 0
    for i, n := range a {
        if n > b[i]{
            x += 1
        }else if n < b[i]{
            y += 1
        }   
    }
    triplets = append(triplets, int32(x))
    triplets = append(triplets, int32(y))

    return triplets    
}
```

O problema é sobre comparar as classificações de Alice e Bob em três categorias diferentes (clareza do problema, originalidade e dificuldade) e determinar quantos pontos cada um ganha em comparação com o outro. Eles recebem 1 ponto se a classificação de Alice for maior do que a de Bob em uma categoria específica, 1 ponto se a classificação de Bob for maior e nenhum ponto se as classificações forem iguais.

A solução apresentada é uma função chamada compareTriplets que recebe duas fatias (slices) de inteiros, a e b, representando as classificações de Alice e Bob, respectivamente. Ela retorna uma fatia com dois inteiros, onde o primeiro valor é a pontuação de Alice e o segundo valor é a pontuação de Bob.

A função percorre os elementos das fatias a e b em um loop, comparando as classificações correspondentes em cada categoria. Se a classificação de Alice for maior, adiciona 1 ponto a x, que representa a pontuação de Alice. Se a classificação de Bob for maior, adiciona 1 ponto a y, que representa a pontuação de Bob. Se as classificações forem iguais, nenhum ponto é atribuído. No final do loop, os valores de x e y são usados para criar uma fatia triplets, que é retornada pela função.

Em resumo, esta função calcula a pontuação de Alice e Bob com base em suas classificações em três categorias diferentes, de acordo com as regras especificadas no enunciado do problema.
