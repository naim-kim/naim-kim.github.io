---
layout: post
title: "Java Cafe CRUD Project: CLI Console Version"
date: 2025-07-18
categories: [Java, Projects, CRUD]
tags: [Java, CLI, OOP]
---

## ☕ Java Café Menu CRUD – CLI Edition (Part 1 of 3)

This is the **first post** in my Café CRUD project series — where I walk through building a CRUD app in **3 stages**:

1. Java CLI console version (this post)
2. Spring Boot REST API ([Part 2 here](./2025-07-19-java-cafe-menu-spring-boot.html))
3. Spring Legacy JSP app ([Part 3 here](./2025-07-20-java-cafe-crud-spring-legacy.html))

In this version, we manage a menu system using simple console input. The goal: keep it clean, modular, and readable using OOP principles.

---

### 📌 Project Features

- Create a new menu item
- View all items
- Update an existing item
- Delete a menu item
- Loop until user exits

---

### 🛠️ Tech Stack

- Java 17
- Console input with `Scanner`
- `ArrayList` for dynamic data
- Clean OOP separation: `Main`, `Menu`, `MenuCRUD`

---

### 📁 Folder Structure

```

src/
├── Main.java         // Entry point
├── Menu.java         // Model class
└── MenuCRUD.java     // Service for handling menu logic

```

---

### 🧱 Code Highlights

#### 🔹 Menu.java (Model)

```java
public class Menu {
    private String name;
    private String description;
    private String category;
    private int price;
    private int calories;

    public Menu(String name, String description, String category, int price, int calories) {
        this.name = name;
        this.description = description;
        this.category = category;
        this.price = price;
        this.calories = calories;
    }

    @Override
    public String toString() {
        return String.format("[%s] %s | %s | %,d원 | %dkcal", category, name, description, price, calories);
    }

    // Getters/Setters omitted for brevity
}
```

---

#### 🔹 MenuCRUD.java (Service)

```java
import java.util.*;

public class MenuCRUD {
    private final List<Menu> menuList = new ArrayList<>();
    private final Scanner sc = new Scanner(System.in);

    public void run() {
        while (true) {
            System.out.println("\n카페 메뉴 관리 > 1. 추가  2. 수정  3. 목록  4. 삭제  0. 종료");
            int choice = Integer.parseInt(sc.nextLine());
            switch (choice) {
                case 1 -> addMenu();
                case 2 -> editMenu();
                case 3 -> listMenus();
                case 4 -> deleteMenu();
                case 0 -> {
                    System.out.println("프로그램 종료.");
                    return;
                }
                default -> System.out.println("잘못된 입력입니다.");
            }
        }
    }

    private void addMenu() {
        System.out.print("메뉴 이름: ");
        String name = sc.nextLine();
        System.out.print("설명: ");
        String desc = sc.nextLine();
        System.out.print("카테고리 (예: 커피, 디저트 등): ");
        String category = sc.nextLine();
        System.out.print("가격 (원): ");
        int price = Integer.parseInt(sc.nextLine());
        System.out.print("칼로리 (kcal): ");
        int cal = Integer.parseInt(sc.nextLine());

        menuList.add(new Menu(name, desc, category, price, cal));
        System.out.println(" > 메뉴가 추가되었습니다.");
    }

    private void listMenus() {
        if (menuList.isEmpty()) {
            System.out.println("등록된 메뉴가 없습니다.");
        } else {
            for (int i = 0; i < menuList.size(); i++) {
                System.out.println((i + 1) + ". " + menuList.get(i));
            }
        }
    }

    private void editMenu() {
        listMenus();
        System.out.print("수정할 메뉴 번호: ");
        int idx = Integer.parseInt(sc.nextLine()) - 1;
        if (idx >= 0 && idx < menuList.size()) {
            addMenu();  // Reuse add logic
            menuList.remove(idx);
            System.out.println(" > 메뉴가 수정되었습니다.");
        } else {
            System.out.println("잘못된 번호입니다.");
        }
    }

    private void deleteMenu() {
        listMenus();
        System.out.print("삭제할 메뉴 번호: ");
        int idx = Integer.parseInt(sc.nextLine()) - 1;
        if (idx >= 0 && idx < menuList.size()) {
            menuList.remove(idx);
            System.out.println(" > 메뉴가 삭제되었습니다.");
        } else {
            System.out.println("잘못된 번호입니다.");
        }
    }
}
```

---

#### 🔹 Main.java (Entry Point)

```java
public class Main {
    public static void main(String[] args) {
        new MenuCRUD().run();
    }
}
```

---

### ✅ Summary

This CLI version of the Café Menu project gave me a solid foundation for organizing CRUD logic. With clear class separation and clean console interaction, it was a great exercise before moving into Spring-based development.

➡️ [Continue to Part 2 – Spring Boot REST API](./2025-07-20-java-cafe-menu-spring-boot.html)

---
