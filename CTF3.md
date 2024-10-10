# Wordpress CVE

Ao abrir a aplicação web e dirigindo ao código fonte, conseguimos facilmente descobrir a versão do Wordpress como a do WooCommerce. (imagemhtml)<br>
Posteriormente, explorando o website, conseguimos encontrar, na descrição de um artigo, as versões do Wordpress, MStore API, WooCommerce plugin e Booster for WooCommerce plugin. (imagem no website).
Decidimos então procurar CVE's para explorar vulnerabilidas de login, e ao pesquisar por "MStore API
3.9.0 login exploit", encontramos facilmente o CVE-2023-2732 que permite explorar esta vulnerabilidade até a versão 3.9.2. A falha acontece pois é possivel que utilizadores não autenticados ganhem acesso a qualquer conta do website, tal como admin, caso eles possuam o used id desse admin. <br>
Conseguimos obter a informação sobre como utilizar o exploit num repositorio github. (imagem do tutorial)
Utilizando o scrip python usando <br>
mstore-api.py  -u http://143.47.40.175:5001/ <br>
na consola conseguimos ter o output do url "http://143.47.40.175:5001/wp-json/wp/v2/add-listing?id=1", que aparenta, inicialmente, não fazer nada mas ao voltar a pagina inicial reparamos que já estamos em modo admin. (imagem site admin). Depois de procurar pelo website em modo admin conseguimos encontrar uma mensagem privada enviada pelo admin que contem a flag necessária (imagem final)<br>
Sendo a flag: flag{byebye}