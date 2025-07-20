---
layout: post
title: "Java Cafe CRUD Project: Spring Boot REST API Edition"
date: 2025-07-19
categories: [Java, Spring, CRUD]
tags: [SpringBoot, REST, JPA]
---

## ☕ Java Café Menu CRUD with Spring Boot

This post builds on the previous **CLI-based menu manager** and introduces a clean, modern implementation using **Spring Boot**. We’ll create a full-featured REST API to manage a café’s menu system.

➡️ _Missed the CLI version? [Read Part 1 here](./2025-07-18-java-mini-crud-project-cafe-menu-management.html)_

---

### 📌 Project Features

- REST API endpoints for menu items
- Create, Read, Update, Delete operations
- DTOs and service separation
- Spring Data JPA integration
- JSON-based communication

---

### 🛠️ Tech Stack

- Java 17
- Spring Boot
- Spring Data JPA
- H2 (or MySQL)
- Maven
- IntelliJ IDEA

---

### 📁 Project Structure

```

src/main/java/com/example/menu/
├── controller/
├── dto/
├── entity/
├── repository/
├── service/

```

---

### 🧱 Key Code Snippets

#### 🔹 MenuItem Entity

```java
@Entity
@Getter @Setter @NoArgsConstructor
public class MenuItem {
    @Id @GeneratedValue
    private Long id;
    private String name;
    private int price;
    private String category;
}
```

#### 🔹 DTO Example

```java
@Getter @Setter
public class MenuRequestDto {
    private String name;
    private int price;
    private String category;

    public MenuItem toEntity() {
        return new MenuItem(null, name, price, category);
    }
}
```

#### 🔹 Service Layer

```java
@Service
@RequiredArgsConstructor
public class MenuService {
    private final MenuRepository repository;

    public Long save(MenuRequestDto dto) {
        return repository.save(dto.toEntity()).getId();
    }

    public List<MenuResponseDto> findAll() {
        return repository.findAll()
                         .stream()
                         .map(MenuResponseDto::new)
                         .toList();
    }
}
```

#### 🔹 REST Controller

```java
@RestController
@RequestMapping("/api/menus")
@RequiredArgsConstructor
public class MenuController {
    private final MenuService service;

    @PostMapping
    public Long save(@RequestBody MenuRequestDto dto) {
        return service.save(dto);
    }

    @GetMapping
    public List<MenuResponseDto> findAll() {
        return service.findAll();
    }
}
```

---

### 🔁 Sample API Usage

| Method   | Endpoint          | Description        |
| -------- | ----------------- | ------------------ |
| `GET`    | `/api/menus`      | Get all menu items |
| `POST`   | `/api/menus`      | Add new item       |
| `PUT`    | `/api/menus/{id}` | Update item        |
| `DELETE` | `/api/menus/{id}` | Delete item        |

---

### 🔍 Test the API

You can test the API with tools like:

- Postman
- Curl
- Browser (GET only)

---
