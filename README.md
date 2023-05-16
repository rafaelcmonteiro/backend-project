# Teste Pessoa Desenvolvedora Back-end Amo Promo V1.1

# ![Amopromo](https://www.amopromo.com/images/logo-amo-promo.svg)

#### Nossas Marcas
|   Passagens Promo   |  Seguros Promo  |    Parceiros Promo    |
| :---:         |     :---:      |          :---: |
| ![PassagensPromo](https://www.amopromo.com/images/logo-passagens-promo.svg)   | ![SegurosPromo](https://www.amopromo.com/images/logo-seguros-promo.svg) |  ![ParceirosPromo](https://www.amopromo.com/images/logo-parceiros-promo.svg) |

### O que queremos:
Estamos interessados em entender sua lógica de programação e resolução objetiva de problemas, portanto não faz parte do desafio a criação de artefatos de planejamento/gestão de projetos, como: cronogramas, diagramas, burndown, quadro de demandas e etc. Nós amamos testes, apesar de não ser obrigatório, seria um grande diferencial qualquer tipo de teste (unitários, integração e etc).

Sua capacidade de interpretação também está sendo avaliada, mas lembre-se que não existe uma solução única para o teste! Então, siga sua interpretação e use a criatividade para completar as tarefas especificadas. Havendo tempo e disponibilidade, incrementar com features/detalhes que julgar interessantes. Boa sorte!

Na Amo Promo, trabalhamos todos os dias com diversas combinações de voos e aeroportos para encontrar a melhor viagem para o nosso cliente. Nesse teste, você deverá lidar com 2 problemas muito comuns no nosso dia-a-dia. Processar dados de rotina e criar um serviço que consuma uma API (de uma companhia aérea ficticia) e retorne os vôos.

## Problema 1:
Consumir uma API todas as vezes que precisamos buscar aeroportos estava ficando muito moroso e consumindo muitos recursos.

Precisamos fazer cache dos aeroportos em nosso banco de dados. Como a lista de aeroportos é dinâmica, precisamos atualizar diariamente esta base.

O desafio é criar um serviço/comando/CLI que permita processar diariamente a [Domestic Airports API](http://stub.2xt.com.br/air/airports/pzrvlDwoCwlzrWJmOzviqvOWtm4dkvuc), inserindo e atualizando registros.

*Basic Auth*
Login: `demo`
Senha: `swnvlD`

> Dica: Se você utilizar Django Framework, poderá fazer algo como...
> `python manage.py import_airports`

#### Importante:
- Utilize o Banco de Dados que quiser. SQLite, Postgres, Mysql, MongoDB ou serviços de BD em nuvem.
- A modelagem do banco de dados fica a seu critério. Pense na melhor organização possível dos dados para fácil entendimento e boa performance.

> Dica: Utilizar SQLite pode reduzir drasticamente o tempo de desenvolvimento

## Problema 2:
Surgiu uma nova companhia aérea, *Mock Airlines Inc* no mercado e devemos fazer a integração com a mesma para disponilizar os voos para nossos clientes.

Como de costume, não temos controle sobre as informações entregues pelos fornecedores. Portanto será necessário adaptar os dados recebidos para atender as nossas necessidades.

*Basic Auth*
Login: `demo`
Senha: `swnvlD`
apikey: `pzrvlDwoCwlzrWJmOzviqvOWtm4dkvuc`

Após um estudo da documentação [Mock Airlines Inc API](http://stub.2xt.com.br/air/search), notamos que:

1. A API desta companhia não permite combinar voos de ida e volta (duas datas) em uma única chamada.
2. Precisamos preencher as informações dos blocos `price` e `meta` de cada voo.

O desafio é criar uma API REST HTTP com um `endpoint` que faça o consumo da API desta nova companhia e modificar/padronizar o retorno dela para que tenha uma interface em comum com as nossas outras integrações e para que seja consumida por outros serviços. Deverá ser retornado um resumo com as informações da viagem, as opções de voos ida/volta com meta e price para cada opção e combinações de **ida X volta**.

#### Antes de detalhar o desafio, vamos levantar alguns pontos:
- Não é necessário criar um frontend para o exibição/consumo da api.
- Este `endpoint` deve ser capaz de receber os parâmetros para consulta por origem, destino, data da ida e data da volta.
- As rotas expostas, no webservice que você irá criar, devem estar protegidas por validação de token através de um cabeçalho na requisição.
- Fica a seu critério definir o formato do `json` de retorno da api, desde que seja fácil identificar o voo da ida e o voo da volta na combinação final.

#### Validações
- Não permitir buscas com aeroportos de `origem` e `destino` iguais.
- Os aeroportos precisam existir em nossa base de aeroportos do **Problema 1**.
- A `data de ida` precisar ser igual ou maior que a data atual.
- A `data de volta` precisar ser igual ou maior que a `data de ida`.

### Parte 1 - Informações adicionais de cada voo
Vamos preencher alguns valores e métricas para cada voo retornado:

1. **Price:**
- A taxa fee pode ser 10% do valor da tarifa (fare) ou R$ 40.0 (o que for maior).
- O total consiste na soma da tarifa (fare) com a taxa fee.

```
"price": {
  "fare": 819.12,
  "fee": 81.9,
  "total": 901.02
}
```

2. **Meta:**
- Calcule a distância linear em kms, entre os dois aeroportos utilizando a lat-lon informados e usando a Formula de Haversine.
- Utilize os horários de saída e chegada, e distância linear recém-encontrada entre os aeroportos, para calcular a velocidade aproximada de voo.
- Utilize a distância para obter o custo de tarifa (fare) por km voado.

```
"meta": {
  "range": 1000,
  "cruise_speed_kmh": 400,
  "cost_per_km": 0.7
}
```

### Parte 2 - Criando as combinações
O serviço desta companhia não permite combinar voos de ida e volta (duas datas) em uma única chamada. Com isto em mente, nosso serviço/api deve consumir a api desta companhia aérea duas vezes: uma consulta para a data de ida e outra consulta para a data de volta (com os aeroportos alternados).

- Precisamos de uma lista contendo todas as combinações de voos possiveis **ida X volta**, mantendo os dados já preenchidos anteriormente.
- A combinação deve ter um bloco `price` semelhante ao do voo unitário (mas neste caso com a soma de ambos os voos).
- A lista deve estar ordenada do mais barato para o mais caro.

### Critérios de aceitação
- Envie um link do Repositório com o código fonte do seu projeto (Github ou Bitbucket).
- Com o projeto rodando, devo ser capaz de consumir a nova API via CURL/Postman/Insomnia e ter todas as possibilidades de combinações disponíveis.

### Requisitos do Projeto
- Disponibilize um ambiente funcional em nuvem (Ex: Heroku) ou local usando Docker/Docker Compose.
- O projeto precisa ser escrito em Python (>=3.8) e/ou Go (>=1.18)
- Escolha o framework de sua preferência (ou não)
- Todo texto ou código precisa estar estritamente escrito em Inglês
- O repositório deve conter um Readme escrito em Inglês com a seguinte estrutura:
  - Descrição;
  - Instruções para instalação/setup e execução dos testes (caso existam);
  - Um breve resumo das tecnologias utilizadas;
  
### Recomendações
- Melhores práticas de código, limpo, semântico, fácil de ler
- Evitar ao máximo o uso de libs/pacotes de terceiros

> Diferencial (não obrigatório):
> Escreva testes
