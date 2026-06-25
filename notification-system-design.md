# Notification System Design

## Stage 1 – REST API Specification

## Overview

The Notification System is designed to deliver real-time updates to students regarding **placement drives, examination results, and campus events**. The platform exposes a RESTful API for managing notifications and uses **Socket.IO (WebSockets)** to instantly push newly created notifications to connected users.

The APIs follow REST architectural principles with consistent resource naming, JSON request/response formats, and standard HTTP status codes.

---

# Notification Resource

A notification contains the following information.

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

---

# Common HTTP Headers

### Request Headers

```http
Content-Type: application/json
Accept: application/json
```

### Response Headers

```http
Content-Type: application/json
```

---

# REST API Endpoints

## 1. Retrieve Notifications

**Endpoint**

```http
GET /api/notifications
```

### Description

Returns a paginated list of notifications for the authenticated student.

### Query Parameters

| Parameter | Type    | Required | Description                                            |
| --------- | ------- | -------- | ------------------------------------------------------ |
| page      | Integer | No       | Page number (default: 1)                               |
| limit     | Integer | No       | Number of records per page                             |
| type      | String  | No       | Filter by notification type (Placement, Event, Result) |

### Example Request

```http
GET /api/notifications?page=1&limit=10&type=Placement
```

### Success Response (200 OK)

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
      "message": "Amazon is hiring.",
      "isRead": false,
      "createdAt": "2026-04-22T17:51:18Z"
    }
  ]
}
```

---

## 2. Retrieve a Notification

**Endpoint**

```http
GET /api/notifications/{id}
```

### Description

Returns details of a specific notification.

### Example Request

```http
GET /api/notifications/123
```

### Success Response (200 OK)

```json
{
  "success": true,
  "notification": {
    "id": "123",
    "type": "Result",
    "title": "Semester Result",
    "message": "Results have been published.",
    "isRead": false,
    "createdAt": "2026-04-22T17:51:18Z"
  }
}
```

---

## 3. Create a Notification

**Endpoint**

```http
POST /api/notifications
```

### Description

Creates a new notification that will later be delivered to students.

### Request Body

```json
{
  "type": "Placement",
  "title": "Placement Drive",
  "message": "Microsoft is hiring."
}
```

### Success Response (201 Created)

```json
{
  "success": true,
  "message": "Notification created successfully.",
  "notificationId": "12345"
}
```

---

## 4. Mark a Notification as Read

**Endpoint**

```http
PATCH /api/notifications/{id}/read
```

### Description

Marks a single notification as read.

### Success Response

```json
{
  "success": true,
  "message": "Notification marked as read."
}
```

---

## 5. Mark All Notifications as Read

**Endpoint**

```http
PATCH /api/notifications/read-all
```

### Description

Marks all notifications for the authenticated student as read.

### Success Response

```json
{
  "success": true,
  "message": "All notifications marked as read."
}
```

---

## 6. Delete a Notification

**Endpoint**

```http
DELETE /api/notifications/{id}
```

### Description

Deletes the specified notification.

### Success Response

```json
{
  "success": true,
  "message": "Notification deleted successfully."
}
```

---

# HTTP Status Codes

| Status Code               | Description                    |
| ------------------------- | ------------------------------ |
| 200 OK                    | Request processed successfully |
| 201 Created               | Resource created successfully  |
| 400 Bad Request           | Invalid request parameters     |
| 404 Not Found             | Notification does not exist    |
| 500 Internal Server Error | Unexpected server error        |

---

# Standard Error Response

```json
{
  "success": false,
  "message": "Notification not found."
}
```

---

# Real-Time Notification Delivery

## Technology

* Socket.IO
* WebSockets

## Workflow

1. Student logs into the application.
2. Client establishes a WebSocket connection.
3. Server stores the student's active socket connection.
4. Administrator creates a notification.
5. Notification is saved in the database.
6. Server emits the notification to the appropriate connected students.
7. Students receive the notification instantly without refreshing the page.

---

## Socket Event

### Event Name

```text
new-notification
```

### Event Payload

```json
{
  "id": "123",
  "type": "Placement",
  "title": "Placement Drive",
  "message": "Amazon is hiring.",
  "createdAt": "2026-04-22T17:51:18Z"
}
```

---

# REST API Summary

| HTTP Method | Endpoint                     | Description                    |
| ----------- | ---------------------------- | ------------------------------ |
| GET         | /api/notifications           | Retrieve all notifications     |
| GET         | /api/notifications/{id}      | Retrieve a notification by ID  |
| POST        | /api/notifications           | Create a new notification      |
| PATCH       | /api/notifications/{id}/read | Mark a notification as read    |
| PATCH       | /api/notifications/read-all  | Mark all notifications as read |
| DELETE      | /api/notifications/{id}      | Delete a notification          |

---

# Design Conventions

* RESTful resource naming using plural nouns.
* Standard HTTP methods (GET, POST, PATCH, DELETE).
* JSON used for all request and response bodies.
* Notification identifiers use UUID format.
* Date and time values follow the ISO 8601 standard (UTC).
* Consistent success and error response structure across all endpoints.
* Pagination support for scalable notification retrieval.
* Real-time notification delivery using Socket.IO and WebSockets.

# Stage 2 – Database Design

## Database Selection

### Chosen Database: MongoDB

MongoDB is selected as the primary database for the notification system because it offers high performance, flexible document storage, and excellent scalability. Its document-oriented architecture aligns naturally with JSON-based REST APIs and integrates seamlessly with Node.js through Mongoose.

### Why MongoDB?

* Stores notifications as JSON-like BSON documents.
* Delivers high write throughput, making it suitable for systems generating large volumes of notifications.
* Supports horizontal scaling through sharding.
* Provides a flexible schema, allowing new notification attributes to be added without database migrations.
* Integrates efficiently with Node.js applications using Mongoose ODM.

---

# Database Collections

## 1. Students Collection

Stores basic student information.

```json
{
  "_id": ObjectId,
  "name": "Santhosh",
  "email": "santhosh@example.com",
  "createdAt": ISODate("2026-04-22T17:51:18Z")
}
```

---

## 2. Notifications Collection

Stores notifications associated with individual students.

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

---

# Data Relationship

A **one-to-many** relationship exists between students and notifications.

```
Student
   │
   ├── Notification
   ├── Notification
   ├── Notification
   └── ...
```

Each notification references its owner through the `studentId` field.

---

# Indexing Strategy

To optimize query performance, the following indexes are recommended.

```javascript
// Retrieve notifications for a student
db.notifications.createIndex({ studentId: 1 });

// Retrieve unread/read notifications efficiently
db.notifications.createIndex({ studentId: 1, isRead: 1 });

// Filter notifications by type
db.notifications.createIndex({ type: 1 });

// Sort notifications by creation time
db.notifications.createIndex({ createdAt: -1 });
```

---

# Scalability Challenges

As the notification volume increases, several performance challenges may arise:

* Slower query execution without appropriate indexes.
* Higher memory usage when retrieving large datasets.
* Increased sorting overhead on large collections.
* Longer response times due to collection growth.
* Greater storage requirements for historical notifications.

---

# Performance Optimization Strategies

To ensure scalability and maintain performance:

* Create indexes on frequently queried fields.
* Implement pagination using `skip()` and `limit()`.
* Use field projection to return only required attributes.
* Archive or remove outdated notifications periodically.
* Enable sharding for very large datasets.
* Cache frequently accessed notifications using Redis.
* Process bulk notification creation asynchronously where appropriate.

---

# MongoDB Operations

## Retrieve Notifications

Returns paginated notifications for a student.

```javascript
db.notifications
  .find({ studentId: ObjectId(studentId) })
  .sort({ createdAt: -1 })
  .skip(0)
  .limit(10);
```

---

## Retrieve Notification by ID

```javascript
db.notifications.findOne({
  _id: ObjectId(notificationId)
});
```

---

## Create Notification

```javascript
db.notifications.insertOne({
  studentId: ObjectId(studentId),
  type: "Placement",
  title: "Placement Drive",
  message: "Microsoft is hiring",
  isRead: false,
  createdAt: new Date()
});
```

---

## Mark Notification as Read

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
);
```

---

## Mark All Notifications as Read

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
);
```

---

## Delete Notification

```javascript
db.notifications.deleteOne({
  _id: ObjectId(notificationId)
});
```

---

# Database Design Summary

MongoDB is well suited for the notification platform due to its document-oriented architecture, high write performance, and horizontal scalability. By combining efficient indexing, pagination, projection, caching, and sharding, the system can continue to deliver fast and reliable notification services even as the number of students and notifications grows significantly.

# Stage 3 – Query Analysis and Optimization

## Query Analysis

### Given SQL Query

```sql
SELECT *
FROM notifications
WHERE student_id = 1042
  AND is_read = FALSE
ORDER BY created_at ASC;
```

### Query Evaluation

The query correctly retrieves all **unread notifications** for the student with ID **1042** and returns them in **ascending order of creation time**.

While the query is functionally correct, its performance may degrade significantly as the notification table grows. In a production environment containing millions of records, additional optimization is necessary to maintain low response times.

---

# Performance Analysis

## 1. Retrieving All Columns

Using `SELECT *` returns every column from the table, regardless of whether all fields are required by the application.

### Drawbacks

* Increases network bandwidth usage.
* Consumes additional memory.
* Slows query execution.
* Prevents efficient use of covering indexes.

Selecting only the required columns reduces unnecessary data transfer and improves overall performance.

---

## 2. Missing Indexes

If no index exists on the filtering columns (`student_id` and `is_read`), the database performs a **full table scan** to locate matching records.

### Impact

* Higher disk I/O.
* Increased CPU utilization.
* Longer query execution time as the table grows.

---

## 3. Sorting Overhead

The query orders results using `created_at`.

Without a suitable index, the database must perform an additional sorting operation after filtering the records.

### Impact

* Additional CPU usage.
* Temporary memory allocation.
* Slower response times for large datasets.

---

# Optimized Query

Instead of retrieving every column, return only the fields required by the client application.

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

* Reduces data transfer.
* Improves query execution speed.
* Lowers memory consumption.
* Enables better index utilization.

---

# Recommended Composite Index

To optimize filtering and sorting, create the following composite index:

```sql
CREATE INDEX idx_notifications_student_read_created
ON notifications(student_id, is_read, created_at);
```

### Advantages

This index enables the database to:

* Locate notifications for a specific student efficiently.
* Filter unread notifications without scanning unrelated rows.
* Return results already ordered by `created_at`.
* Eliminate unnecessary sorting operations in many cases.

---

# Time Complexity

| Scenario             | Estimated Complexity                                                   |
| -------------------- | ---------------------------------------------------------------------- |
| Without Index        | Search: **O(n)**<br>Sort: **O(n log n)**                               |
| With Composite Index | Search: **O(log n)**<br>Sorting: Usually avoided due to index ordering |

Using the composite index significantly reduces query execution time, especially for large notification datasets.

---

# Indexing Best Practices

Not every database column should be indexed.

Although indexes improve read performance, excessive indexing introduces additional overhead.

### Disadvantages of Over-Indexing

* Increased storage consumption.
* Slower INSERT operations.
* Slower UPDATE operations.
* Slower DELETE operations.
* Higher maintenance costs.

Indexes should be created only on columns that are frequently used for:

* Filtering (`WHERE`)
* Sorting (`ORDER BY`)
* Joining (`JOIN`)
* Searching (`LIKE`, equality conditions)

---

# Additional SQL Query

### Requirement

Retrieve all students who received **Placement** notifications during the last seven days.

```sql
SELECT DISTINCT student_id
FROM notifications
WHERE type = 'Placement'
  AND created_at >= NOW() - INTERVAL 7 DAY;
```

---

# Supporting Index

To improve the performance of the above query, create the following composite index:

```sql
CREATE INDEX idx_notifications_type_created
ON notifications(type, created_at);
```

### Benefits

The index allows the database to:

* Quickly filter notifications by type.
* Efficiently identify notifications created within the specified time range.
* Reduce unnecessary table scans.

---

# Performance Summary

| Optimization                        | Benefit                                   |
| ----------------------------------- | ----------------------------------------- |
| Select only required columns        | Reduces memory usage and network overhead |
| Composite indexes                   | Faster filtering and sorting              |
| Avoid `SELECT *`                    | Improves execution efficiency             |
| Index frequently queried fields     | Minimizes full table scans                |
| Use covering indexes where possible | Further reduces disk access               |

---

# Conclusion

The original SQL query is functionally correct but not optimized for large-scale systems. Replacing `SELECT *` with explicit column selection and introducing appropriate composite indexes significantly improves query performance. These optimizations reduce disk I/O, eliminate unnecessary sorting, and enable the notification system to scale efficiently while maintaining fast response times as the volume of data increases.

# Stage 4 – Performance Optimization with Redis and Socket.IO

## Overview

As the number of users and notifications grows, directly querying the database for every request can increase latency and place significant load on the database server. To improve system performance and scalability, the notification system incorporates **Redis** for caching and **Socket.IO (WebSockets)** for real-time notification delivery.

Together, these technologies reduce database load, improve response times, and provide a seamless user experience.

---

# Caching Strategy

## Why Use Redis?

Redis is an in-memory key-value data store designed for extremely fast read and write operations.

Instead of querying the database for every request, frequently accessed notification data is temporarily stored in Redis. This minimizes database access and significantly improves application performance.

### Benefits of Redis

* Low-latency data retrieval
* Reduced database load
* Faster API response times
* Improved application scalability
* Efficient handling of frequently accessed data

---

# Cache Workflow

The notification retrieval process follows a **Cache-Aside (Lazy Loading)** strategy.

### Workflow

1. A user requests their notifications.
2. The application checks Redis for cached notification data.
3. **Cache Hit**

   * Notifications are retrieved directly from Redis.
   * No database query is required.
4. **Cache Miss**

   * Notifications are fetched from the database.
   * The retrieved data is stored in Redis with an expiration time (TTL).
   * The notifications are returned to the user.

```text id="y2k6rx"
Client
   │
   ▼
Application Server
   │
   ▼
Redis Cache
   │
Cache Hit ─────────► Return Notifications
   │
Cache Miss
   ▼
Database
   │
   ▼
Store in Redis
   │
   ▼
Return Notifications
```

---

# Cache Invalidation

To maintain data consistency, cached notifications must be refreshed whenever notification data changes.

### Cache is Invalidated When

* A new notification is created.
* A notification is updated.
* A notification is marked as read.
* A notification is deleted.

### Invalidation Process

1. Update the database.
2. Remove or refresh the corresponding Redis cache.
3. The next request retrieves the latest data from the database.
4. Fresh data is cached again for future requests.

This strategy ensures users always receive the most up-to-date notifications while maintaining high performance.

---

# Real-Time Notification Delivery

## Why Socket.IO?

Socket.IO enables bidirectional communication between the client and server using WebSockets (with automatic fallback mechanisms when necessary).

Unlike traditional HTTP polling, Socket.IO allows the server to push notifications to connected users instantly.

### Benefits

* Instant notification delivery
* Lower network overhead
* Reduced client polling
* Improved user experience
* Persistent client-server connection

---

# Notification Delivery Workflow

1. The user logs into the application.
2. The frontend establishes a Socket.IO connection with the server.
3. The server associates the socket connection with the authenticated user.
4. An administrator creates a new notification.
5. The notification is stored in the database.
6. The user's Redis cache is invalidated.
7. The backend emits a `new-notification` event to the connected client.
8. The client immediately displays the notification without requiring a page refresh.

```text id="4mz2qx"
Admin
   │
Create Notification
   │
   ▼
Application Server
   │
   ▼
Database
   │
Invalidate Redis Cache
   │
   ▼
Socket.IO Server
   │
   ▼
Connected Student
```

---

# Redis and Socket.IO Integration

Redis and Socket.IO address different aspects of system performance and complement each other.

| Technology | Purpose                                                                   |
| ---------- | ------------------------------------------------------------------------- |
| Redis      | Reduces database queries by caching frequently accessed notification data |
| Socket.IO  | Delivers notifications instantly to connected users using WebSockets      |

Using both technologies together provides:

* Faster API response times
* Reduced database workload
* Efficient resource utilization
* Improved scalability
* Real-time user engagement

---

# Scalability Considerations

For large-scale deployments supporting thousands of concurrent users, additional enhancements can be implemented:

* Configure Redis with an appropriate Time-To-Live (TTL) for cached notifications.
* Use Redis Pub/Sub to synchronize notifications across multiple application servers.
* Deploy Socket.IO with a Redis Adapter to support horizontal scaling.
* Implement database connection pooling to optimize resource usage.
* Archive older notifications to reduce active database size.

---

# Architecture Overview

```text id="bw1sk4"
Client
   │
HTTP Request
   ▼
Application Server
   │
   ├────────► Redis Cache
   │            │
   │        Cache Hit
   │            │
   │            ▼
   │      Return Data
   │
   └────────► Database
                  │
                  ▼
          Update Redis Cache

                 │
                 ▼

           Socket.IO Server
                 │
                 ▼
        Connected Student
```

---

# Conclusion

Integrating **MySQL**, **Redis**, and **Socket.IO** creates a high-performance and scalable notification platform.

* **MySQL** provides reliable persistent storage.
* **Redis** accelerates notification retrieval by reducing repetitive database queries.
* **Socket.IO** enables real-time delivery of notifications to connected users.

This architecture ensures low-latency responses, minimizes database load, and delivers a responsive user experience, making the notification system capable of efficiently supporting thousands of concurrent users and millions of notifications.


# Stage 5 – Scalable Notification Processing

## Overview

In a large-scale notification system, sending notifications synchronously during an API request can increase response times and reduce system throughput. To improve scalability and reliability, notification delivery should be **decoupled** from the API using a **message queue**.

A message broker such as **RabbitMQ** enables asynchronous processing by allowing the API to publish notification events while background workers handle the actual delivery to users.

---

# Why Use RabbitMQ?

RabbitMQ acts as an intermediary between the Notification API and the notification delivery service.

Instead of sending notifications immediately, the API publishes a message to a queue. Background worker processes consume messages from the queue and deliver notifications independently.

### Benefits

* Asynchronous notification processing
* Faster API response times
* Improved scalability
* Reliable message delivery
* Better fault tolerance
* Load balancing across multiple workers

---

# Notification Processing Workflow

The notification delivery process consists of the following steps:

1. A client submits a request to create a notification.
2. The Notification API validates the request.
3. The notification is stored in the database.
4. A notification event is published to a RabbitMQ queue.
5. The API immediately returns a success response to the client.
6. A background worker consumes the queued message.
7. The worker delivers the notification through Socket.IO.
8. Connected users receive the notification instantly.

```text id="8dzt1l"
Client
   │
Create Notification
   │
   ▼
Notification API
   │
Save Notification
   ▼
Database
   │
Publish Event
   ▼
RabbitMQ Queue
   │
Consume Message
   ▼
Background Worker
   │
Deliver Notification
   ▼
Socket.IO Server
   │
   ▼
Connected Users
```

---

# Retry Mechanism

Temporary failures such as network interruptions or service outages should not result in permanent message loss.

If notification delivery fails:

1. The worker retries the operation a predefined number of times.
2. If all retry attempts fail, the message is moved to a **Dead Letter Queue (DLQ)**.
3. Failed messages can later be inspected, monitored, and reprocessed without affecting the main queue.

```text id="ccjlwm"
RabbitMQ Queue
      │
      ▼
Background Worker
      │
Success ─────────► Deliver Notification
      │
Failure
      ▼
Retry
      │
Still Fails
      ▼
Dead Letter Queue (DLQ)
```

---

# Advantages of Asynchronous Processing

| Feature            | Benefit                                                 |
| ------------------ | ------------------------------------------------------- |
| Message Queue      | Decouples notification creation from delivery           |
| Background Workers | Processes notifications independently of API requests   |
| Retry Mechanism    | Handles temporary delivery failures automatically       |
| Dead Letter Queue  | Stores permanently failed messages for later analysis   |
| Horizontal Scaling | Multiple workers can process notifications concurrently |

---

# Scalability Enhancements

As system traffic increases, RabbitMQ can be scaled efficiently by:

* Running multiple consumer workers in parallel.
* Distributing workload across multiple queues.
* Using message acknowledgments to ensure reliable processing.
* Configuring message durability for persistence.
* Monitoring queue length and worker performance.
* Scaling workers automatically based on queue size.

---

# High-Level Architecture

```text id="0lb0i2"
                    Client
                       │
                       ▼
              Notification API
                       │
         Save Notification to Database
                       │
                       ▼
                   Database
                       │
              Publish Notification Event
                       ▼
                 RabbitMQ Queue
                       │
           ┌───────────┴───────────┐
           ▼                       ▼
     Worker Instance 1       Worker Instance 2
           │                       │
           └───────────┬───────────┘
                       ▼
                 Socket.IO Server
                       │
                       ▼
               Connected Users
```

---

# System Benefits

By introducing RabbitMQ into the notification pipeline, the system achieves:

* Fast API responses through asynchronous processing.
* Reliable notification delivery using durable queues.
* Automatic retry handling for transient failures.
* Improved fault tolerance with Dead Letter Queues.
* Efficient handling of traffic spikes.
* Horizontal scalability through multiple worker instances.
* Better separation of responsibilities between notification creation and delivery.

---

# Conclusion

Integrating **RabbitMQ** with **Socket.IO** enables a scalable, reliable, and high-performance notification system.

The Notification API focuses solely on validating requests and persisting notification data, while RabbitMQ manages asynchronous processing and worker coordination. Background workers handle notification delivery independently, ensuring that API responsiveness remains high even under heavy traffic.

This architecture supports large-scale deployments by providing reliable message processing, fault tolerance through retry and Dead Letter Queues, and seamless horizontal scaling for thousands of concurrent users.

# Stage 6 – Priority Inbox

## Overview

Displaying notifications solely by creation time may cause critical updates to be buried beneath less important ones. To improve the user experience, the notification system implements **priority-based sorting**, ensuring that the most important notifications are displayed first.

In this system, notifications are prioritized based on their category while maintaining chronological order within each priority level.

---

# Notification Priority

The notification categories are assigned the following priority levels:

| Priority | Notification Type |
| -------- | ----------------- |
| 1        | Placement         |
| 2        | Result            |
| 3        | Event             |

This ordering ensures that placement opportunities are immediately visible to students, followed by examination results and general campus events.

---

# SQL Query

The following query retrieves notifications for a student and orders them according to the defined priority.

```sql id="f6o2kn"
SELECT
    id,
    title,
    message,
    type,
    is_read,
    created_at
FROM notifications
WHERE student_id = ?
ORDER BY
    CASE
        WHEN type = 'Placement' THEN 1
        WHEN type = 'Result' THEN 2
        WHEN type = 'Event' THEN 3
        ELSE 4
    END,
    created_at DESC;
```

---

# Query Explanation

The query performs the following operations:

1. Retrieves notifications belonging to the specified student.
2. Assigns a priority value to each notification using the `CASE` expression.
3. Sorts notifications by priority in ascending order.
4. Within each priority level, orders notifications by creation time in descending order so that the newest notifications appear first.

This ensures users always see the most relevant and recent information.

---

# Performance Considerations

While the `CASE` expression provides a simple solution, evaluating it for every row can become less efficient as the notification table grows.

For large-scale systems, the following optimizations are recommended:

* Add a dedicated `priority` column to the `notifications` table.
* Assign the priority value when the notification is created.
* Create a composite index on frequently sorted columns.

Example:

```sql id="8v8b4z"
CREATE INDEX idx_notifications_student_priority_created
ON notifications(student_id, priority, created_at DESC);
```

With this approach, the query becomes simpler and allows the database optimizer to use the index more effectively.

```sql id="mspgxu"
SELECT
    id,
    title,
    message,
    type,
    is_read,
    created_at
FROM notifications
WHERE student_id = ?
ORDER BY priority ASC, created_at DESC;
```

---

# Benefits of Priority-Based Sorting

* Ensures critical notifications appear first.
* Improves visibility of placement opportunities.
* Prevents important updates from being overlooked.
* Maintains chronological order within each priority category.
* Provides a more intuitive and user-friendly notification experience.

---

# High-Level Workflow

```text id="t9ekvc"
Notifications
      │
      ▼
Filter by Student
      │
      ▼
Assign Priority
      │
      ▼
Sort by Priority
      │
      ▼
Sort by Creation Time
      │
      ▼
Display to User
```

---

# Conclusion

Implementing a **Priority Inbox** enhances the notification experience by presenting the most important information first while preserving the recency of notifications within each category. Although the `CASE`-based query is suitable for small and medium-sized applications, storing a dedicated **priority** field and indexing it provides better performance and scalability for large-scale systems with millions of notifications.
