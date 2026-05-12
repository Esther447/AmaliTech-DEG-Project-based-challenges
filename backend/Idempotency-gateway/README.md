Idempotency Gateway (The "Pay-Once" Protocol)

This project was built as part of the AmaliTech backend challenge. It is a simple REST API that simulates a payment system and ensures that a payment is processed only once, even if the same request is sent multiple times due to retries or network issues.

Author

Esther

1. Project Overview

This application simulates a payment processing system.

The main goal is to prevent users from being charged twice when they retry a request using the same Idempotency-Key.

The system ensures that repeated requests with the same key return the same response without re-processing the payment.

2. How It Works
A client sends a POST request to /process-payment
The request must include an Idempotency-Key in the headers
The server checks if the key already exists:
If it does not exist, the payment is processed and the result is stored
If it exists, the stored response is returned immediately
If the same key is used with different data, the request is rejected
3. Architecture Flow

Client sends request
→ Server receives request
→ Check Idempotency-Key
→ If key is new:
→ Process payment
→ Store response
→ If key exists:
→ Return cached response
→ If key exists with different data:
→ Return error

4. Setup Instructions

Clone the repository:

git clone https://github.com/Esther447/AmaliTech-DEG-Project-based-challenges.git    backend/Idempotency-gateway/idempotency-api

Navigate to the project folder:

cd backend/Idempotency-gateway/idempotency-api

Install dependencies:

npm install

Start the server:

npm start

The server runs on:

http://localhost:3000

5. API Documentation
Endpoint

POST /process-payment

Headers

Content-Type: application/json
Idempotency-Key: unique-key

Request Body
{
  "amount": 100,
  "currency": "GHS"
}
Success Response (Status: 201 Created)
```
{
  "message": "Charged 100 GHS"
}
```
Cached Response (Duplicate Request)

If the same Idempotency-Key is used again, the same response is returned with status 201.

Header:

X-Cache-Hit: true

Error Response (Same Key, Different Data)
```
{
  "error": "Idempotency key already used with different request data"
}
```

Status Code:

409 Conflict

Error Response (Missing Idempotency-Key header)
```
{
  "error": "Missing required header: Idempotency-Key"
}
```

Status Code:

400 Bad Request

Error Response (Request Still Processing)
```
{
  "error": "Request is already being processed. Try again shortly."
}
```

Status Code:

429 Too Many Requests

6. Design Decisions

I used an in-memory JavaScript object to store idempotency keys and their responses.

Each key stores:

request body
response
status

Before processing a payment, the system checks if the key already exists.

If a request is still being processed, any duplicate request immediately receives a `429 Too Many Requests` response. Once the first request completes, subsequent duplicates with the same key and body receive the cached response. This prevents double-processing and ensures consistent results.

7. Bonus Feature

I added a custom response header called X-Cache-Hit.

This helps identify when a response is returned from cache instead of being processed again.

It improves debugging and makes system behavior clearer.
