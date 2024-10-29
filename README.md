Практическое задание 
Использолвал Java 17 , Maven , Spring , html , js.
CustomsDeclarationController.java

package com.example.declarations.controller;
```java 
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
