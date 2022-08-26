# Documentação para usar função helper para os mocks em testes

## Porque de sua criação
Gostáriamos de separar os mocks  em outro arquivo para "descontaminar" os arquivos de testes
e tambem evitar o uso da funçao mock em objetos pois gera alguma complicações em alguns testes
onde os mocks tem que ser mais especificos, trocando apenas alguma ou algumas propriedades, o que
leva ao criar toda a estrutura de um mock novamente.

Por isso criamos essa funçao que tendo um objeto de base substitui apenas os valores passados a ela
tirando a necessiade de se criar todo um objeto de mock novamente

**Para isso basta usar a função changeValuesMock**

## Como usar:
```ts
type CreateUserRequest = {
  name: string;
  email: string;
  address: {
    street: string;
    number: number;
  },
  interests: ['exemplo1', 'exemplo2'],
  birthDate: Date;
  friends: {
    name: string;
    email: string;
  }[];
}

const makeCreateUserRequest = (
  valuesToBeChanged?: CreateUserRequest,
  replaceInsideListObject = false,
) => changeValuesMock<CreateUserRequest>({
  name: faker.datatype().string(),
  email: faker.internet().email(),
  address: {
    street: faker.datatype().string(),
    number: faker.datatype().number(),
  },
  interests: faker.datatype().array(),,
  birthDate: faker.date().past();
  friends: [
    {
      name: faker.datatype().string(),
      email: faker.internet().email(),
    },
    {
      name: faker.datatype().string(),
      email: faker.internet().email(),
    }
  ]
}, valuesToBeChanged, replaceInsideListObject)
```
## Explicando os parametros na funçao makeCreateUserRequest:
 - **ValuesToBeChanged** => Sao os argumento que devem ser substituidos no mock base;
 - **replaceInsideListObject** => Argumento true ou false que informa a funçao quer que ela percorra os arrays que contenham objetos trocando os valores informados, ou se ela substitua o array por inteiro.
  True => Irá percorrer todos os objetos dentro do array
  False => Irá substituir todo o array pelo novo passado em ValuesToBeChanged

## Explicando os parametros da funçao changeValuesMock
 - **1-** => O mock base que será usado pela funçao;
 - **2-** => Valores a serem substítuidos no mock base pela função
 - **3-** => Argumento true ou false ja explicado a cima.

**Lembrando que se nada for passado no argumento valuesToBeChanged a função retornará o mockBase passado a ela**

**Se o parâmetro replaceInsideListObject for true, o array deve ser do mesmo tamanho do que se encotra no mock base**

## Exemplo de chamada da função makeCreateUserRequest:

```ts
const makeCreateUserRequest = (
  valuesToBeChanged?: CreateUserRequest,
  replaceInsideListObject = false,
) => changeValuesMock<CreateUserRequest>({
  name: faker.datatype().string(),
  email: faker.internet().email(),
  address: {
    street: faker.datatype().string(),
    number: faker.datatype().number(),
  },
  interests: faker.datatype().array(),,
  birthDate: faker.date().past();
  friends: [
    {
      name: faker.datatype().string(),
      email: faker.internet().email(),
    },
    {
      name: faker.datatype().string(),
      email: faker.internet().email(),
    }
  ]
}, valuesToBeChanged, replaceInsideListObject)

const createUserRequestMock = makeCreateUserRequest({
  name: 'Matheus',
  address: {
    number: 434,
  },
  interests: ['Exemplo1', 'Exemplo2'],
  friends: [
    {},
    {
      name: 'thiago narcizo',
      email: 'thiago_gostoso@delicia.com'
    }
  ]
}, true);
```
Pode notar que no exemplo de chamada da função makeCreateUserRequest no parametro friends está enviado um array
com um objeto vazio e o segundo com valores. Isto resultará que o primeiro objeto no mock base nao será alterado, apenas o segundo. **SE ATENTAR DE TER O MESMO TAMANHO OS ARRAYS E PASSAR O SEGUNDO PARAMETRO DA FUNÇAO COMO TRUE**


