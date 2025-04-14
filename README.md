

### 1. **C’est quoi Spring Boot ?**
> Un framework Java qui permet de créer rapidement des API web robustes et scalables.

### 2. **C’est quoi CRUD ?**
> CRUD = **Create**, **Read**, **Update**, **Delete**
Les 4 opérations de base sur une base de données.

---

## ⚙️ FLUX COMPLET : CRUD avec Spring Boot

### 1. **Créer un projet Spring Boot**
Utilise [https://start.spring.io](https://start.spring.io/) avec :
- **Spring Web**
- **Spring Data JPA**
- **MySQL Driver**

---

### 2. **Configurer la base de données**
Dans `application.properties` :

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/nom_de_ta_base
spring.datasource.username=root
spring.datasource.password=ton_mot_de_passe
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

### 3. **Créer l’entité (Model)**

```java
import jakarta.persistence.*;

@Entity
public class Person {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    // Getters & setters
}
```

---

### 4. **Créer le repository**

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface PersonRepository extends JpaRepository<Person, Long> {
}
```

---

### 5. **Créer le contrôleur (API)**

```java
@RestController
@RequestMapping("/api/persons")
public class PersonController {

    private final PersonRepository personRepository;

    @Autowired
    public PersonController(PersonRepository personRepository) {
        this.personRepository = personRepository;
    }

    @GetMapping
    public List<Person> getAllPersons() {
        return personRepository.findAll();
    }

    @PostMapping
    public ResponseEntity<Person> createPerson(@RequestBody Person person) {
        return new ResponseEntity<>(personRepository.save(person), HttpStatus.CREATED);
    }

    @GetMapping("/{id}")
    public ResponseEntity<Person> getPerson(@PathVariable Long id) {
        return personRepository.findById(id)
            .map(person -> new ResponseEntity<>(person, HttpStatus.OK))
            .orElse(new ResponseEntity<>(HttpStatus.NOT_FOUND));
    }

    @PutMapping("/{id}")
    public ResponseEntity<Person> updatePerson(@PathVariable Long id, @RequestBody Person updatedPerson) {
        return personRepository.findById(id)
            .map(person -> {
                person.setName(updatedPerson.getName());
                person.setEmail(updatedPerson.getEmail());
                return new ResponseEntity<>(personRepository.save(person), HttpStatus.OK);
            })
            .orElse(new ResponseEntity<>(HttpStatus.NOT_FOUND));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deletePerson(@PathVariable Long id) {
        return personRepository.findById(id)
            .map(person -> {
                personRepository.delete(person);
                return new ResponseEntity<Void>(HttpStatus.OK);
            })
            .orElse(new ResponseEntity<>(HttpStatus.NOT_FOUND));
    }
}
```

