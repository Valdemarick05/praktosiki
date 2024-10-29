Практическое задание 
Использолвал Java 17 , Maven , Spring , html , js.
__CustomsDeclarationController.java__ - контроллер, обрабатывающий HTTP-запросы для работы с декларациями.
```java
package com.example.declarations.controller; 
import com.example.declarations.model.CustomsDeclaration;
import com.example.declarations.service.CustomsDeclarationService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.NoSuchElementException;

@CrossOrigin(origins = "http://localhost:63342")
@RestController
@RequestMapping("/api/declarations")
public class CustomsDeclarationController {

    @Autowired
    private CustomsDeclarationService service;

    // Создание новой декларации
    @PostMapping
    public ResponseEntity<CustomsDeclaration> createDeclaration(@RequestBody CustomsDeclaration declaration) {
        try {
            CustomsDeclaration created = service.createDeclaration(declaration);
            return ResponseEntity.status(HttpStatus.CREATED).body(created);
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
        }
    }

    // Получение всех деклараций
    @GetMapping
    public ResponseEntity<List<CustomsDeclaration>> getAllDeclarations() {
        try {
            List<CustomsDeclaration> declarations = service.getAllDeclarations();
            return ResponseEntity.ok(declarations);
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
        }
    }

    // Получение декларации по ID
    @GetMapping("/{id}")
    public ResponseEntity<CustomsDeclaration> getDeclarationById(@PathVariable Long id) {
        try {
            return service.getDeclarationById(id)
                    .map(declaration -> ResponseEntity.ok(declaration))
                    .orElse(ResponseEntity.notFound().build());
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
        }
    }

    // Поиск декларации по номеру
    @GetMapping("/search")
    public ResponseEntity<CustomsDeclaration> getDeclarationByNumber(@RequestParam String number) {
        try {
            CustomsDeclaration declaration = service.getDeclarationByNumber(number);
            return declaration != null
                    ? ResponseEntity.ok(declaration)
                    : ResponseEntity.notFound().build();
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
        }
    }

    // Фильтрация деклараций по статусу
    @GetMapping("/filter")
    public ResponseEntity<List<CustomsDeclaration>> getDeclarationsByStatus(@RequestParam String status) {
        try {
            List<CustomsDeclaration> declarations = service.getDeclarationsByStatus(status);
            return ResponseEntity.ok(declarations);
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
        }
    }

    // Обновление декларации
    @PutMapping("/{id}")
    public ResponseEntity<CustomsDeclaration> updateDeclaration(@PathVariable Long id, @RequestBody CustomsDeclaration declaration) {
        try {
            CustomsDeclaration updated = service.updateDeclaration(id, declaration);
            return updated != null
                    ? ResponseEntity.ok(updated)
                    : ResponseEntity.notFound().build();
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
        }
    }

    // Удаление декларации
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteDeclaration(@PathVariable Long id) {
        try {
            service.deleteDeclaration(id);
            return ResponseEntity.noContent().build();
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
        }
    }
}
```
__CustomsDeclarationService.java__ — сервис, который реализует бизнес-логику для деклараций.
```java
package com.example.declarations.service;

import com.example.declarations.model.CustomsDeclaration;
import com.example.declarations.repository.CustomsDeclarationRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class CustomsDeclarationService {

    @Autowired
    private CustomsDeclarationRepository repository;

    // Создание новой декларации
    public CustomsDeclaration createDeclaration(CustomsDeclaration declaration) {
        return repository.save(declaration);
    }

    // Получение всех деклараций
    public List<CustomsDeclaration> getAllDeclarations() {
        return repository.findAll();
    }

    // Получение декларации по ID
    public Optional<CustomsDeclaration> getDeclarationById(Long id) {
        return repository.findById(id);
    }

    // Поиск декларации по номеру
    public CustomsDeclaration getDeclarationByNumber(String number) {
        return repository.findByDeclarationNumber(number);
    }

    // Фильтрация деклараций по статусу
    public List<CustomsDeclaration> getDeclarationsByStatus(String status) {
        return repository.findByStatus(status);
    }

    // Обновление декларации
    public CustomsDeclaration updateDeclaration(Long id, CustomsDeclaration newDeclaration) {
        return repository.findById(id)
                .map(declaration -> {
                    declaration.setDeclarationNumber(newDeclaration.getDeclarationNumber());
                    declaration.setStatus(newDeclaration.getStatus());
                    declaration.setDescription(newDeclaration.getDescription());
                    return repository.save(declaration);
                })
                .orElse(null);
    }

    // Удаление декларации
    public void deleteDeclaration(Long id) {
        repository.deleteById(id);
    }
}
```
__CustomsDeclaration.java__  — модель, представляющая декларацию.
```java
package com.example.declarations.model;

import jakarta.persistence.*;

@Entity
public class CustomsDeclaration {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true)
    private String declarationNumber;

    @Column(nullable = false)
    private String status;  // "на проверке", "одобрена", "отклонена"

    private String description;

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDeclarationNumber() {
        return declarationNumber;
    }

    public void setDeclarationNumber(String declarationNumber) {
        this.declarationNumber = declarationNumber;
    }

    public String getStatus() {
        return status;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
}
```

__CustomsDeclarationRepository.java__ — репозиторий для доступа к базе данных.
```java
package com.example.declarations.repository;

import com.example.declarations.model.CustomsDeclaration;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface CustomsDeclarationRepository extends JpaRepository<CustomsDeclaration, Long> {
    List<CustomsDeclaration> findByStatus(String status);
    CustomsDeclaration findByDeclarationNumber(String declarationNumber);
}

```
__CustomsDeclarationApplication.java__ — основной файл запуска Spring Boot приложения.
```java
package com.example.declarations;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class CustomsDeclarationApplication {

    public static void main(String[] args) {
        SpringApplication.run(CustomsDeclarationApplication.class, args);
    }
}

```
