
# API REST - Spring Boot

Este é a forma que eu criei essa API REST usando o framework Spring Boot. A API será desenvolvida utilizando Java e uma base de dados, como PostgreSQL ou MySQL. A seguir estão os passos necessários para criar a API:



## Etapas

1. Spring Initializr
Inicie um novo projeto Spring usando o Spring Initializr. Selecione as dependências necessárias, como Spring Web, Spring Data JPA e PostgreSQL Driver, de acordo com as suas necessidades.

2. Configuração do Banco de Dados
Crie um banco de dados no PostgreSQL ou MySQL, e configure as credenciais de acesso no arquivo application.properties do projeto Spring Boot. Certifique-se de que o driver JDBC correto está sendo utilizado e forneça as informações de conexão necessárias.

3. Criação da Entidade
Crie uma classe Java para representar a entidade principal da sua API. Por exemplo, se você estiver construindo uma API de produtos, crie uma classe Product com os atributos desejados.

4. Mapeamento da Entidade
Utilize as anotações do Spring, como @Entity, @Table, @Id e @GeneratedValue, para mapear a entidade com a tabela correspondente no banco de dados. Por exemplo:

```java
@Entity
@Table(name = "products")
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    private double price;
    
    // Getters e Setters
}
```
5. Criação do Repository
Crie uma interface ProductRepository que estenda a interface JpaRepository do Spring Data JPA. Essa interface será responsável por fornecer os métodos para acessar os dados da entidade no banco de dados.

```java

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
}
```
6. Criação dos Controladores
Crie classes de controladores para manipular as requisições HTTP da API. Anote essas classes com @RestController e injete o repositório necessário utilizando a anotação @Autowired.

```java

@RestController
@RequestMapping("/api/products")
public class ProductController {
    private final ProductRepository productRepository;
    
    @Autowired
    public ProductController(ProductRepository productRepository) {
        this.productRepository = productRepository;
    }
    
    // Implemente os métodos HTTP (GET, POST, PUT, DELETE) para manipular os produtos
}
```
7. Implementação dos Endpoints
Dentro dos controladores, implemente os métodos HTTP (GET, POST, PUT, DELETE) para manipular os produtos. Utilize as anotações @GetMapping, @PostMapping, @PutMapping e @DeleteMapping para mapear os endpoints corretamente.

8. Implementando HATEOAS
O HATEOAS (Hypermedia as the Engine of Application State) é um princípio fundamental da arquitetura REST que permite aos clientes navegar e interagir com os recursos de uma API por meio de links hipermídia.

Para implementar o HATEOAS em nossa API, siga as etapas abaixo:

Certifique-se de que a dependência HATEOAS esteja incluída no arquivo pom.xml do projeto Maven. Adicione a seguinte dependência:

```xml

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-hateoas</artifactId>
</dependency>
```
Em sua entidade ou modelo (por exemplo, ProductModel), estenda a classe RepresentationModel. Isso permite que sua entidade tenha suporte a links HATEOAS. Exemplo:

```java
import org.springframework.hateoas.RepresentationModel;

public class ProductModel extends RepresentationModel<ProductModel> {
    // Seus atributos e métodos existentes
}
```
Localize o método getAllProducts no seu controlador (por exemplo, ProductController) e faça as seguintes alterações:

```java
Copy code
import org.springframework.hateoas.server.mvc.WebMvcLinkBuilder;

@GetMapping("/products")
public ResponseEntity<List<ProductModel>> getAllProducts() {
    List<ProductModel> productsList = productRepository.findAll();
    if (!productsList.isEmpty()) {
        for (ProductModel product : productsList) {
            UUID id = product.getIdProduct();
            product.add(WebMvcLinkBuilder.linkTo(methodOn(ProductController.class).getOneProduct(id)).withSelfRel());
        }
    }
    return ResponseEntity.status(HttpStatus.OK).body(productsList);
}
```
Nesse exemplo, a lista de produtos é obtida do repositório. Em seguida, verificamos se a lista não está vazia. Para cada produto na lista, é extraído o ID e adicionado um link HATEOAS para o método getOneProduct do ProductController. O método getOneProduct deve ser implementado para retornar um produto específico com base no ID.

Certifique-se de importar corretamente as classes necessárias para o HATEOAS, como org.springframework.hateoas.RepresentationModel e org.springframework.hateoas.server.mvc.WebMvcLinkBuilder.

Com essas implementações, sua API agora suporta links HATEOAS, permitindo que os clientes naveguem e interajam com os recursos por meio dos links retornados nas respostas. Isso torna a API mais autoexplicativa e facilita a descoberta de funcionalidades adicionais pelos clientes.

9. Teste da API
Após implementar os endpoints, inicie a aplicação e teste a API utilizando ferramentas como o Postman ou o cURL. Certifique-se de que os endpoints estão respondendo corretamente e manipulando os dados no banco de dados.

Este é um guia básico para criar uma API REST com o Spring Boot. Lembre-se de adaptar o código às necessidades do seu projeto, adicionando validações, tratamento de erros e outras funcionalidades desejadas.
## Licença

Este repositório é licenciado sob a licença MIT. Você é livre para usar, modificar e distribuir o código contido neste repositório para qualquer finalidade, desde que a atribuição correta seja fornecida. Consulte o arquivo [LICENSE](https://choosealicense.com/licenses/mit/) para obter mais informações.

