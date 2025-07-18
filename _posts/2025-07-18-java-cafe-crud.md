---
layout: post
title: "Java Mini CRUD Project: Café Menu Management"
date: 2025-07-18
categories: [Java, Projects, CRUD]
---

## ☕️ Java Café Menu Management Project

This post walks you through building a simple Java CLI (Command Line Interface) application to manage a café menu. It supports the basic CRUD operations: **Create, Read, Update, Delete**.

### 📌 Project Features

- Add a new menu item
- View all menu items
- Edit a menu item
- Delete a menu item
- Exit the program

---

### 🛠️ Tech Stack

- Language: **Java**
- Input: **Scanner**
- Data Structure: **ArrayList**

---

### 📁 File Structure

All the logic is written in a single Java file for simplicity. Later, you can refactor it into:

- `MenuItem.java` (for the model)
- `MenuService.java` (for business logic)
- `MenuApp.java` (for main loop)

---

### 💡 Sample Code (Monolithic Version)

```java
import java.util.*;

class MenuItem {
    String name, description, category;
    int price, calories;

    MenuItem(String name, String description, String category, int price, int calories) {
        this.name = name;
        this.description = description;
        this.category = category;
        this.price = price;
        this.calories = calories;
    }

    @Override
    public String toString() {
        return String.format(
            "[%s] %s | %s | %,d원 | %dkcal",
            category, name, description, price, calories
        );
    }
}

public class MenuApp {
    static List<MenuItem> menuList = new ArrayList<>();
    static Scanner sc = new Scanner(System.in);

    public static void main(String[] args) {
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

    static void addMenu() {
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

        menuList.add(new MenuItem(name, desc, category, price, cal));
        System.out.println(" > 메뉴가 추가되었습니다.");
    }

    static void listMenus() {
        if (menuList.isEmpty()) {
            System.out.println("등록된 메뉴가 없습니다.");
        } else {
            for (int i = 0; i < menuList.size(); i++) {
                System.out.println((i + 1) + ". " + menuList.get(i));
            }
        }
    }

    static void editMenu() {
        listMenus();
        System.out.print("수정할 메뉴 번호: ");
        int idx = Integer.parseInt(sc.nextLine()) - 1;
        if (idx >= 0 && idx < menuList.size()) {
            addMenu();  // Reuse addMenu to overwrite info
            menuList.remove(idx);
            System.out.println(" > 메뉴가 수정되었습니다.");
        } else {
            System.out.println("잘못된 번호입니다.");
        }
    }

    static void deleteMenu() {
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
