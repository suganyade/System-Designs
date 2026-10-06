
# Low-Level Design (LLD)

## Description

The Low-Level Design (LLD) describes the internal structure of the Library Management System. It includes the entities, their relationships, database schema, business logic, and APIs used within the application.

---

## 1. User Entity

### Attributes

User
-------------------------

userId : UUID
name : String
email : String
password : String
membershipStatus : String
createdAt : Timestamp

### Responsibilities

* Register/Login
* Search books
* Borrow books
* Return books
* Reserve books
* View borrowing history

> **Note:** The books a user borrows should not be stored directly in the **User** table. The relationship between users and books is maintained in the **Loan** table.

---

## 2. Book Entity

### Attributes

Book
-------------------------

bookId : UUID
title : String
isbn : String
author : String
category : String
totalCopies : Integer
availableCopies : Integer

### Responsibilities

* Store book information
* Track inventory
* Manage book availability

> **Note:** `userId` should not be stored in the **Book** table because multiple users can borrow the same book over time.

---

## 3. Loan Entity

### Attributes

```text
Loan
-------------------------
loanId : UUID
userId : UUID
bookId : UUID
borrowDate : Date
dueDate : Date
returnDate : Date
fineAmount : Decimal
status : Enum
```

### Responsibilities

* Track borrowed books
* Store borrow and return dates
* Calculate due dates
* Calculate overdue fines
* Maintain loan status

> **Note:** The **Loan** table acts as the bridge between the **User** and **Book** tables.

---

## 4. Reservation Entity

### Attributes

```text
Reservation
-------------------------
reservationId : UUID
userId : UUID
bookId : UUID
reservationDate : Date
status : Enum
```

### Responsibilities

* Reserve unavailable books
* Maintain the waiting list
* Notify users when the reserved book becomes available

---

## 5. Admin Entity

### Attributes

```text
Admin
-------------------------
adminId : UUID
name : String
email : String
role : String
```

### Responsibilities

* Add books
* Update books
* Delete books
* Manage users
* View reports

---

# Class Relationships

           User
             │
             │ 1:N
             ▼
           Loan
             ▲
             │ N:1
             │
            Book
             ▲
             │
        Reservation

Admin ─────────► User
Admin ─────────► Book


### Explanation

* One **User** can have multiple **Loan** records.
* Each **Loan** belongs to one **Book**.
* One **Book** can have multiple loan records over time.
* Users can reserve books that are currently unavailable.
* Admin manages both users and books.

---

# Database Relationships

### User → Loan

**Relationship:** One-to-Many (1:N)

One user can borrow multiple books over time.

User (1)
   │
   │
Loan (Many)


---

### Book → Loan

**Relationship:** One-to-Many (1:N)

One book can appear in many loan records over its lifetime.

---

### User → Reservation

**Relationship:** One-to-Many (1:N)

One user can create multiple reservations.

---

### Book → Reservation

**Relationship:** One-to-Many (1:N)

One book can have multiple reservations from different users.

---

# Entity Relationship (ER) Overview

```text
          User
            │
        1   │   N
            ▼
          Loan
            ▲
        N   │   1
            │
          Book
            ▲
            │
       Reservation

Admin ─────────► User
Admin ─────────► Book
```

---

# Overall Design Summary

* The **User** entity stores user information.
* The **Book** entity stores book details and inventory.
* The **Loan** entity connects users and books and tracks borrowing activities.
* The **Reservation** entity manages waiting lists for unavailable books.
* The **Admin** entity manages users, books, and system operations.
* Relationships are designed using **One-to-Many (1:N)** associations to maintain normalization and data consistency.
