# Event API – FastAPI Project

This is a REST API built using **FastAPI**. It allows users to create, view, and manage events, with the added functionality of simulating notifications when an event is about to start using a **background worker**.

---

## Features

- Add new events with title, description, and datetime  
- List all existing events  
- Retrieve a specific event by its ID  
- Simulated notifications in the background: if an event is scheduled to begin within the next 5 minutes, a message is printed to the console automatically  
- MVC-like structure for clarity and maintainability (`models/`, `services/`, `controllers/`)  
- In-memory data storage using Python dictionary  
- Timezone-aware datetime validation with Pydantic  
- Unit tests with `pytest` and FastAPI’s `TestClient`  
- Real-time event checks using FastAPI’s `lifespan` and async background loop  

---

## How to Set Up & Run

### 1. **Clone the project**

```bash
git clone https://github.com/AydaAzadegan/event-api.git
cd event-api
```


### 3. **Install dependencies**

```bash
pip install -r requirements.txt
```

### 4. **Run the app**

```bash
uvicorn main:app --reload
```

Visit: [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) to interact with the API.

---

## Swagger UI

FastAPI provides an interactive Swagger UI at:

```
http://127.0.0.1:8000/docs
```

You can test:
- `POST /events` – Create an event  
- `GET /events` – List all events  
- `GET /events/{event_id}` – Get one by ID  
If any event starts in ≤ 5 mins, a console message will be printed.

---

## Run the Tests

```bash
pytest
```

Tests include:
- Creating events
- Listing all events
- Handling non-existent event ID (404)
- Triggering notification for soon-starting events
- Checking if background notification logic works directly (unit-level)

  
You should see something like this if everything works:

```bash
tests/test_events.py .... [100%]
5 passed in 0.7s
```
---

## Limitations (If scaled to more than 10,000 users)

### 1. In-Memory Data Storage 

Currently, all event data is stored in a **Python dictionary** (`events_db`). This means:
- **All data is lost** when the server restarts.
- It cannot support horizontal scaling (e.g. multiple servers) because memory isn’t shared.
- There’s no long-term data retention, backups, or query optimization.

 **Solution**: Migrate to a **persistent database** like PostgreSQL.

---

### 2. Background Notifications Are Console-Only

Now, notifications are handled by a background worker that checks every 60 seconds if any event is about to start (within 5 minutes). This is a better from, when notifications are only triggered by API calls.
However, this setup still has these limitations:
- Notifications are printed to the server console, not delivered to users.
- There is no integration with real notification services like email, SMS, or push notifications.

 **Solution**: Use a background task queue like **Celery** with **Redis**, or FastAPI’s `BackgroundTasks`, to deliver actual alerts to users.

---

### 3. No Fault Tolerance or Persistence on Restart

Every time the app restarts:
- All created events are gone.
- There is no backup or caching system to recover previous data.

This makes the system **unsuitable for production** or even internal tools.

 **Solution**: Persist data using a real DB and add proper error handling and logging mechanisms.

---

While this project is great for showcasing **FastAPI fundamentals**, testing, and modular design, it requires significant upgrades for real-world scalability.

---

## If I had more time, I would:

- Integrate a real database (like PostgreSQL) for data persistence and better query support.

- Implement email/SMS notifications using tools like Celery or external services (e.g., Twilio, SendGrid).

- Add Docker support for containerization and portability.

- Provide OpenAPI documentation using FastAPI's built-in support.
  
---


## Folder Structure

```
.
├── controllers/        # Route handlers
├── models/             # Pydantic schemas
├── services/           # Business logic
├── tasks/              # Background worker (notification checker)
├── tests/              # Test cases
├── main.py             # FastAPI entry point
├── requirements.txt    # Dependencies
├── README.md           # Project info
└── .gitignore          # Ignored files
```


