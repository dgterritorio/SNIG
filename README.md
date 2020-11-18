# SNIG

Repositório do código aberto da infraestrutura de informação geográfica do Sistema Nacional de Informação Geográfica (SNIG)
https://snig.dgterritorio.gov.pt/

Este repositório é constituído pelo vários projetos que permitem adaptar o software [GeoNetwork](https://geonetwork-opensource.org/) às necessidades específicas do SNIG. Esta adaptação teve como base a versão 3.4.x do GeoNetwork, não se recomendando a sua utilização com outras versões. É constituído pelos seguintes componentes:

- **core-geonetwork**: Corresponde ao código base do GeoNetwork, com a introdução de funcionalidade adicionais para dar resposta aos requisitos específicos do SNIG, em particular ao nível do modo como são pesquisados e filtrados os registos de metadados do catálogo.

-  **snig-service**: Módulo que adiciona ao GeoNetwork a funcionalidade de limitar as pesquisas rápidas ao catálogo (autocomplete) a determinados tipos de registos de metadados (dataset ou series, por exemplo). O catálogo do SNIG tem como lógica de funcionamento a pesquisa apenas a Conjuntos de Dados Geográficos e Séries de Conjuntos de Dados Geográficos, sendo a informação relativa aos respectivos serviços geográficos (visualização e/ou descarregamento) disponibilizada através da ficha de metadados de cada recurso. Este comportamente tem como objetivo reduzir o número de registos existente no catálogo, promovendo assim uma pesquisa mais fácil e rápida da informação.

-  **iso19139.pt.mig.2.0**: Implementação do schema plugin do GeoNework para o Perfil Nacional de Metadados de Informação Geográfica (MIG). Tendo como base o schema iso19139, implementa as regras de validação específicas do Perfil MIG e adiciona novos campos à indexação da informação dos metadados, de forma a permitir suportar as pesquisas que são disponibilizadas através do Registo Nacional de Dados Geográficos.

-  **geonetwork-snig-view**:  Implementa uma interface customizada (*skin*) do GeoNetwork para utilização no âmbito do SNIG. Esta *skin* foi desenvolvida para ser utilizada no âmbito do portal do Registo Nacional de Dados Geográficos (https://snig.dgterritorio.gov.pt/rndg), pelo que as funcionalidades que implementa estão directamente relacionadas com os objectivos e requistos definidos para este sistema. Algumas das funcionalidades de pesquisa nesta *skin* exigem a utilização da versão adaptada do GeoNetwork para o SNIG (https://github.com/wktsi/core-geonetwork) e do template de metadados iso19139.pt.mig.2.0 (https://github.com/wktsi/iso19139.pt.mig.2.0).


## Instalação

### A partir do código fonte

A versão alterada do GeoNetwork (https://github.com/wktsi/core-geonetwork) integra no seu código os vários componentes que implementam as funcionalidades do SNIG. Exceptuando pequenas alterações ao código base, essa integração fez-se através da configuração desses componentes como submódulos do projeto principal, o que facilita a instalação e execução da aplicação. 

Uma das formas de executar a aplicação é através do servidor de web Jetty que vem integrado com GeoNetwork, bastando para isso realizar os seguintes passos:

* Obter o código fonte do projeto
```
git clone https://github.com/dgterritorio/SNIG.git
```

* Ir para a pasta do projeto
```
cd SNIG
```

* Inicializar e atualizar os submódulos
```
git submodule update --init --recursive
```

* Ir para a diretoria raiz do GeoNetwork
```
cd core-geonetwork
```

* Configurar ambiente para execução
```
export MAVEN_OPTS="-Xmx512M"
```

* Fazer o *build* do projeto
```
mvn clean install -DskipTests
```

* Executar
```
cd web
mvn jetty:run
```

* Abrir a aplicação no browser através do endereço http://localhost:8080/geonetwork

Para aceder ao catálogo através da vista (*skin*) customizada para o SNIG deverá adicionar-se o parâmetro *?view=snig* ao url de acesso à aplicação:
- http://localhost:8080/geonetwork/srv/por/catalog.search?**view=snig**#/home

### A partir do ficheiro WAR

O build do projeto dá origem à criação de um ficheiro WAR, o qual poderá ser utilizado para instalar a aplicação num servidor web de Java (Apache Tomcat, por exemplo). Esse ficheiro encontra-se em:

- SNIG/core-geonetwork/web/target/geonetwork.war

## Configuração

A versão adaptada do GeoNetwork possui um conjunto de configurações que permite a utilização de grande parte das funcionalidades desenvolvidas para o SNIG, no entanto ainda é necessário proceder a mais algumas definições para que essa utilização seja completa. Descrevemos em seguida essa configurações.

### Carregar o schema plugin do Perfil Nacional de Metadados de Informação Geográfica (iso19139.pt.mig.2.0)

O schema plugin do GeoNework para o Perfil Nacional de Metadados de Informação Geográfica (MIG). Tendo como base o schema iso19139, implementa as regras de validação específicas do Perfil MIG e adiciona novos campos à indexação da informação dos metadados, de forma a permitir suportar as pesquisas que são disponibilizadas através do Registo Nacional de Dados Geográficos.

Deverá utilizar-se a interface de administração do GeoNetwork para carregar o schema plugin do Perfil MIG no catálogo (ver instruções mais detalhada na [documentação](https://geonetwork-opensource.org/manuals/trunk/en/install-guide/loading-samples.html#signing-in-and-loading-templates) do GeoNetwork).

### Definir a *skin* do SNIG como a interface por defeito do catálogo

É possível definir a *skin* do SNIG como a interface por defeito do catálogo, eliminando-se assim a necessidade de se ter que utilizar o parâmetro `?view=snig` no url de acesso à aplicação. Essa configuração pode ser realizada através da administração do GeoNetwork, em 'Admin > Settings > User interface configuration > Choose the user interface to use', atribuindo-se o valor "snig".
