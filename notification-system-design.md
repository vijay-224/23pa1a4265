# Notification System Design
# 📢 Notification System Design

A RESTful Notification Management System for a campus platform that delivers **Placement**, **Event**, and **Result** notifications to students.

The system provides REST APIs for notification management and uses **Socket.IO (WebSockets)** for real-time notification delivery.

---

# 📑 Table of Contents

* Overview
* Notification Model
* Common Headers
* REST API Endpoints

  * Get All Notifications
  * Get Notification by ID
  * Create Notification
  * Mark Notification as Read
  * Mark All Notifications as Read
  * Delete Notification
* HTTP Status Codes
* Error Response
* Real-Time Notification Flow
* API Summary
* Naming Conventions

---

# 📌 Overview

This document defines the REST API contract for a campus notification platform.

The APIs follow RESTful principles with:

* Predictable resource naming
* JSON request and response formats
* Standard HTTP status codes
* Real-time communication using Socket.IO

---

# 📦 Notification Model

```json
{
  "id": "uuid",
  "type": "Placement",
  "title": "Placement Drive",
  "message": "TCS Corporation is hiring.",
  "isRead": false,
  "createdAt": "2026-04-22T17:51:18Z"
}
```

### Field Description

| Field     | Type               | Description                    |
| --------- | ------------------ | ------------------------------ |
| id        | UUID               | Unique notification identifier |
| type      | String             | Placement / Event / Result     |
| title     | String             | Notification title             |
| message   | String             | Notification content           |
| isRead    | Boolean            | Read status                    |
| createdAt | ISO-8601 Timestamp | Notification creation time     |

---

# 📋 Common Headers

## Request Headers

```http
Content-Type: application/json
Accept: application/json
```

## Response Headers

```http
Content-Type: application/json
```

---

# 🚀 REST API Endpoints

## 1️⃣ Get All Notifications

Retrieve notifications for a student.

### Endpoint

```http
GET /api/notifications
```

### Query Parameters

| Parameter | Type   | Required | Description                                          |
| --------- | ------ | -------- | ---------------------------------------------------- |
| page      | Number | No       | Current page number                                  |
| limit     | Number | No       | Number of records per page                           |
| type      | String | No       | Filter by notification type (Placement/Event/Result) |

### Example Request

```http
GET /api/notifications?page=1&limit=10&type=Placement
```

### Success Response (200)

```json
{
  "success": true,
  "page": 1,
  "limit": 10,
  "total": 250,
  "notifications": [
    {
      "id": "1",
      "type": "Placement",
      "title": "Placement Drive",
      "message": "Amazon hiring",
      "isRead": false,
      "createdAt": "2026-04-22T17:51:18Z"
    }
  ]
}
```

---

## 2️⃣ Get Notification by ID

Retrieve a single notification.

### Endpoint

```http
GET /api/notifications/{id}
```

### Example Request

```http
GET /api/notifications/123
```

### Success Response (200)

```json
{
  "success": true,
  "notification": {
    "id": "123",
    "type": "Result",
    "title": "Semester Result",
    "message": "Results published",
    "isRead": false,
    "createdAt": "2026-04-22T17:51:18Z"
  }
}
```

---

## 3️⃣ Create Notification

Create a new notification.

### Endpoint

```http
POST /api/notifications
```

### Request Body

```json
{
  "type": "Placement",
  "title": "Placement Drive",
  "message": "Microsoft is hiring."
}
```

### Success Response (201)

```json
{
  "success": true,
  "message": "Notification created successfully",
  "notificationId": "12345"
}
```

---

## 4️⃣ Mark Notification as Read

Mark a specific notification as read.

### Endpoint

```http
PATCH /api/notifications/{id}/read
```

### Success Response (200)

```json
{
  "success": true,
  "message": "Notification marked as read"
}
```

---

## 5️⃣ Mark All Notifications as Read

Mark every notification as read.

### Endpoint

```http
PATCH /api/notifications/read-all
```

### Success Response (200)

```json
{
  "success": true,
  "message": "All notifications marked as read"
}
```

---

## 6️⃣ Delete Notification

Delete a notification.

### Endpoint

```http
DELETE /api/notifications/{id}
```

### Success Response (200)

```json
{
  "success": true,
  "message": "Notification deleted"
}
```

---

# 📊 HTTP Status Codes

| Status Code | Description                   |
| ----------- | ----------------------------- |
| 200         | Request successful            |
| 201         | Resource created successfully |
| 400         | Bad request                   |
| 404         | Notification not found        |
| 500         | Internal server error         |

---

# ❌ Error Response

```json
{
  "success": false,
  "message": "Notification not found"
}
```

---

# ⚡ Real-Time Notification System

## Technology

* Socket.IO
* WebSockets

## Workflow

```text
Student Login
      │
      ▼
WebSocket Connection Established
      │
      ▼
Backend Stores Active Socket
      │
      ▼
Admin Creates Notification
      │
      ▼
Notification Saved to Database
      │
      ▼
Socket.IO Emits Event
      │
      ▼
Student Receives Notification Instantly
```

---

## Event Name

```text
new-notification
```

## Event Payload

```json
{
  "id": "123",
  "type": "Placement",
  "title": "Placement Drive",
  "message": "Amazon hiring",
  "createdAt": "2026-04-22T17:51:18Z"
}
```

---

# 📌 REST API Summary

| Method | Endpoint                       | Description                    |
| ------ | ------------------------------ | ------------------------------ |
| GET    | `/api/notifications`           | Retrieve all notifications     |
| GET    | `/api/notifications/{id}`      | Retrieve notification by ID    |
| POST   | `/api/notifications`           | Create a notification          |
| PATCH  | `/api/notifications/{id}/read` | Mark a notification as read    |
| PATCH  | `/api/notifications/read-all`  | Mark all notifications as read |
| DELETE | `/api/notifications/{id}`      | Delete a notification          |

---

# 🏗️ Naming Conventions

* Use **plural nouns** for resources.
* Follow **RESTful HTTP methods**.
* Exchange data using **JSON**.
* Use **UUIDs** as notification identifiers.
* Store timestamps in **ISO-8601** format.
* Return consistent response structures for success and error cases.

---

# 📖 Technology Stack

| Layer                   | Technology |
| ----------------------- | ---------- |
| API Architecture        | REST       |
| Data Format             | JSON       |
| Real-Time Communication | Socket.IO  |
| Protocol                | WebSockets |
| Identifier              | UUID       |
| Timestamp Format        | ISO-8601   |

---

## 📄 License

This API specification is intended for educational and development purposes. Feel free to extend it according to your application's requirements.

# 🗄️ Stage 2 – Database Design

This document describes the database architecture for the **Campus Notification System**, including the database selection, data model, indexing strategy, optimization techniques, and common MongoDB operations.

---

# 📑 Table of Contents

* Database Choice
* Why MongoDB?
* Database Collections

  * Students Collection
  * Notifications Collection
* Entity Relationship
* Indexing Strategy
* Performance Challenges
* Optimization Techniques
* MongoDB Queries

  * Get Notifications
  * Get Notification by ID
  * Create Notification
  * Mark Notification as Read
  * Mark All Notifications as Read
  * Delete Notification
* Summary

---

# 🗃️ Database Choice

The notification system uses **MongoDB** as the primary persistent database.

MongoDB is a NoSQL, document-oriented database that stores data in flexible JSON-like documents, making it well suited for notification-based applications.

---

# ✅ Why MongoDB?

MongoDB provides several advantages for a notification management system:

* 📄 Stores notifications as JSON-like documents.
* ⚡ High write performance for handling frequent notification creation.
* 📈 Supports horizontal scaling through **sharding**.
* 🔄 Flexible schema allows adding new fields without database migrations.
* 🟢 Integrates seamlessly with **Node.js** using **Mongoose**.
* 🚀 Efficient querying through indexing.

---

# 📂 Database Collections

## 👨‍🎓 Students Collection

Stores student information.

```json
{
  "_id": ObjectId,
  "name": "Santhosh",
  "email": "santhosh@example.com",
  "createdAt": ISODate("2026-04-22T17:51:18Z")
}
```

### Fields

| Field     | Type     | Description                |
| --------- | -------- | -------------------------- |
| _id       | ObjectId | Unique student identifier  |
| name      | String   | Student name               |
| email     | String   | Student email address      |
| createdAt | ISODate  | Account creation timestamp |

---

## 🔔 Notifications Collection

Stores notifications associated with students.

```json
{
  "_id": ObjectId,
  "studentId": ObjectId,
  "type": "Placement",
  "title": "Placement Drive",
  "message": "Microsoft is hiring",
  "isRead": false,
  "createdAt": ISODate("2026-04-22T17:51:18Z")
}
```

### Fields

| Field     | Type     | Description                     |
| --------- | -------- | ------------------------------- |
| _id       | ObjectId | Notification identifier         |
| studentId | ObjectId | Reference to the student        |
| type      | String   | Placement / Event / Result      |
| title     | String   | Notification title              |
| message   | String   | Notification content            |
| isRead    | Boolean  | Read status                     |
| createdAt | ISODate  | Notification creation timestamp |

---

# 🔗 Entity Relationship

Each student can receive multiple notifications.

```text
Student (1)
     │
     ├──────── Notification
     ├──────── Notification
     ├──────── Notification
     └──────── Notification
```

**Relationship:** One Student → Many Notifications

The `studentId` field in the **notifications** collection references the corresponding student.

---

# 📌 Indexing Strategy

Indexes improve query performance by reducing the time required to search large collections.

```javascript
// Retrieve notifications for a student
db.notifications.createIndex({ studentId: 1 })

// Retrieve unread notifications efficiently
db.notifications.createIndex({ studentId: 1, isRead: 1 })

// Filter notifications by type
db.notifications.createIndex({ type: 1 })

// Sort notifications by creation time
db.notifications.createIndex({ createdAt: -1 })
```

---

# ⚠️ Performance Challenges

As the number of notifications increases, several issues may arise:

* Slow queries without proper indexing.
* Higher memory consumption when retrieving all notifications.
* Expensive sorting operations on large datasets.
* Increased query execution time due to collection growth.
* Higher response latency during peak usage.

---

# 🚀 Optimization Techniques

To maintain system performance as data grows:

* ✅ Create indexes on frequently queried fields.
* ✅ Implement pagination using `skip()` and `limit()`.
* ✅ Use projection to retrieve only required fields.
* ✅ Archive or delete outdated notifications.
* ✅ Enable sharding for very large collections.
* ✅ Cache frequently accessed notifications using Redis.
* ✅ Monitor database performance and optimize slow queries.

---

# 💻 MongoDB Queries

## 1️⃣ Get Notifications

Retrieve paginated notifications for a student.

```javascript
db.notifications
  .find({
    studentId: ObjectId(studentId)
  })
  .sort({
    createdAt: -1
  })
  .skip(0)
  .limit(10)
```

---

## 2️⃣ Get Notification by ID

Retrieve a specific notification.

```javascript
db.notifications.findOne({
  _id: ObjectId(notificationId)
})
```

---

## 3️⃣ Create Notification

Insert a new notification document.

```javascript
db.notifications.insertOne({
  studentId: ObjectId(studentId),
  type: "Placement",
  title: "Placement Drive",
  message: "Microsoft is hiring",
  isRead: false,
  createdAt: new Date()
})
```

---

## 4️⃣ Mark Notification as Read

Update the read status of a notification.

```javascript
db.notifications.updateOne(
  {
    _id: ObjectId(notificationId)
  },
  {
    $set: {
      isRead: true
    }
  }
)
```

---

## 5️⃣ Mark All Notifications as Read

Update every notification belonging to a student.

```javascript
db.notifications.updateMany(
  {
    studentId: ObjectId(studentId)
  },
  {
    $set: {
      isRead: true
    }
  }
)
```

---

## 6️⃣ Delete Notification

Remove a notification from the database.

```javascript
db.notifications.deleteOne({
  _id: ObjectId(notificationId)
})
```

---

# 📊 Database Design Summary

| Feature      | Implementation                              |
| ------------ | ------------------------------------------- |
| Database     | MongoDB                                     |
| Data Model   | Document-Oriented                           |
| Relationship | One Student → Many Notifications            |
| Identifier   | ObjectId                                    |
| Indexing     | Student ID, Read Status, Type, Created Time |
| Pagination   | `skip()` and `limit()`                      |
| Caching      | Redis                                       |
| Scalability  | Horizontal Sharding                         |

---

# 📌 Conclusion

MongoDB is an excellent choice for the Campus Notification System due to its flexible document model, high write throughput, and scalability. By combining efficient indexing, pagination, caching, and sharding, the system can handle a growing number of students and notifications while maintaining fast response times and reliable performance.

# ⚡ Stage 3 – Query Analysis & Optimization

This document analyzes the SQL query used to retrieve unread notifications, identifies potential performance bottlenecks, and provides optimization strategies to ensure efficient query execution in a large-scale notification system.

---

# 📑 Table of Contents

* Query Analysis
* Original SQL Query
* Query Validation
* Performance Challenges
* Optimized Query
* Indexing Strategy
* Computational Complexity
* Indexing Best Practices
* Additional SQL Query
* Additional Index
* Conclusion

---

# 🔍 Query Analysis

The following SQL query retrieves all unread notifications for a specific student and sorts them by creation time.

## Original SQL Query

```sql
SELECT *
FROM notifications
WHERE student_id = 1042
AND is_read = FALSE
ORDER BY created_at ASC;
```

---

# ✅ Query Validation

The query is logically correct.

It performs the following operations:

* Retrieves notifications belonging to student **1042**
* Filters only **unread** notifications
* Sorts the results in **ascending order of creation time**

While suitable for small datasets, this query may become inefficient when the notifications table grows to millions of records.

---

# ⚠️ Performance Challenges

## 1️⃣ Using `SELECT *`

Selecting all columns retrieves unnecessary data, increasing:

* Memory usage
* Network bandwidth
* Query execution time

If the application only displays a few fields (such as title, message, and timestamp), retrieving every column is inefficient.

---

## 2️⃣ Missing Indexes

Without indexes on the filtering columns, the database performs a **full table scan**.

This results in:

* Higher CPU usage
* Increased disk I/O
* Slower response times as data volume increases

---

## 3️⃣ Sorting Overhead

The query sorts results using the `created_at` column.

If no suitable index exists, the database performs an additional sorting operation, increasing execution time.

---

# 🚀 Optimized Query

Instead of retrieving every column, select only the fields required by the application.

```sql
SELECT
    id,
    title,
    message,
    type,
    created_at
FROM notifications
WHERE student_id = 1042
AND is_read = FALSE
ORDER BY created_at ASC;
```

### Benefits

* Reduces unnecessary data transfer
* Lowers memory consumption
* Improves response time
* Makes better use of covering indexes

---

# 📌 Recommended Composite Index

Create the following composite index to optimize filtering and sorting.

```sql
CREATE INDEX idx_notifications_student_read_created
ON notifications(student_id, is_read, created_at);
```

## Advantages

This index enables the database to:

* Quickly locate notifications for a specific student
* Efficiently filter unread notifications
* Return results already sorted by `created_at`
* Minimize disk reads and avoid unnecessary sorting

---

# 📊 Computational Complexity

## Without an Index

| Operation | Complexity     |
| --------- | -------------- |
| Searching | **O(n)**       |
| Sorting   | **O(n log n)** |

A full table scan and separate sorting operation make the query inefficient for large datasets.

---

## With the Recommended Index

| Operation | Complexity          |
| --------- | ------------------- |
| Searching | **O(log n)**        |
| Sorting   | Usually unnecessary |

Since the index stores records in the required order, the database can often avoid an additional sorting step.

---

# 🏗️ Indexing Best Practices

Indexes significantly improve read performance, but they should be used strategically.

## Avoid Indexing Every Column

Creating indexes on all columns is not recommended because it:

* Consumes additional storage
* Slows down `INSERT`, `UPDATE`, and `DELETE` operations
* Increases maintenance overhead

## Recommended Practice

Create indexes only on columns that are frequently used for:

* Filtering (`WHERE`)
* Sorting (`ORDER BY`)
* Joining (`JOIN`)
* Searching (`LIKE`, equality conditions)

---

# 💻 Additional SQL Query

Retrieve all students who received **Placement** notifications within the last seven days.

```sql
SELECT DISTINCT student_id
FROM notifications
WHERE type = 'Placement'
AND created_at >= NOW() - INTERVAL 7 DAY;
```

---

# 📌 Additional Index

Optimize the above query with the following index.

```sql
CREATE INDEX idx_notifications_type_created
ON notifications(type, created_at);
```

### Benefits

This index allows the database to:

* Quickly filter notifications by type
* Efficiently search recent notifications
* Reduce query execution time
* Improve scalability for reporting queries

---

# 📈 Performance Summary

| Optimization                    | Benefit                                   |
| ------------------------------- | ----------------------------------------- |
| Select required columns         | Reduces memory usage and data transfer    |
| Composite indexes               | Faster filtering and sorting              |
| Avoid `SELECT *`                | Improves query efficiency                 |
| Index frequently queried fields | Minimizes full table scans                |
| Proper sorting index            | Eliminates unnecessary sorting operations |

---

# 🎯 Conclusion

The original SQL query is functionally correct but requires optimization for production-scale applications.

By retrieving only the required columns and implementing composite indexes, the database can significantly reduce query execution time, minimize resource consumption, and efficiently handle millions of notifications. Proper indexing, combined with thoughtful query design, ensures the notification system remains fast, scalable, and responsive as data volume grows.

# 🚀 Stage 4 – System Performance Improvements

This document outlines strategies to improve the performance and scalability of the **Campus Notification System** using **Redis** for caching and **Socket.IO** for real-time notification delivery.

As the number of users and notifications grows, these technologies help reduce database load, improve response times, and provide a seamless user experience.

---

# 📑 Table of Contents

* System Overview
* Why Use Caching?
* Why Redis?
* Caching Workflow
* Cache Invalidation Strategy
* Real-Time Notification Delivery
* Socket.IO Workflow
* Why Redis and Socket.IO Together?
* System Architecture
* Benefits
* Conclusion

---

# 📌 System Overview

In a large-scale notification platform, querying the database for every request can lead to:

* Increased response times
* Higher database load
* Reduced scalability
* Poor user experience

To address these challenges, the system incorporates:

* **Redis** for high-speed caching
* **Socket.IO** for instant notification delivery

Together, these technologies ensure fast, scalable, and real-time communication.

---

# ⚡ Why Use Caching?

Caching stores frequently accessed data in memory, allowing applications to retrieve information much faster than querying the database.

### Advantages

* 🚀 Faster response times
* 📉 Reduced database load
* 💾 Lower latency
* 📈 Improved scalability
* 😊 Better user experience

---

# 🟥 Why Redis?

**Redis** is an in-memory data store designed for extremely fast read and write operations.

### Key Features

* In-memory storage for high performance
* Millisecond response times
* Supports key-value data structures
* Lightweight and highly scalable
* Ideal for caching frequently accessed notifications

---

# 🔄 Caching Workflow

The application follows a **Cache-Aside Pattern** for notification retrieval.

```text
User Requests Notifications
            │
            ▼
     Check Redis Cache
            │
      ┌─────┴─────┐
      │           │
 Cache Hit     Cache Miss
      │           │
      ▼           ▼
Return Data   Query Database
                  │
                  ▼
         Store Results in Redis
                  │
                  ▼
           Return Notifications
```

### Workflow Steps

1. The user requests notifications.
2. The application checks Redis for cached data.
3. If the cache contains the notifications (**Cache Hit**), they are returned immediately.
4. If the cache does not contain the data (**Cache Miss**), the application retrieves notifications from the database.
5. The fetched notifications are stored in Redis before being returned to the user.

This approach significantly reduces repeated database queries.

---

# 🔄 Cache Invalidation Strategy

Cached data must remain consistent with the database.

Whenever a notification is:

* Created
* Updated
* Marked as Read
* Deleted

the corresponding Redis cache should be refreshed or removed.

### Recommended Process

```text
Update Database
      │
      ▼
Invalidate Redis Cache
      │
      ▼
Next Request
      │
      ▼
Fetch Fresh Data
      │
      ▼
Store Updated Data in Redis
```

This ensures users always receive the latest notification data.

---

# 📡 Real-Time Notification Delivery

To deliver notifications instantly, the system uses **Socket.IO**, a library built on top of WebSockets.

Socket.IO enables bidirectional communication between the client and server, allowing notifications to be pushed to users without requiring page refreshes.

---

# 🔔 Socket.IO Workflow

```text
User Logs In
      │
      ▼
Socket.IO Connection Established
      │
      ▼
Admin Creates Notification
      │
      ▼
Notification Saved to Database
      │
      ▼
Redis Cache Invalidated
      │
      ▼
Backend Emits "new-notification"
      │
      ▼
Student Receives Notification Instantly
```

### Event Name

```text
new-notification
```

---

# 🤝 Why Use Redis and Socket.IO Together?

Redis and Socket.IO complement each other by addressing different aspects of system performance.

| Technology | Purpose                                             |
| ---------- | --------------------------------------------------- |
| Redis      | Reduces database queries through caching            |
| Socket.IO  | Delivers notifications instantly to connected users |

### Combined Benefits

* ⚡ Faster application response times
* 📉 Reduced database workload
* 📈 Improved scalability
* 🔄 Real-time updates
* 😊 Enhanced user experience

---

# 🏗️ System Architecture

```text
                 +-------------------+
                 |      Client       |
                 | (Web / Mobile App)|
                 +---------+---------+
                           |
                    HTTP / WebSocket
                           |
                 +---------v---------+
                 |   Node.js Server  |
                 +----+---------+----+
                      |         |
              Redis Cache   Socket.IO
                      |         |
                      +----+----+
                           |
                    MySQL Database
```

---

# 📊 Performance Benefits

| Feature               | Benefit                                   |
| --------------------- | ----------------------------------------- |
| Redis Caching         | Faster data retrieval                     |
| Cache-Aside Pattern   | Reduces repeated database queries         |
| Cache Invalidation    | Maintains data consistency                |
| Socket.IO             | Instant notification delivery             |
| WebSockets            | Persistent low-latency communication      |
| Combined Architecture | High scalability and improved performance |

---

# 🎯 Conclusion

Integrating **MySQL**, **Redis**, and **Socket.IO** creates a robust and scalable notification system capable of supporting thousands of concurrent users.

* **Redis** minimizes unnecessary database access by caching frequently requested notifications.
* **Socket.IO** provides real-time communication, ensuring users receive notifications instantly without refreshing the application.
* Together, these technologies improve performance, reduce server load, and deliver a responsive user experience even as the system scales.

# 🚀 Stage 5 – Scalable Notification Processing

This document explains how to design a scalable and reliable notification processing system using **RabbitMQ**, **Socket.IO**, and **background workers**.

Instead of sending notifications directly from the API, notifications are processed asynchronously through a message queue. This approach improves application performance, reliability, and scalability.

---

# 📑 Table of Contents

* Overview
* Why Use a Message Queue?
* Why RabbitMQ?
* Notification Processing Workflow
* Retry Mechanism & Dead Letter Queue (DLQ)
* System Architecture
* Benefits
* Conclusion

---

# 📌 Overview

In a high-traffic notification system, sending notifications synchronously can increase API response time and overload the server.

To overcome this challenge, the system uses **RabbitMQ** as a message broker.

When a notification is created:

1. The notification is stored in the database.
2. A message is published to RabbitMQ.
3. A background worker consumes the message.
4. The notification is delivered to connected users via **Socket.IO**.

This asynchronous architecture keeps the API fast and ensures reliable notification delivery.

---

# 📨 Why Use a Message Queue?

A message queue decouples notification creation from notification delivery.

Instead of making users wait until the notification is sent, the API immediately queues the task and responds.

### Advantages

* ⚡ Faster API response times
* 📈 Better scalability
* 🔄 Asynchronous processing
* 🛡️ Improved fault tolerance
* 📦 Handles traffic spikes efficiently

---

# 🐇 Why RabbitMQ?

**RabbitMQ** is a reliable and widely used message broker designed for asynchronous communication between services.

### Key Features

* Reliable message delivery
* Persistent message storage
* Retry support
* Dead Letter Queue (DLQ)
* Load balancing across multiple workers
* Scalable producer-consumer architecture

---

# 🔄 Notification Processing Workflow

The notification lifecycle follows these steps:

```text id="glff3d"
1. Create Notification Request
            │
            ▼
2. Save Notification to Database
            │
            ▼
3. Publish Message to RabbitMQ
            │
            ▼
4. Background Worker Consumes Message
            │
            ▼
5. Deliver Notification via Socket.IO
            │
            ▼
6. User Receives Notification Instantly
```

### Step-by-Step Process

1. A new notification request is received by the API.
2. The notification is stored in the database.
3. A message containing the notification details is added to the RabbitMQ queue.
4. A background worker retrieves the message from the queue.
5. The worker sends the notification using Socket.IO.
6. Connected users receive the notification in real time.

---

# 🔁 Retry Mechanism & Dead Letter Queue (DLQ)

Notification delivery may occasionally fail due to temporary issues such as network interruptions or disconnected clients.

To improve reliability:

* Retry failed deliveries automatically.
* Attempt delivery a limited number of times.
* Move permanently failed messages to a **Dead Letter Queue (DLQ)** for later inspection and processing.

### Workflow

```text id="8twmff"
Notification Queue
        │
        ▼
Background Worker
        │
   Delivery Success?
     ┌──────┴──────┐
     │             │
    Yes            No
     │             │
     ▼             ▼
 Delivered      Retry
                   │
          Max Retries Reached?
             ┌─────┴─────┐
             │           │
            No          Yes
             │           │
             ▼           ▼
         Retry Again   Dead Letter Queue
```

This approach prevents message loss and improves overall system reliability.

---

# 🏗️ System Architecture

```text id="y6qvnc"
                 +------------------+
                 |      Client      |
                 +---------+--------+
                           |
                     HTTP Request
                           |
                 +---------v--------+
                 | Notification API |
                 +---------+--------+
                           |
                    Save Notification
                           |
                           ▼
                 +------------------+
                 |     Database     |
                 +---------+--------+
                           |
                    Publish Message
                           |
                           ▼
                 +------------------+
                 |     RabbitMQ     |
                 +---------+--------+
                           |
                    Consume Message
                           |
                           ▼
                 +------------------+
                 | Background Worker|
                 +---------+--------+
                           |
                     Socket.IO Event
                           |
                           ▼
                 +------------------+
                 | Connected Users  |
                 +------------------+
```

---

# 📊 Benefits

| Feature                  | Benefit                            |
| ------------------------ | ---------------------------------- |
| RabbitMQ                 | Asynchronous message processing    |
| Background Workers       | Offloads heavy tasks from the API  |
| Socket.IO                | Real-time notification delivery    |
| Retry Mechanism          | Reliable message processing        |
| Dead Letter Queue        | Prevents message loss              |
| Queue-Based Architecture | Handles traffic spikes efficiently |
| Decoupled Services       | Easier scaling and maintenance     |

---

# 🎯 Performance Advantages

Implementing RabbitMQ provides several performance improvements:

* 🚀 Faster API responses by processing notifications asynchronously.
* 📉 Reduced server workload during peak traffic.
* 📦 Better handling of high notification volumes.
* 🔄 Automatic retry of failed notification deliveries.
* 📈 Improved scalability through multiple background workers.
* 🛡️ Reliable and fault-tolerant message processing.

---

# 📌 Conclusion

Using **RabbitMQ** together with **Socket.IO** creates a scalable, reliable, and high-performance notification processing system.

* **RabbitMQ** ensures notifications are processed asynchronously through a message queue.
* **Background workers** handle delivery without blocking API requests.
* **Socket.IO** provides instant, real-time notification delivery to connected users.
* **Retry mechanisms** and a **Dead Letter Queue (DLQ)** improve reliability by preventing message loss.

This architecture enables the notification system to efficiently support thousands of concurrent users while maintaining fast response times, high availability, and a seamless user experience.

# ⭐ Stage 6 – Priority Inbox

This document describes the implementation of a **Priority Inbox** for the Campus Notification System. Instead of displaying notifications solely by creation time, notifications are ordered based on their importance, ensuring that users see the most critical updates first.

---

# 📑 Table of Contents

* Overview
* Priority Levels
* SQL Query
* Query Explanation
* Why Use Priority-Based Sorting?
* Performance Considerations
* Benefits
* Conclusion

---

# 📌 Overview

In a campus notification system, not all notifications have the same level of importance.

For example:

* Placement opportunities require immediate attention.
* Examination results are time-sensitive.
* General event announcements are informative but less urgent.

To improve the user experience, notifications are sorted by **priority first**, followed by **creation time**.

---

# 🏆 Priority Levels

Notifications are displayed according to the following priority order:

| Priority | Notification Type | Description                                                   |
| -------- | ----------------- | ------------------------------------------------------------- |
| 🥇 1     | Placement         | Highest priority – Job opportunities and placement drives     |
| 🥈 2     | Result            | Semester results, exam updates, and academic notifications    |
| 🥉 3     | Event             | Campus events, workshops, seminars, and general announcements |

---

# 💻 SQL Query

The following SQL query retrieves all notifications for a student and sorts them by priority and recency.

```sql id="k9cxk6"
SELECT *
FROM notifications
WHERE student_id = ?
ORDER BY
CASE
    WHEN type = 'Placement' THEN 1
    WHEN type = 'Result' THEN 2
    WHEN type = 'Event' THEN 3
END,
created_at DESC;
```

---

# 🔍 Query Explanation

The query performs the following operations:

1. Retrieves notifications for a specific student.
2. Assigns a numeric priority using the `CASE` expression.
3. Sorts notifications based on priority.
4. Orders notifications of the same priority by **most recent first** using `created_at DESC`.

This ensures that users always see the most important and latest notifications at the top of their inbox.

---

# 🚀 Why Use Priority-Based Sorting?

Priority-based sorting enhances the notification experience by presenting critical updates before less important ones.

### Advantages

* ⭐ Important notifications appear first.
* 💼 Placement opportunities are highly visible.
* 📚 Academic result updates receive higher priority.
* 📅 Event announcements remain accessible without overshadowing critical updates.
* ⏱️ Recent notifications are displayed first within each priority level.

---

# ⚙️ Performance Considerations

To maintain efficient query performance as the notification table grows:

* Create indexes on frequently filtered columns such as `student_id`.
* Consider indexing `type` if priority filtering is common.
* Use pagination (`LIMIT` and `OFFSET`) for large datasets.
* Retrieve only required columns instead of using `SELECT *` in production environments.

### Recommended Composite Index

```sql id="m4tqf8"
CREATE INDEX idx_notifications_student_type_created
ON notifications(student_id, type, created_at);
```

This index helps improve filtering and sorting performance for priority-based notification retrieval.

---

# 📊 Benefits

| Feature                  | Benefit                                                       |
| ------------------------ | ------------------------------------------------------------- |
| Priority Sorting         | Displays the most important notifications first               |
| CASE Expression          | Implements custom priority ordering                           |
| Recent First             | Shows the latest notifications within each priority level     |
| Better Visibility        | Ensures placement and result notifications are not overlooked |
| Improved User Experience | Makes the inbox more organized and relevant                   |

---

# 🎯 Conclusion

Implementing a **Priority Inbox** significantly improves the usability of the Campus Notification System.

By combining **priority-based sorting** with **reverse chronological ordering**, users receive the most relevant notifications first while still seeing the latest updates within each category. This approach keeps the query simple, efficient, and scalable, providing a better overall notification experience.
