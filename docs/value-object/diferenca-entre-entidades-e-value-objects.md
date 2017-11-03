# Qual a diferença entre Value Object e Entidades?

> Este artigo é uma tradução desta publicação: http://culttt.com/2014/04/30/difference-entities-value-objects/

Como você está começando a mergulhar no mundo da programação, você está começando a descobrir uma série de novos conceitos e 
teorias. Uma dessas idéias, que não é tão clara, é o Value Object. Um value object é um conceito importante do Domain Driven 
Design (DDD). 

Para esta leitura, você não precisa se preocupar em dominar DDD (ou qualquer conceito relacionado a ele). Aqui, vamos falar 
unicamente sobre Value Objects.

> Nota: Estou considerando que você possui um bom entendimento sobre Programaço Orientada a Objeto (POO). Se não for o seu 
caso, provavelmente você irá ler sobre POO antes de continuar com esta leitura.

# Entidades versus Value Objects

Na Programação Orientada à Objetos, nós representados atributos e métodos relacionados como um Objeto. Por exemplo, 
uma **Pessoa** pode ser um Objeto em nossa aplicação. Uma Pessoa vai possuir um nome, um endereço de e-mail e uma senha, assim 
como outros atributos. Em nosso banco de dados, uma Pessoa poderia alterar o seu nome, e-mail ou senha, mas ela continuaria 
sendo a mesma Pessoa. Quando um objeto pode alterar alguns desses atributos (nome, e-mail, senha), mas permanece o mesmo 
objeto, nós o chamamos de **Entidade**. Uma Entidade é **mutável** porque ela pode alterar seus atributos sem alterar alterar
a identidade do objeto. Neste caso, a Entidade vai manter a identidade porque ela possui um `id` no banco de dados.

Confira um exemplo de uma classe que representa uma entidade:

```php
  class Person extends Model
  { 
    public $name;
    
    public $email;
    
    public $password;
    
    public function setName($name) { //... }
    
    public function setEmail($email) { //... }

    public function setPassword($password) { //... }
  }
```

Imagine que nossa aplicação permite que pessoas rastreiem sua localização atual. Quando a Pessoa está apta a se conectar 
com a internet e autenticar em nossa aplicação um novo objeto Localição é criado. O objeto Localização possui atributos para
armazenar a latitude e a longitude da Localização. Neste caso, Localização é Value Object porque nós não nos preocupamos com
uma instância específica da Localização, nós nos preocupamos somente com a Localização.

Veja o exemplo da classe Localização:

```php
 class Location
 {
  private $lat;
  private $long;
  
  public function __construct($lat, $long)
  {
    $this->lat = $lat;
    $this->long = $long;    
  }
  
  public function getLat()
  {
    return $this->lat;
  }
  
  public function getLong()
  {
    return $this->long;
  }
  
  /**
   * Um exemplo simples de checagem de igualdade.
   * Pode (e deve) ser mais bem elaborado nas utilizações em casos reais.
   * 
   * @return bool
   */ 
  public function equalsTo($other)
  {
    if($this->lat == $other->lat && $this->long == $other->long)
    {
      return true;
    }
    
    return false;
  }
  
  public function __toString()
  {
    return sprintf('Lat: %s; Long: %s', $this->lat, $this->long);
  }
 }
```

Quando uma Pessoa modifica a sua Localização, nós no temos que nos preocupar com atualiza o objeto Localização, 
nós simplesmente criamos um novo objeto Localização para a Pessoa. O objeto Localização nunca modifica seus atributos, eles 
serão os mesmos do momento da criação à destruição. Quando um objeto não pode ser modificado, nós o chamamos de imutável.

Uma outra distinção importante sobre Value Objects é que a igualdade não se baseia na identidade. Observe o método 
`Localization > equalsTo($other) : bool`. Quando você criar dois objetos Localização com a mesma latitude e longitude, estes 
objetos serão iguais um com o outro, de acordo com o critério de comparação que utilizamos acima.

Exemplo:

```php
$l1 = new Location(3.7319, 38.5267);
$l2 = new Location(3.7319, 38.5267);
$l2 = new Location(3.1000, 35.7652);

$l1->equalsTo($l2); // true
$l1->equalsTo($l3); // false
```

Agora, caso você faça o mesmo com Pessoa, você terá objetos diferentes, porque, embora você defina os mesmos atributos, 
o objeto possui um identidade diferente, tornando-os, assim, uma entidade.

Veja o exemplo:

```php
$p1 = new Person();
$p1->name = 'Mauricio Rodrigues';
$p1->email = 'pessoa@email.com';
$p1->password = '~super@secret!';
$p1->save(); // retorna true; $p1->id = 1;


$p2 = new Person();
$p2->name = 'Mauricio Rodrigues';
$p2->email = 'pessoa@email.com';
$p2->password = '~super@secret!';
$p2->save(); // retorna true; $p1->id = 2;

$p1 == $p2; // false
```

Não é porque duas pessoas tem o mesmo nome, que elas são as mesmas. 



