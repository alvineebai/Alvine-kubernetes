## Lab: Deploying PHP Guestbook application with Redis

### What is Redis and Why Use It in DevOps?
Redis (Remote Dictionary Server) is an open-source, in-memory data store used as a cache, database, and message broker. It is designed for high-speed performance and supports various data structures like strings, lists, sets, and hashes.

### Why Use Redis for DevOps?
For DevOps engineers, Redis is a powerful tool that helps improve performance, scalability, and reliability in distributed systems. Key use cases include:

🔄 Caching: Reduces database load and speeds up applications.
📬 Message Queues: Used for real-time event streaming and task queuing.
📊 Session Management: Stores user sessions in a fast and scalable way.
⚡ High Availability: Supports replication and clustering for fault tolerance.
🔍 Real-time Monitoring: Used for tracking logs, metrics, and alerts.
By deploying a Redis master-follower setup, you ensure data redundancy and load balancing, making your system more efficient and resilient.

### Architecture Overview
Redis Master: Handles write operations and replicates data to followers.
Redis Follower: Reads data replicated from the master and handles read operations.
Frontend Application: A simple web interface that interacts with Redis (reads from the follower, writes to the master).

### Objective
- Start up a Redis leader.
- Start up two Redis followers.
- Start up the guestbook frontend.
- Expose and view the Frontend Service.
- Clean up

### Lab Steps
#### Deploy Redis Master

Create a Redis master deployment and service.
Expose the master service for follower replication and frontend access.

#### Deploy Redis Follower

Create Redis follower deployment that replicate data from the master.
Connect the follower pods to the master using replication settings.

#### Deploy the Frontend Application

Create a simple web application that connects to Redis.
The app will send write requests to the master and read requests to the follower.

#### Test the Deployment

Access the frontend application and interact with Redis.
Verify replication by checking if writes to the master appear in the follower.

### Expected Outcome
By the end of this lab, you will have a functional Redis replication setup with a frontend interacting with it. This setup is commonly used to improve scalability and performance in real-world applications.

