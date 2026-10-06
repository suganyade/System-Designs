# Sequence Diagrams

## Description

A **Sequence Diagram** is a **UML (Unified Modeling Language)** diagram that illustrates how different components of a system interact with each other over time to complete a specific operation. It shows the sequence of messages exchanged between users, services, databases, caches, and other components.

In the **Library Management System**, the major user flows include:

* User Login
* Search Book
* Borrow Book
* Return Book
* Reserve Book

---

# 1. User Login

## Sequence Diagram

```text
User
 │
 │ Enter Email & Password
 ▼
API Gateway
 │
 ▼
User Service
 │
 │ Validate Credentials
 ▼
SQL Database
 │
 │ User Found
 ▲
User Service
 │
 │ Generate JWT Token
 ▼
API Gateway
 │
 ▼
User
(Login Successful)
```

### Explanation

1. The user enters their email and password.
2. The request is sent to the API Gateway.
3. The API Gateway forwards the request to the User Service.
4. The User Service validates the credentials by checking the SQL database.
5. If the credentials are valid, a JWT token is generated.
6. The login success response is returned to the user.

---

# 2. Search Book

## Sequence Diagram

```text
User
 │
 │ Search Book
 ▼
API Gateway
 │
 ▼
Book Service
 │
 ▼
Redis Cache
 │
 ├── Cache Hit
 │      │
 │      ▼
 │  Return Book Details
 │
 └── Cache Miss
        │
        ▼
    SQL Database
        │
        ▼
 Store Book Details in Redis
        │
        ▼
 Return Book Details
```

### Explanation

1. The user searches for a book.
2. The request is forwarded to the Book Service.
3. The Book Service first checks Redis Cache.
4. If the data is available (Cache Hit), the book details are returned immediately.
5. If the data is not available (Cache Miss), the Book Service retrieves it from the SQL database.
6. The retrieved data is stored in Redis for future requests.
7. The book details are returned to the user.

---

# 3. Borrow Book

## Sequence Diagram

```text
User
 │
 │ Borrow Book
 ▼
API Gateway
 │
 ▼
Loan Service
 │
 ▼
Book Service
 │
 ▼
SQL Database
 │
 │ Check Book Availability
 │
 ├── Book Available
 │        │
 │        ▼
 │   Create Loan Record
 │        │
 │   Update Available Copies
 │        │
 │   Publish Event
 │        ▼
 │      Kafka
 │        │
 │        ▼
 │ Notification Service
 │        │
 │ Send Due Date Reminder
 │
 └── Book Not Available
          │
          ▼
     Suggest Reservation
```

### Explanation

1. The user selects a book to borrow.
2. The Loan Service requests the Book Service to verify availability.
3. The Book Service checks the SQL database.
4. If the book is available, a loan record is created.
5. The available book count is updated.
6. An event is published to Kafka.
7. The Notification Service sends a due date reminder.
8. If the book is unavailable, the user is prompted to reserve it.

---

# 4. Return Book

## Sequence Diagram

```text
User
 │
 │ Return Book
 ▼
API Gateway
 │
 ▼
Loan Service
 │
 ▼
SQL Database
 │
 │ Update Loan Status
 ▼
Book Service
 │
 │ Increase Available Copies
 ▼
SQL Database
 │
 ▼
Calculate Fine (If Overdue)
 │
 ▼
Notification Service
 │
 ▼
User
(Return Successful)
```

### Explanation

1. The user returns the borrowed book.
2. The Loan Service updates the loan record.
3. The Book Service increases the number of available copies.
4. If the return is overdue, the fine is calculated.
5. The Notification Service informs the user about the successful return and any applicable fine.

---

# 5. Reserve Book

## Sequence Diagram

```text
User
 │
 │ Reserve Book
 ▼
API Gateway
 │
 ▼
Reservation Service
 │
 ▼
SQL Database
 │
 │ Create Reservation
 ▼
Kafka
 │
 ▼
Notification Service
 │
 ▼
User
(Reservation Confirmed)
```

### Explanation

1. If the requested book is unavailable, the user places a reservation.
2. The Reservation Service stores the reservation details in the SQL database.
3. A reservation event is published to Kafka.
4. When the book becomes available, the Notification Service sends a notification to the user.

---

# Summary

The sequence diagrams illustrate the interactions between the user, API Gateway, application services, SQL database, Redis cache, Kafka, and the Notification Service. They help developers understand the order of operations for each feature and clearly document how requests flow through the Library Management System.
