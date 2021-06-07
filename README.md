<h1 align="center">
  Redis II: Estruturas e recursos na sua base NoSQL
</h1>

<p align="center">Exercícios do curso de Redis II do Alura.</a>
</p>

<p align="center">
  
  <img alt="GitHub language count" src="https://img.shields.io/github/languages/count/dpalmas/redis2?color=0000FF">

  <img alt="License" src="https://img.shields.io/github/license/dpalmas/redis2?color=0000FF&logo=MIT">
  
  <a href="https://github.com/dpalmas/redis2/commits/master">
    <img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/dpalmas/redis2?color=0000FF">
  </a>
</p>

### Inserindo elementos em uma lista
**1° Neste capítulo, vimos uma nova estrutura de dados suportada pelo Redis: listas. Vamos agora criar uma chave associada a uma lista que guarda as últimas noticias de um site. Como visto nas aulas, crie uma chave chamada ultimas_noticias e adicione as seguintes notícias considerando a última como sendo a mais recente: :pencil:**

- Jogador de futebol e flagrado jogando basquete na rua
- Novo curso de Redis e lancado pelo Alura
- Guilherme Silveira faz aniversario e espera presentes... sentado
  
```
LPUSH ultimas_noticias "Jogador de futebol e flagrado jogando basquete na rua"
LPUSH ultimas_noticias "Novo curso de Redis e lancado pelo Alura"
LPUSH ultimas_noticias "Guilherme Silveira faz aniversario e espera presentes...sentado"
```

### Recuperando um elemento de uma lista
**2° Qual o comando necessário para recuperar a notícia mais recente armazenada na nossa lista? E para recuperar as duas notícias mais recentes, como podemos fazer? :pencil:**

```
LINDEX utlimas_noticias 0
LINDEX ultimas_noticias 0 1
```

### Tamanho da lista
**3° Qual comando podemos utilizar para descobrir o tamanho de nossa lista? Como podemos fazer para deixar a lista com apenas as três últimas notícias? :pencil:**

```
LLEN ultimas_noticias
LTRIM ultimas_noticias 0 2
LLEN ultimas_noticias
```
### Utilização de filas no Redis
**4° Cite alguns exemplos onde podemos utilizar filas. Em algum momento da sua carreira você já passou por situações onde acredita que o uso de filas no Redis se encaixaria no projeto que você participou? Caso tenha acontecido, conte um pouco sobre o(s) projeto(s). :pencil:**

Uma utilização comum das filas, é quando desejamos executar tarefas em background, para que o usuário não tenha que esperar a tarefa ser concluída e possa realizar outras atividades de forma paralela. No caso de um sistema onde é necessário confirmação por e-mail ou SMS, podemos executar esse envio em segundo plano e a medida que usuários vão necessitando de confirmação vamos armazenando-os em uma fila, onde o primeiro que chega é o primeiro que terá sua solicitação atendida.

Um outro exemplo de uso de fila, é um chat de atendimento, onde a medida que os usuários precisam esperar os atendentes estarem disponíveis, eles são colocados em uma fila.

### Adicionando elementos em uma fila
**5° Vamos agora representar um sistema de envio de e-mails de confirmação. Para isso armazene os seguintes valores associados à chave "fila:confirma-email". Lembre-se de inserir os elementos pela direita da lista: :pencil:**

- "guilherme.silveira@alura.com.br"
- "daniela.mikyung@alura.com.br"
- "carlos.felicio@alura.com.br"

Como podemos confirmar que a lista possui realmente 3 elementos?
Como podemos recuperar todos os elementos da lista e garantir que eles estão inseridos na ordem que desejamos?

```
RPUSH "fila:confirma-email" "guilherme.silveira@alura.com.br"
LLEN "fila:confirma-email"
RPUSH "fila:confirma-email" "daniela.mikyung@alura.com.br"
LLEN "fila:confirma-email"
RPUSH "fila:confirma-email" "carlos.felicio@alura.com.br"
```

### Recuperando e removendo o valores
**6° Como podemos remover e recuperar o primeiro valor da fila, de forma que a operação seja realizada de maneira atômica, garantindo que nenhum outro valor será adicionado nesse meio tempo e acabe trazendo resultados indesejados? :pencil:**

E se houver necessidade de remover e recuperar o último elemento, qual comando seria necessário?
Confira o tamanho da fila, que agora deve ser 1.

```
LPOP "fila:confirma-email"
POP "fila:confirma-email"
LEN "fila:confirma-email"
```

### Problema ao executar o LPOP
**7° De acordo com o que vimos nesse capítulo, que nome damos ao tipo de situação na qual ficamos a todo momento executando o comando LPOP para remover um valor de uma fila sem elementos até que haja um elemento para remover? :pencil:**

- [ ] Pop Bloquante

:white_check_mark: Busy Waiting

- [ ] Spin Lock

- [ ] Synchronization

### Pop blocante
**8° Qual o comando necessário para remover o primeiro elemento na fila cuja chave é "fila:confirma-email" de forma que ele fique bloqueado por 60 segundos caso não exista valor a ser removido? :pencil:**

- [ ] LPOP "fila:confirma-email" 60

- [ ] RPOP "fila:confirma-email" 60

:white_check_mark: BLPOP "fila:confirma-email" 60

- [ ] BLPOP "fila:confirma-email" 60000

### Utilizando o BLPOP
**9° Remova todos os elementos da nossa fila. Utilize o comando BLPOP para ficar bloqueado por um tempo inderteminado. Agora abra um novo terminal, inicie o cliente do Redis e insira um valor na fila. Como resposta, forneça os comandos necessários, e também o resultado do comando BLPOP após a inserção de um valor na fila. :pencil:**

```
BLPOP fila:confirma-email 0
RPUSH fila:confirma-email 
```

### Características de um conjunto
**10° Quais as principais diferenças de um conjunto em relação as listas? :pencil:**

Quando utilizamos conjuntos, não conseguimos recuperar um elemento em um determinado índice, pois não temos garantia de que a ordem dos valores será mantida. Outra diferença em relação as listas, é que conjuntos não permitem elementos repetidos.

### Utilizando conjuntos no Redis
**11° Vamos implementar agora um exemplo para representar os amigos de um usuário de uma rede social. Adicione os seguintes valores em um conjunto associados à chave "relacionamentos:guilherme":  :pencil:**

- "daniela"
- "carlos"
- "ana"
- "lucia"

Como podemos descobrir a quantidade de elementos que no nosso conjunto?

```
SADD "relacionamentos:guilherme" "daniela" "carlos" "ana" "lucia"
SCARD "relacionamentos:guilherme"
```

### Removendo elementos de um conjunto
**12° Qual o comando necessário para remover o elemento "ana" do conjunto "relacionamentos:guilherme"? :pencil:**

- [ ] SDEL "relacionamentos:guilherme" "ana"

- [ ] SREMOVE "relacionamentos:guilherme" "ana"

:white_check_mark: SREM "relacionamentos:guilherme" "ana"

- [ ] SREM "ana" "relacionamentos:guilherme"

### Verificando os menbros de um conjunto
**13° Qual comando podemos utilizar para recuperar todos os elementos do conjunto "relacionamentos:guilherme"? E qual o comando para descobrir se o elemento "marcela" é um membro do conjunto "relacionamentos:guilherme"? :pencil:**

```
SMEMBERS "relacionamentos:guilherme"
SISMEMBER "relacionamentos:guilherme" "marcela"
```

### Intersecção e diferença de conjuntos
**14° Agora, crie um novo conjunto associado à chave "relacionamentos:marcela", com os seguintes elementos: :pencil:**

- "daniela"
- "ana"
 -"paulo"

Como podemos fazer para recuperar os amigos comuns entre "relacionamentos:guilherme" e "relacionamentos:marcela"?
Qual comando podemos utilizar para descobrir quem o Guilherme("relacionamentos:guilherme") conhece que a Marcela (relacionamentos:marcela) não conhece? E para descobrir quem a Marcela conhece que o Guilherme não conhece?

```
SINTER "relacionamentos:guilherme" "relacionamentos:marcela"
SDIFF "relacionamentos:guilherme" "relacionamentos:marcela"
SDIFF "relacionamentos:marcela" "relacionamentos:guilherme"
```

### Para saber mais: União de conjuntos
**15° Uma outra operação que podemos fazer com conjuntos no Redis é a união. Nesta operação, o Redis irá pegar os elementos dos dois conjuntos e retornar todos eles, sem repetição. Tente executar o comando: :pencil:**

```
SUNION "relacionamentos:guilherme" "relacionamentos:marcela"
```

Qua o resultado?

```
1) "lucia"
2) "ana"
3) "carlos"
4) "daniela"
5) "paulo"
```

### Conjuntos ordenados
**16° Agora vamos implementar um exemplo de um ranking, similar ao que temos hoje no Alura, onde um aluno possui uma determinada pontuação. Queremos que os dados estejam ordenados de acordo com a pontuação, e para isso vamos armazenar no Redis utilizando um Sorted Set, ou conjunto ordenado. Armazene no conjunto ordenado "pontuações" os seguintes valores: :pencil:**

- 50076 guilherme
- 65543 carlos
- 33786 daniela
- 8754 paulo

Qual comando utilizamos se quisermos saber o tipo da chave "pontuações"?

```
ZADD pontuacoes 50076 guilherme
ZADD pontuacoes 65543 carlos
ZADD pontuacoes 33786 daniela
ZADD pontuacoes 8754 paulo
TYPE "pontuacoes"
```

### Descobrindo a cardinalidade
**17° Qual comando podemos utilizar para descobrir a cardinalidade do conjunto ordenado criado anteriormente? :pencil:**

```
ZCARD pontuacoes
```

### Visualizando os elementos
**18° Como podemos fazer para visualizar os elementos do conjunto criado? E se quisermos ver em ordem reversa ao que temos? :pencil:**

```
ZRANGE pontuacoes 0 3
ZREVRANGE pontuacoes 0 3
```

### Buscando os usuários com os pontos
**19° Como podemos fazer para trazer os elementos dos conjuntos e seus pontos de uma forma que não tenha perigo de que enquanto digitamos o comando um novo elemento seja adicionado e acabe ficando de fora? :pencil:**

```
ZREVRANGE pontuacoes 0 -1 WITHSCORES
```

### Recuperando elementos
**20° Que comando poderíamos utilizar para trazer o penúltimo e o último elemento, com a pontuação, do nosso conjunto? :pencil:**

- [ ]  ZRANGE pontuacoes 0 -1 WITHSCORES

- [ ] ZRANGE pontuacoes 0 -2 WITHSCORES

- [ ] ZRANGE pontuacoes -1 -2 WITHSCORES

:white_check_mark: ZRANGE pontuacoes -2 -1 WITHSCORES

### Buscando a pontuação dos usuários
**21° Use o comando necessário e adicione 50000 pontos ao elemento "guilherme". Qual o comando necessário para saber a pontuação do elemento "paulo"? E se quisermos saber a posição do elemento "guilherme" no ranking em ordem crescente. E em ordem descrescente, que é o mais comum quando estamos trabalhando com rankings? :pencil:**

```
ZINCRBY pontuacoes 50000 guilherme
ZSCORE pontuacoes paulo
ZRANK pontuacoes guilherme
ZREVRANK pontuacoes guilherme
```

### Armazenando os ids dos usuários
**22° Para evitar que no nosso sistema só possa haver usuários com nomes diferentes, já que estamos armazenando o nome como elemento do conjunto, vamos passar a armazenar os ids dos usuários associados aos pontos. Para isso remova o nosso conjunto atual: :pencil:**

```
DEL pontuacoes
```

Adicione um novo conjunto à chave "pontuacoes" com os seguintes ids:

- Guilherme - 55
- Ana - 35
- Daniela - 65

Utilize o ZRANGE e verifique se agora temos os ids atrelados aos pontos.

```
ZADD pontuacoes 50000 55
ZADD pontuacoes 30000 35
ZADD pontuacoes 300000 65
ZRANGE pontuacoes 0 -1
```