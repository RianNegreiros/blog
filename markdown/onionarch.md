# Onion Architecture

![Fonte: [https://commons.wikimedia.org/wiki/File:Clean_Architecture_core.svg](https://commons.wikimedia.org/wiki/File:Clean_Architecture_core.svg)](https://upload.wikimedia.org/wikipedia/commons/9/9c/Clean_Architecture_core.svg)

Fonte: [https://commons.wikimedia.org/wiki/File:Clean_Architecture_core.svg](https://commons.wikimedia.org/wiki/File:Clean_Architecture_core.svg)

Apresentada pela primeira vez por [Jeffrey Palermo](https://jeffreypalermo.com/) em uma [série de postagens de blog](https://jeffreypalermo.com/tag/onion-architecture/), a Onion Architecture orienta os engenheiros de software a modelar sua lógica de negócios em uma coleção **principal** sem acoplamento com as preocupações externas, como a escolha do banco de dados ou o funcionamento da interface do usuário.

Qual é a aparência da Onion Architecture? Pode ser uma surpresa.

Ela se parece muito com uma cebola, com camadas que envolvem um núcleo central. Cada uma dessas camadas representa uma tarefa específica dentro da função geral de um serviço. Da mesma forma que uma cebola, só é possível acessar o núcleo passando pelas camadas mais externas, e é essa narrativa que nos informa sobre o objetivo das arquiteturas: direcionar o fluxo de acoplamento para o centro, de fora para dentro.

Portanto, como em uma cebola típica, vamos nos aprofundar no núcleo e, com sorte, evitar quaisquer rasgos ao longo do caminho. As três camadas externas são aquelas que não estão diretamente relacionadas à nossa lógica de negócios, mas dependem dela para cumprir seu próprio objetivo. Elas podem mudar com frequência e, portanto, são separadas da nossa lógica de aplicativo principal.

Essas camadas são: **Infraestrutura**, onde estão nosso banco de dados, sistema de arquivos ou qualquer serviço externo da Web do qual dependemos. **Testes**: unitários, de integração, de ponta a ponta. Como validamos nossos casos de negócios. Por fim, a **User Interface**, como nossos usuários interagem com o código que criamos. Essas camadas são as que interagem com a primeira camada do nosso "**application core**", que é a camada de **serviços** (às vezes conhecida como **camada de transporte**). Nessa camada, definimos o que o nosso serviço pode fazer por meio de uma série de contratos.

Ao nos movermos para dentro, encontramos a camada de **serviços de domínio**. É nessa camada que reside a maioria da nossa lógica de negócios, que realiza as operações para transformar A em B, entrada em saída, ovo em galinha. Isso é feito por meio da interação com a camada final, a camada de **modelo de domínio**, a qual é a representação dos objetos de dados de alto nível que usamos.

Vamos examinar um exemplo de como podemos resolver uma tarefa do mundo real, como o processamento de uma transação financeira, para ver como aplicamos a **Onion Architecture.**

## Um exemplo - Fazer uma compra

Uma camada externa que pode surpreender a muitos é a **Infraestrutura**. O banco de dados que usamos ou uma dependência externa não faz parte da nossa camada de modelo de domínio? Uma pergunta muito válida, então vamos explorá-la mais a fundo.

Por exemplo, temos a seguinte definição de uma conta de usuário em algum banco de dados **NoSQL**:

```json
{
	"id": "user_id",
	"saldo": 500.00,
	"moeda": "BRL",
}
```

Portanto, quando fizermos uma consulta e quisermos interagir com isso, seria bom criar um objeto de modelo para transformar esse **JSON** em algo como:

```go
type Conta struct {
	ID       string    `json:"id"`
	Saldo    string    `json:"saldo"`
	Moeda    float64   `json:"moeda"`
}
```

Faz sentido, certo? Digamos, no entanto, que a equipe decida que o invés do NoSQL usem a tendência do mês. Bem, graças ao poder da onion architecture, desde que a nova estrutura relacional forneça os campos necessários, conforme definido no **contrato** do modelo de domínio, a lógica comercial do aplicativo não precisa ser alterada e você pode continuar a fornecer a funcionalidade de que os usuários precisam. Isso se aplica a qualquer dependência externa ou armazenamento de dados com os quais o aplicativo interage, sendo a principal lição:

```
Externalize suas dependências e desacoplá-las por meio de contratos.
```

Agora que temos uma representação do modelo de domínio de uma conta, precisamos definir um caso de uso para uma transação financeira e as etapas envolvidas quando nosso usuário, André, decide comprar algo:

Como podemos ver no diagrama, precisamos definir um contrato para permitir que a "Generic Shop" cobre o saldo de André pelo custo da compra. Vamos supor que a "Generic Shop" se comunicará conosco por meio de uma solicitação **HTTP POST**, e podemos ver que precisamos do seguinte:

- A ID de usuário de André
- O valor solicitado para fazer a compra

A representação **JSON** dessa solicitação seria:

```json
{
	"userID": "user_id",
	"saldo": 100.00,
}
```

O nome desse caso de uso seria cobrar o saldo de um usuário e, conforme mencionado, é na camada de **serviços de aplicativos** que definimos isso. Para representar isso no código, precisaremos incorporá-lo ao nosso código de handler http:

```go
package application

import (
	"net/http"
	"encoding/json"
)

// ChargeRequest é a nossa representação de uma solicitação recebida.
type ChargeRequest struct {
	UserID string  `json:"userID"`
	Saldo  float64 `json:"saldo"`
}

// O ChargeUserHandler recebe uma solicitação HTTP POST e decodifica
// o body em um ChargeRequest para que possamos cobrar
// o saldo do usuário pelo custo da transação.
func ChargeUserHandler(w http.ResponseWriter, r *http.Request) {
	var cr ChargeRequest
	
	err := json.NewDecoder(r.Body).Decode(&cr)
	if err != nil {
		http.Error(w, err.Error(), http.StatusBadRequest)
		return
	}

	if cr.UserID == "" {
		http.Error(w, "user id esta vazio", http.StatusBadRequest)
		return
	}

	// Ligue para nossa equipe de serviços de domínio

	return
}
```

Excelente! Agora definimos nossas expectativas para qualquer pessoa que deseje cobrar de um usuário por uma transação em nossa camada de **serviços de aplicativos**. No entanto, no momento, não estamos fazendo nada de útil com a transação, portanto, a partir disso e seguindo as camadas da **Onion Architecture**, precisamos definir nossa camada de **Serviços de Domínio**.

Como desenvolvedores, vamos dedicar algum tempo para definir nosso fluxo:

1. Verifique o saldo da conta do André e se ela tem fundos suficientes para cobrir o custo da transação.
    - Caso contrário, ou se não houver uma conta para André, retorne um erro informando isso.
2. Agora que temos a conta, deduzimos o custo da transação do saldo do André e o atualizamos em nosso banco de dados.
    - Se a atualização falhar, precisamos retornar um erro informando isso. Não queremos que ninguém receba um café de graça.
3. Agora que o saldo foi atualizado, devolva a confirmação para a "Generic Company", concluindo assim a transação e permitindo que André tome seu delicioso café.

Felizmente, já definimos nosso modelo de domínio de conta que podemos usar, portanto, encapsular tudo isso no código seria algo parecido com:

```go
package service

import (
	"database/sql"
	"fmt"
)

// A interface DB define as operações permitidas em nosso banco de dados.
type DB interface {
	StartTransaction() (*sql.Tx, error)
	GetUserAccount(tx *sql.Tx, userID string) (Conta, error)
	UpdateUserAccount(tx *sql.Tx, userID string, conta Conta) error
}

// O ChargeService executa a lógica comercial para cobrar os saldos dos usuários
type ChargeService struct {
	db DB
}

// O ChargeUser recebe um ID de usuário e um valor e, em seguida, deduz esse valor do
// saldo desse usuário, se ele tiver o suficiente.
func (cs *ChargeService) ChargeUser(userID string, valorCobranca float64) error {
	transaction, err := cs.db.StartTransaction()
	if err != nil {
		return fmt.Errorf("erro ao iniciar transacao")
	}
	defer func() {
    	_ = transaction.Rollback()
	}()

	conta, err := cs.db.GetUserAccount(transaction, userID)
	if err != nil {
		return err
	}
	if conta.Saldo < valorCobranca {
		return fmt.Errorf("saldo insuficiente")
	}

	conta.Saldo = conta.Saldo - valorCobranca
	err = cs.db.UpdateUserAccount(transaction, userID, conta)
	if err != nil {
		return err
	}

	err = transaction.Commit()
	if err != nil {
		return fmt.Errorf("erro ao confirma transacao")
	}

	return nil
}
```

Voltando aos casos que mapeamos acima, podemos ver que capturamos a lógica comercial necessária em nosso código da camada de **serviço de domínio** e agora podemos adicionar a chamada de função em nosso manipulador:

```go
// O ChargeUserHandler recebe uma solicitação HTTP POST e decodifica
// o body em um ChargeRequest para que possamos cobrar
// o saldo do usuário pelo custo da transação.
func ChargeUserHandler(w http.ResponseWriter, r *http.Request) {
	var cr ChargeRequest
	
	err := json.NewDecoder(r.Body).Decode(&cr)
	if err != nil {
		http.Error(w, err.Error(), http.StatusBadRequest)
		return
	}

	if cr.UserID == "" {
		http.Error(w, "user id esta vazio", http.StatusBadRequest)
		return
	}

	// Novo código aqui
	db := DatabaseAdapter{}
	chargeService := service.ChargeService{db: db}
	err := chargeService.ChargeUser(cr.UserID, cr.Amount)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
	}

	return
}
```

Agora, se implantarmos esse serviço, quando André decidir fazer uma compra, poderemos ter certeza de que atenderemos aos requisitos definidos em nossa investigação e estaremos confiantes de que nossa lógica está ordenada em camadas (supondo que tenhamos o nível adequado de testes em vigor). Esse é um caso de uso simples, mas a verdadeira pergunta que está sendo feita é por quê.

## Por que usar a ****Onion Architecture ?****

Mas quais são os benefícios de usar a **Onion Architecture**?

São os contratos entre cada camada que definimos, também conhecidos como **Princípio de Inversão de Dependência**, dos quais a **Onion Architecture** depende fortemente. Desde que nossas camadas sigam os contratos/interfaces definidos em nosso código, podemos utilizá-los conforme mencionado em nosso debate sobre NoSQL ou SQL.

O uso de contratos permite que cada camada defina suas expectativas em relação à próxima e a associe **apenas** ao que ela precisa ser. Além disso, as especificidades de implementação de cada camada podem ser refatoradas a qualquer momento, desde que cumpram suas obrigações contratuais com seus vizinhos, facilitando a resposta às mudanças e aumentando a capacidade de teste do nosso código.

Entretanto, esse padrão de arquitetura não é uma solução mágica para todos os problemas. Como em todos os problemas de software, precisamos avaliar se precisamos ou não dessa abstração adicional, pois ela é mais adequada para aplicativos maiores com muitos engenheiros trabalhando neles. Como engenheiros, precisamos aplicar o pensamento crítico para determinar se isso beneficiará ou não a tarefa em questão. Além disso, a complexidade adicional de definir contratos/interfaces e aplicá-los religiosamente exige um sólido entendimento do padrão. Se bem executado, os benefícios aumentarão a produtividade e a flexibilidade dos aplicativos que estão sendo desenvolvidos.
