class: center, middle
# Arquitetura do ePol Grafos
###Versão 1.0
<br>

Sistema de Grafos de Investigação do ePol

©2020 Dalton Serey, UFCG


---
class: purple2bar
# Objetivo e Status

Este documento se propõe a apresentar e explicar as principais
decisões arquiteturais que adotaremos na linha de pesquisa e
desenvolvimento do _Sistema de Grafos de Investigação do ePol_ (a
que chamaremos de ePol Grafos).


---
class: middle, center, greenbar2
# Subsistemas

---
class: greenbar2
## Os subsistemas do ePol Grafos

<img src="arquitetura-v0-subsistemas.jpg" alt="Arquitetura ePol v0"
width="70%" style="position: fixed; left: 0; right: 0; margin:
auto; bottom: 3em;"> Para uma primeira compreensão do ePol Grafos
é apropriado conhecer os subsistemas em que se divide e a forma
como interagem. São eles: 1) o subsistema de Mineração;
2) o subsistema de Navegação; e 3) o subsistema de Supervisão.

---
class: greenbar2
## 1. Mineração

O subsistema de .bred[Mineração] é responsável por reunir e
depurar os dados que compõem os grafos (essencialmente entidades
e relacionamentos) a partir de dados brutos disponíveis nas
diversas bases de dados da PF (documentos, casos, etc). Seu
comportamento é supervisionado e ajustado pelo subsistema de
Supervisão.

<figure>
<img src="arquitetura-v0-subsistemas.jpg" alt="Arquitetura ePol v0"
width="80%">
<figcaption>
Arquitetura do ePol Grafos: subsistemas
</figcaption>
</figure>



---
class: greenbar2
## 2. Navegação

O subsistema de .bblue[Navegação] é responsável por interagir com
o usuário permitindo-lhe que crie, explore, consulte e visualize
grafos de investigação customizados. Para isso, usa dados do BD de Grafos
e, caso necessário, demanda a produção e/ou atualização de novos
dados ao subsistema de Supervisão.

<figure>
<img src="arquitetura-v0-subsistemas.jpg" alt="Arquitetura ePol v0"
width="80%">
<figcaption>
Arquitetura do ePol Grafos: subsistemas
</figcaption>
</figure>



---
class: greenbar2
## 3. Supervisão

O subsistema de .bgreen[Supervisão] é responsável por: 1) receber
demandas de coleta e atualização de dados do subsistema de
Navegação e de operadores do sistema; 2) gerenciar o conjunto de
tarefas (_workload_) de coleta e atualização de dados,
alocando-as oportunamente para serem executadas pelo subsistema
de mineração; 3) supervisionar e ajustar a atuação do sistema de
Mineração.

<figure style="position: relative; bottom: 32px; mix-blend-mode: multiply;">
<img src="arquitetura-v0-subsistemas.jpg" alt="Arquitetura ePol v0"
width="80%">
<figcaption>
Arquitetura do ePol Grafos: subsistemas
</figcaption>
</figure>


---
class: graybar, middle, center
# A Arquitetura do ePol Grafos


---
class: graybar
## Arquitetura ePol Grafos: visão geral

<figure>
<img src="arquitetura-v0.jpg" alt="Arquitetura
ePol v0" width="100%">
<!--figcaption>
Arquitetura do ePol Grafos: componentes
</figcaption-->
</figure>

---
class: graybar
## Componentes do Subsistema de Mineração

<img src="arquitetura-v0-mineracao.jpg" alt="Arquitetura ePol v0"
width="70%" class="figdir">

1. Frontend
2. Backend
5. BD de Anotações
3. Engine de IA
6. BD dos Crawlers
4. Crawlers

---
class: graybar
## Componentes do Subsistema de Navegação

<img src="arquitetura-v0-navegacao.jpg" alt="Arquitetura ePol v0"
width="70%" class="figdir">

1. Frontend
2. Backend
3. BD de Grafos


---
class: graybar
## Componentes do Subsistema de Supervisão

<img src="arquitetura-v0-supervisao.jpg" alt="Arquitetura ePol v0"
width="70%" class="figdir">

1. Gerenciador de Crawlers
2. Gerenciador do Workload

---
class: center, middle, redbar
# Descrição dos<br>componentes da arquitetura


---
class: redbar
# Mineração: Frontend

.twocols[
1. .hl[Frontend]
2. Backend 
1. BD de Anotações
4. Engine de IA
6. Crawlers
2. BD dos Crawlers
]

<small>
App web (HTML + CSS + JS) para interação com o
usuário. Interage com o usuário e com o backend, acessando a
API REST. Preferencialmente, deve ser desenvolvida sem o uso
de frameworks e deve se basear em Componentes Web
reutilizáveis para que possamos eventualmente usá-los no
próprio ePol e/ou outros apps web que precisem permitir o
processo de anotações.

<small>
> <span style="font-weight: bold; font-style: italic;">Subsistema</span> **de Anotações** 
> O frontend, o backend e o BD de anotações
> podem ser vistos como um “Subsistema de Anotações”,
> cujo propósito é permitir que a IA colete anotações
> do usuário _expert_ e valide seus resultados.
</small>


</small>

---
class: redbar
# Mineração: Backend

.twocols[
1. Frontend
2. .hl[Backend]
1. BD de Anotações
4. Engine de IA
6. Crawlers
2. BD dos Crawlers
]

<small>
Servidor da lógica de backend do subsistema de anotações.
Implementa uma API REST JSON para interação com frontends
(inicialmente um app específico; no futuro, componentes web
rodando em qualquer outro app web). Deve manipular _chunks_
de texto contendo trechos anotados com identificação dos
possíveis tipos de entidades e relações de interesse. Deve
permitir a edição/adição das anotações, bem como a
confirmação ou negação da validade das entidades e relações
indicadas por cada anotação. Principal responsável pelo BD
de anotações, cujos dados são inicialmente criados pelo Engine de
IA.
</small>

---
class: redbar
# Mineração: BD de Anotações

.twocols[
1. Frontend
2. Backend
1. .hl[BD de Anotações]
3. Engine de IA
4. Crawlers
2. BD dos Crawlers
]

<small> 
É o repositório principal do sistema de anotações. Armazena
_chunks_ de texto com anotações que podem ter sido
identificadas manualmente pelo usuário, como forma de
entrada, ou pelo Engine de IA, como resultado de seu
treinamento e uso. Também deve conter potenciais entidades e
relacionamentos identificados, que também podem vir do
próprio usuário ou dos Crawlers, através do Engine de IA,
mas que não são necessariamente _chunks_ de texto. Todos os
dados devem incluir objetivas referências às fontes ou
origem dos dados. A ideia é que a qualquer momento seja
possível navegar de volta aos dados a partir dos quais os
crawlers, a IA ou o usuário que deram origem à informação.
Também pode ser necessário adicionar informação temporal aos
dados, já que algumas informações podem ser mutáveis.
</small>

---
class: redbar
# Mineração: Engine de IA

.twocols[
1. Frontend
2. Backend
1. BD de Anotações
3. .hl[Engine de IA]
6. Crawlers
2. BD dos Crawlers
]

<small>
Lógica de IA necessária para fazer o sistema aprender a identificar
entidades e relações automaticamente a partir das anotações
e confirmações dos usuários. Interage com os dados do
usuário *apenas* através dos dados registrados pelo Backend
no BD. Funciona de forma assíncrona em relação ao Backend,
ao Frontend e aos Crawlers. Inicialmente, pode ser um script
de linha de comando que pode ser executado sob demanda pelo
operador humano. Deve ser idempotente. No futuro, é provável
que precisemos de uma interface CLI para ele que interaja
com um pequeno servidor REST apenas para controle.
</small>

---
class: redbar
# Mineração: Crawlers

.twocols[
1. Frontend
2. Backend
1. BD de Anotações
3. Engine de IA
4. .hl[Crawlers]
2. BD dos Crawlers
]

<small>
Conjunto de pequenos scripts (e/ou daemons) de
coleta de dados primários. Alguns podem produzir _chunks_ de
texto pra posterior anotação; outros podem produzir
entidades e/ou relações e outros podem criar novas tarefas para
outros _crawlers_. Todos os dados devem ser
confirmadas e/ou avaliadas pela IA ou pelos usuários.  Cada
_crawler_ foca em um tipo específico de tarefa.  São
identificados e versionados para que seus dados sejam
_assinados_.  Exemplo inicial de crawler: lê textos
produzidos pelo processo que faz _pdf2text_ e que o divide
em chunks.
</small>

---
class: redbar
# Mineração: BD dos Crawlers

.twocols[
1. Frontend
2. Backend
3. Engine de IA
4. Crawlers
1. BD de Anotações
2. .hl[BD dos Crawlers]
]

<small> 
Trata-se de uma base de dados intermediária que registra os
dados brutos produzidos pelos _crawlers_. São _chunks_
brutos (sem anotações) de texto ou _entidades potenciais_
também brutas. Todos devem conter informação sobre o
_crawler_ que produziu o dado, bem como dos dados de origem
que foram usados para produzi-los. A ideia é poder navegar
de volta à origem em procedimentos de auditoria e/ou para
atividades de pesquisa e validação dos _crawlers_.
</small>

---
class: bluebar
# Navegação: Frontend

.twocols[
1. .hl[Frontend]
2. Backend
3. BD de Grafos
]

<small>
App web (HTML + CSS + JS) para interação com o usuário.
Permite criar, consultar e visualizar grafos de investigação
a partir dos dados disponíveis. Provavelmente, também deverá
permitir guiar o usuário pelos processos de produção dos
grafos, quando não houver dados suficientes. Isso significa
que, no futuro, deve produzir dados de controle para que o
Daemon provoque a criação dos dados, através do sistema de
anotações.
</small>

---
class: bluebar
# Navegação: Backend

.twocols[
1. Frontend
2. .hl[Backend]
3. BD de Grafos
]


<small> Backend API REST JSON para o sistema de grafos. Dá
apoio às operações do usuário de criação e manipulação de
grafos de investigação. Também precisamos definir se e como
deve dar acesso às operações de preparação de dados que
devem resultar em atuação do sistema de anotações, mais
especificamente dos _crawlers_, do Engine de IA,
bem como do backend e frontend do sistema de anotações para
interação com o usuário.
</small>

---
class: bluebar
# Navegação: BD de Grafos

.twocols[
1. Frontend
2. Backend
3. .hl[BD de Grafos]
]

<small> 
Este é o BD principal de armazenamento de grafos. Ele
armazena as entidades e relacionamentos de forma apropriada
para consultas, exibições e para manipulação. O Neo4J que já
mencionamos é o principal candidato aqui. Mas é provável que
precisemos ainda de mais meta-dados que talvez precisem de
outro formato... ou não, talvez o Neo4J seja suficiente. A
ver.
</small>

---
class: greenbar
# Supervisão: Gerenciador de Crawlers

.twocols[
1. .hl[Gerenciador de Crawlers]
1. Gerenciador de Workload
]

<small>
Daemon do ePol Grafos responsável pela inicialização
(_spawn_), eliminação (_kill_) e monitoramento dos _crawlers_. A lógica é
regulada por arquivos de configuração. Trata-se de módulo
autônomo e, portanto, sem requisitos complexos de interface com
usuário.

> Uma versão mínimalista pode permitir configuração apenas por
> arquivo de configuração. Em versões subsequentes, podemos
> incluir uma interface API REST para acesso através de um
> cliente de linha comando. Somente em estágio posterior será
> necessária uma interface web completa.
</small>

---
class: greenbar
# Supervisão: Gerenciador de Workload

.twocols[
1. Gerenciador de Crawlers
1. .hl[Gerenciador de Workload]
]

<small>
Daemon do ePol Grafos responsável pela gerência de tarefas e do
_workload_ (tarefas pendentes). Oferece uma API REST mínima para
o _backend_ do subsistema de Navegação e, eventualmente, para
operadores do sistema, via interface CLI. Também oferece
endpoints para liberação de tarefas para os _crawlers_.
Implementa a lógica de fila de
prioridades para decidir quais tarefas devem ser liberadas a cada
requisição.
</small>

<small>
> Uma primeira versão mínimalista pode permitir configuração apenas por
> arquivo de configuração. E também pode partir de uma heurística de fila simplista,
> tipo FIFO (ou até aleatória). A liberação de tarefas
> se dará através do mecanismo de _ leasing_.
</small>

---
class: purplebar, middle, center
# Principais Propriedades<br>Esperadas da Arquitetura

---
class: purplebar
# Modularidade

<small>
A forma como os componentes foram especificados e pelo qual se
comunicam permite desenvolvê-los e evoluí-los de forma
independente e versionada. Isso permite usar um modelo de
desenvolvimento iterativo
em que diferentes módulos podem ser abordados a cada
ciclo de forma que os demais módulos sejam minimamente afetados. Em
particular, estimamos que a arquitetura elimina
significativamente os
chamados _cross-cutting concerns_, de forma que cada módulo é
auto-contido e reúne responsabilidades únicas e bem definidas,
muito ao estilo preconizado pela conhecida regra da filosofia Unix
(_Make each program do one thing well_).
</small>


---
class: purplebar
# Escalabilidade (1/2)

<small>
Os componentes do sistema foram pensados para que atuem em tarefa
bastante que podem ser paralelizadas por serem independentes. Os
_crawlers_, por exemplo, uma vez iniciados, apenas produzem
novos registros, mas não alteram registros existentes. Isso garante
que não há acesso concorrente aos registros de dados no BD
dos Crawlers. Pelo mesmo motivo, esses mesmos registros podem ser
lidos e alterados pela IA com segurança depois que tenham sido
criados, dado que não podem ser alterados pelos _crawlers_. De
fato, se considerássemos apagar os dados do BD dos Crawlers
depois de serem “consumidos”, pode-se pensar nesse BD como um
INBOX para a comunicação Crawlers → IA.
</small>

---
class: purplebar
# Escalabilidade (2/2)

<small>
O próprio Engine de IA deve ser construído com escalabilidade
horizontal como requisito. Isso permitirá que quando houver
quantidades significativas de dados produzidos pelos crawlers, o
engine possa ser livremente replicado. Especial cuidado deve ser
tomado com a escrita de dados nos BD de Anotações e de Grafos
para que se evite situações de concorrência e contenção.

Os demais componentes (backends e frontends) também devem ser
projetados com escalabilidade horizontal em mente. A ideia é que
possam ser replicados livremente em situações de demanda, de
forma que se possa tirar proveito de data centers com suporte à
computação elástica (ou equivalentes).
</small>

---
class: purplebar
# Tolerância a faltas

<small>
Por ser baseado no mecanismo de _leasing_ (ver abaixo), o
daemon Gerenciador do Workload pode dar garantia de que toda
tarefa será _eventualmente_ cumprida ou será mantida
indefinidamente como pendente.
O mesmo mecanismo evita que mais de um _crawler_
atue na mesma tarefa simultaneamente (a menos que algum nível de
replicação ou redundância seja explicitamente implementado, caso
se mostre apropriado).
</small>

<!--
Como garantia adicional, o _crawler_ deve apresentar o ID do resultado
registrado no BD dos Crawlers como _prova_ de que concluiu a
tarefa. O _daemon_ pode usar esse ID para se assegurar que a
tarefa está devidamente concluída.
-->

<small>
> Como funciona o _leasing_? O _leasing_ é uma política de
> alocação de tarefas em que o _crawler_ requisita uma tarefa por
> vez e se compromete a concluí-la dentro de certo intervalo de
> tempo. O gerenciador marca a tarefa como _alocada_, mas não a
> apaga de seu BD até que o _crawler_ confirme a conclusão. Se o
> _crawler_ não confirmar a conclusão no tempo definido e não
> pedir a renovação do _leasing_, a tarefa é desmarcada como
> _alocada_ e volta a ser disponibilizada para novas requisições.
> Isso garante que se o _crawler_ sair do controle, perder sua
> conexão de rede ou simplesmente quebrar, a tarefa continuará
> pendente, aguardando ser executada.
</small>
