# Utilizando a Pagination Class (Classe de Paginação) do Codeigniter

19/09/2012

Summary
----
aginar conteúdo quando se tem uma quantidade de informações muito extensa é algo trivial para quem trabalha com websites de diversas grandezas. Pensando neste "inconveniente" a equipe do Codeigniter desenvolveu a Pagination Class (Classe de Paginação), a qual retira todo o árduo trabalho de programar diversas linhas de código para se conseguir uma navegação que seja realmente efetiva e de qualidade. Neste tutorial vamos demonstrar como utilizar a biblioteca de paginação. Primeiramente vamos fazer o download de uma instalação limpa do Codeigniter e, posteriormente, instalá-lo em nosso servidor...

Body
----

Paginar conteúdo quando se tem uma quantidade de informações muito extensa é algo trivial para quem trabalha com websites de diversas grandezas. Pensando neste "inconveniente", a equipe do Codeigniter desenvolveu a [Pagination Class](http://codeigniter.com/user_guide/libraries/pagination.html) (Classe de Paginação), a qual retira todo o árduo trabalho de programar diversas linhas de código para se conseguir uma navegação que seja realmente efetiva e de qualidade. Neste tutorial vamos demonstrar como utilizar a biblioteca de paginação.

Primeiramente faremos o download de uma instalação limpa do Codeigniter e, posteriormente, instalá-lo em nosso servidor. Lembrando que a versão utilizada é a 2.1.2, ou seja, a mais atual no momento em que este tutorial e redigido. Caso tenha dúvidas na instalação acesse por favor o link: [http://www.luizpicolo.com.br/2011/03/06/iniciando-com-framework-codeigniter-instalao/]() para maiores informações sobre o processo.

Agora vamos criar o banco de dados que será utilizado. Neste etapa creio que você já tenha criado o seu e consequentemente todas as suas tabelas, porém, deixarei aqui o link do SQL que utilizei neste tutorial. [Baixar o SQL](https://github.com/luizpicolo/exemplos)

Criaremos nosso controller com o nome de paginacao_controller e nosso primeiro método index que conterá as configurações de nosso paginação. Aproveitaremos também e criaremos nosso paginacao_model, que retornará os dados desejados do banco e nossa paginacao_view. (A nomeclatura está vinculada a escolha de cada um).

Outra opção que deve ser mudado é a inclusão das rotas

/application/config/router.php   
 
    // Configurações para a paginacao
    $route['paginacao'] = "paginacao_controller";
    $route['paginacao/(:num)'] = "paginacao_controller";
    $route['default_controller'] = "paginacao_controller";
    
/application/model/paginacao_model.php

````
class paginacao_model extends CI_Model
{
    // seleção dos dados
    var $select = "*";
    // Tabela de retorno dos dados
    var $table = "usuarios";
 
    public function __construct(){
        parent::__construct();
        // Configurações de Conexão
        $this->load->database();
    }
 
    // Método para retornar a quantidade de registro
    public function contaRegistros() {
        $this->db->select($this->select);
        $this->db->from($this->table);
        $query = $this->db->get();
        return count($query->result());
    }
 
    // Método que retornará os dados mediante os
    // parâmetros de inicio e quantidade
    public function retornaLista($maximo, $inicio) {
        $this->db->select($this->select);
        $this->db->from($this->table);
        $this->db->limit($maximo, $inicio);
        $query = $this->db->get();
        return $query->result();
    }
}
````
/application/controller/paginacao_controller.php

````
if (!defined('BASEPATH')) exit('No direct script access allowed');
 
class paginacao_controller extends CI_Controller
{
 
    public function index() {
        // Helper para URLS
        $this->load->helper("url");
 
        // Incluimos aqui o model de nosso paginação
        $this->load->model('paginacao_model', 'paginacao');
 
        // Definimos a quantiade de registro a
        // serem retornados
        $maximo = 3;
 
        // Verificaremos se a paginação esta sendo executada ou
        // se apenas a página foi iniciada
        // Caso ela tenha apenas os dados iniciais,
        // a paginação começará
        // no ponto 0, ou seja, limit(100, 0)
        if ($this->uri->segment(2) == "") {
            $inicio = 0;
        } else {
            $inicio = $this->uri->segment(2);
        }
 
        // Inicamos aqui as configuração para a paginação
        $this->load->library('pagination');
 
        // URL de nosso página
        $config['base_url'] = base_url() . "index.php/paginacao/";
 
        // Total de dados retornados (Quantidade Geral)
        $config['total_rows'] = $this->paginacao->contaRegistros();
 
        // Segmento que conterá o parâmetro de incremento
        // www.seusite.com.br/paginacao/5
        $config['uri_segment'] = 2;
 
        // Dados mostrados por página
        $config['per_page'] = $maximo;
 
        // Configuracoes de estilo da url
        $config['first_link'] = 'Primeiro';
        $config['cur_tag_open'] = '';
        $config['cur_tag_close'] = '';
        $config['last_link'] = 'Último';
        $config['next_link'] = 'Próximo';
        $config['prev_link'] = 'Anterior';
        $config['full_tag_open'] = '';
        $config['full_tag_close'] = '';
 
        // Iniciamos as configuracoes e criamos o link para o acesso
        $this->pagination->initialize($config);
        $data['paginacao'] = $this->pagination->create_links();
        // Retorno de dados mediante os parãmetros
        $data['dadosbusca'] = $this->paginacao->retornaLista($maximo, $inicio);
        // Chamamos nossa View e passaremos oa dados para a mesma
        $this->load->view('paginacao_view', $data);
    }
}
````

/application/view/paginacao_view.php

````
// Incluiremos este código em nosso View
foreach ($dadosbusca as $valor) {
     echo $valor-&gt;usuario;
     echo "";
}
echo $paginacao;
````

Basicamente é somente isto. Espero que tenha demonstrado com clareza a utilização desta biblioteca muito útil.

[Baixar arquivo de exemplo](https://github.com/luizpicolo/exemplos)

* CodeIgniter
* Frameworks
* PHP 