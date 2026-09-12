# Transactional Emails

## 1. What Are Transactional Emails?

Transactional emails are automated emails sent to a user because of a specific action or event in an application.

Examples:

- User signs up → Welcome/verification email
- User forgets password → Password reset email
- User places an order → Order confirmation email
- Payment succeeds → Payment confirmation email
- Order is shipped → Shipping notification
- Account security changes → Security alert

Simple mental model:

    User Action
        ↓
    Backend
        ↓
    Business Logic
        ↓
    Email Service
        ↓
    User's Inbox

---

# 2. Transactional vs Marketing Emails

### Transactional Email

Triggered by a user's action or system event.

Examples:

    Password reset
    Email verification
    Order confirmation
    Payment receipt
    OTP

Purpose:

    Provide important information related to the user's activity.

### Marketing Email

Sent mainly for promotion or engagement.

Examples:

    Discounts
    Product promotions
    Newsletters
    New product announcements

Purpose:

    Marketing / sales / engagement

Important:

    Transactional emails are generally event-driven,
    while marketing emails are campaign-driven.

---

# 3. Real-World Example

Suppose a user buys an iPhone.

Flow:

    User
      ↓
    POST /orders
      ↓
    Backend
      ↓
    Validate order
      ↓
    Create order
      ↓
    Payment successful
      ↓
    Send order confirmation email
      ↓
    User receives email

Example email:

    Subject:
    Order #1234 confirmed

    Hi Lokendra,

    Your order has been confirmed.

    Product: iPhone 15
    Amount: ₹50,000

    Thank you for your order.

---

# 4. Why Do We Need Transactional Emails?

They are used to:

- Confirm user actions
- Verify accounts
- Reset passwords
- Notify users about important events
- Send receipts
- Notify about order status
- Improve security
- Keep users informed

---

# 5. Common Transactional Emails

### Authentication

    Email verification
    Password reset
    OTP
    Login alert
    Password changed

### E-commerce

    Order confirmation
    Payment confirmation
    Shipment notification
    Delivery notification
    Cancellation confirmation
    Refund confirmation

### SaaS

    Account invitation
    Subscription confirmation
    Subscription renewal
    Trial expiration
    Invoice

---

# 6. Basic Email Architecture

A beginner implementation might look like:

    Client
       ↓
    Backend API
       ↓
    Email Service
       ↓
    SMTP / Email Provider
       ↓
    User Inbox

Example:

    POST /register
        ↓
    Create user
        ↓
    Generate verification token
        ↓
    Send email
        ↓
    Return response

But in production, we usually should not make the API wait for the email to be delivered.

---

# 7. Why Sending Email Directly From the Request Is a Problem

Consider:

    POST /orders

Backend:

    Create order
        ↓
    Send email
        ↓
    Wait for email provider
        ↓
    Send API response

If email sending takes 2 seconds:

    API response = delayed

If email provider is temporarily unavailable:

    API request may fail

This is not ideal.

Better architecture:

    POST /orders
        ↓
    Create order
        ↓
    Queue email job
        ↓
    Return API response

Then:

    Email Worker
        ↓
    Email Provider
        ↓
    User Inbox

This is where queues become very useful.

---

# 8. Transactional Email With a Queue

Production-style flow:

    User
      ↓
    API
      ↓
    Business Logic
      ↓
    Database
      ↓
    Email Queue
      ↓
    Email Worker
      ↓
    Email Provider
      ↓
    User

Example:

    Order created
        ↓
    Add "send order confirmation" job
        ↓
    API returns success
        ↓
    Worker processes job
        ↓
    Email sent

The user does not have to wait for the email provider.

---

# 9. Why Use a Queue?

Benefits:

- Faster API response
- Retry failed emails
- Handle traffic spikes
- Prevent email provider failures from blocking requests
- Process emails asynchronously
- Control email sending rate

Example:

    100,000 orders
        ↓
    100,000 email jobs
        ↓
    Queue
        ↓
    Workers process jobs gradually

---

# 10. Email Provider

Your backend usually does not directly deliver email to Gmail/Outlook/etc.

Instead, you use an email delivery provider.

Common examples include:

    Amazon SES
    SendGrid
    Mailgun
    Postmark
    Resend

The provider handles much of the email delivery infrastructure.

Architecture:

    Your Backend
         ↓
    Email Provider API / SMTP
         ↓
    Recipient Mail Server
         ↓
    User Inbox

---

# 11. SMTP

SMTP stands for:

    Simple Mail Transfer Protocol

It is a protocol used for sending email.

Conceptually:

    Your Application
         ↓
       SMTP
         ↓
    Mail Server
         ↓
    Recipient

Your application can communicate with an SMTP server to send emails.

---

# 12. SMTP vs Email API

There are two common approaches.

### SMTP

Your application connects to an SMTP server.

Example concept:

    Node.js
       ↓
    SMTP Server
       ↓
    Recipient

### Email API

Your application makes an HTTP request to an email provider.

Example:

    Node.js
       ↓
    POST /send-email
       ↓
    Email Provider
       ↓
    Recipient

Modern applications often use provider APIs because they provide useful features such as delivery tracking, templates, logs, and analytics.

---

# 13. Nodemailer

In Node.js applications, Nodemailer is commonly used to send emails.

It can work with SMTP providers.

Basic concept:

    Node.js
       ↓
    Nodemailer
       ↓
    SMTP
       ↓
    Email Server

Example:

    import nodemailer from "nodemailer";

    const transporter = nodemailer.createTransport({
        host: process.env.SMTP_HOST,
        port: 587,
        secure: false,
        auth: {
            user: process.env.SMTP_USER,
            pass: process.env.SMTP_PASSWORD
        }
    });

    await transporter.sendMail({
        from: process.env.EMAIL_FROM,
        to: user.email,
        subject: "Verify your email",
        html: "<h1>Verify your account</h1>"
    });

Important:

    Never hardcode SMTP credentials.

Use environment variables.

---

# 14. Email Service Layer

Don't put email-sending code directly inside controllers.

Bad structure:

    Controller
       ↓
    Database
       ↓
    Nodemailer
       ↓
    Email

Better:

    Controller
       ↓
    Service
       ↓
    Email Service
       ↓
    Provider

Example:

    orderController
        ↓
    orderService
        ↓
    emailService
        ↓
    email provider

This keeps responsibilities separated.

---

# 15. Email Service

Create a dedicated email service.

Conceptually:

    sendVerificationEmail(user)
    sendPasswordResetEmail(user)
    sendOrderConfirmationEmail(order)
    sendPaymentConfirmationEmail(payment)

Then your business logic can call:

    emailService.sendOrderConfirmation(order)

instead of knowing how SMTP/provider communication works.

---

# 16. Email Templates

Don't build large HTML strings inside controllers.

Instead, maintain reusable templates.

Example structure:

    emails/
        templates/
            verification.html
            password-reset.html
            order-confirmation.html
            payment-success.html

Then:

    Email Service
        ↓
    Select template
        ↓
    Insert dynamic data
        ↓
    Send email

---

# 17. Dynamic Email Templates

Suppose:

    user.name = Lokendra
    order.id = 1234
    order.total = ₹50,000

Template:

    Hi {{name}},

    Your order {{orderId}} has been confirmed.

    Total: {{total}}

After rendering:

    Hi Lokendra,

    Your order 1234 has been confirmed.

    Total: ₹50,000

The template remains reusable.

---

# 18. Email Template Engines

For more complex applications, you can use template engines.

Examples:

    Handlebars
    EJS
    React Email
    MJML

Concept:

    Template
       +
    Data
       ↓
    Rendered HTML
       ↓
    Email Provider

---

# 19. Verification Email Flow

Suppose a user registers.

    POST /register
        ↓
    Validate input
        ↓
    Create user
        ↓
    Generate verification token
        ↓
    Store token / expiration
        ↓
    Add email job to queue
        ↓
    Return response

Worker:

    Get email job
        ↓
    Generate email
        ↓
    Send email
        ↓
    Mark job completed

User:

    Click verification link
        ↓
    Backend verifies token
        ↓
    Account verified

---

# 20. Password Reset Email Flow

Flow:

    User clicks "Forgot Password"
        ↓
    POST /forgot-password
        ↓
    Generate secure reset token
        ↓
    Store hashed token + expiration
        ↓
    Queue reset email
        ↓
    Worker sends email
        ↓
    User clicks reset link
        ↓
    Backend verifies token
        ↓
    User sets new password

Important:

    Never send the user's existing password by email.

---

# 21. Email Tokens

For verification/reset links, generate a secure random token.

Conceptually:

    Generate random token
        ↓
    Store token securely
        ↓
    Send token/link to user

Example link:

    https://example.com/reset-password?token=abc123

The token should:

- Be unpredictable
- Have an expiration time
- Be single-use where appropriate
- Be securely stored

For sensitive reset tokens, storing a hash of the token rather than the raw token is a stronger design.

---

# 22. OTP Emails

Transactional email can also be used for OTPs.

Example:

    User requests login OTP
        ↓
    Generate OTP
        ↓
    Store hashed OTP + expiration
        ↓
    Queue email
        ↓
    Send OTP
        ↓
    User enters OTP
        ↓
    Backend verifies OTP

Typical requirements:

    Short expiration
    Limited attempts
    Rate limiting
    One-time use

---

# 23. Email Queue

Suppose we use a queue system.

Example:

    Order Service
        ↓
    emailQueue.add({
        type: "ORDER_CONFIRMATION",
        orderId: "1234"
    })

Worker:

    emailWorker
        ↓
    Receive job
        ↓
    Fetch order
        ↓
    Render template
        ↓
    Send email

This is much more scalable than sending everything inside the HTTP request.

---

# 24. Retry Mechanism

Email delivery can fail.

Example:

    Worker
       ↓
    Email Provider
       ↓
    Network Error

Instead of immediately giving up:

    Attempt 1 → Failed
    Attempt 2 → Failed
    Attempt 3 → Success

A queue system can support retries with delays.

Example:

    Retry after:
    10 seconds
    30 seconds
    2 minutes

The exact strategy depends on the provider and application.

---

# 25. Exponential Backoff

A common retry strategy is exponential backoff.

Conceptually:

    Attempt 1 → wait 1 second
    Attempt 2 → wait 2 seconds
    Attempt 3 → wait 4 seconds
    Attempt 4 → wait 8 seconds

Usually a maximum delay is applied.

This avoids repeatedly hitting a temporarily unavailable service.

---

# 26. Dead-Letter Queue

What if an email fails after all retries?

Instead of repeatedly retrying forever:

    Email Job
        ↓
    Retry
        ↓
    Retry
        ↓
    Retry
        ↓
    Still failed
        ↓
    Dead-Letter Queue

A dead-letter queue stores failed jobs for investigation or later processing.

---

# 27. Idempotency

Email jobs can sometimes be processed more than once.

Example:

    Worker sends email
        ↓
    Provider succeeds
        ↓
    Worker crashes before marking job complete
        ↓
    Queue retries job
        ↓
    Same email sent again

This can result in duplicate emails.

For important transactional workflows, design for idempotency.

Possible approach:

    emailEventId = "order-confirmation:1234"

Store/send-tracking information so the same logical email is not unintentionally sent multiple times.

---

# 28. Email Delivery Status

A provider may provide events such as:

    Accepted
    Delivered
    Bounced
    Failed
    Deferred
    Opened
    Clicked

For transactional systems, delivery/bounce information can be useful for monitoring and user communication.

---

# 29. Webhooks for Email Providers

Email providers can send events back to your application through webhooks.

Flow:

    Your Backend
        ↓
    Email Provider
        ↓
    User

Later:

    Email Provider
        ↓
    POST /webhooks/email
        ↓
    Your Backend

Example event:

    {
        type: "email.delivered",
        messageId: "abc123"
    }

Your backend can update:

    Email status = delivered

This connects transactional emails with the webhook concept.

---

# 30. Email Delivery vs Email Sent

These are not always the same thing.

    Application → Provider
                 = accepted/sent

does not necessarily mean:

    Recipient → Inbox
                 = delivered

The provider may later report:

    delivered
    bounced
    rejected
    deferred

So production systems should distinguish between these states.

---

# 31. Email Bounce

A bounce means the email could not be delivered.

Types can include:

### Hard bounce

Usually a permanent failure.

Example:

    Invalid email address

### Soft bounce

Usually a temporary failure.

Example:

    Recipient mailbox temporarily unavailable

Your email provider may classify and report these differently.

---

# 32. Email Security

Important security considerations:

- Never expose SMTP/API credentials
- Use environment variables/secrets management
- Validate email addresses
- Protect reset links
- Use HTTPS
- Use short-lived tokens
- Rate-limit OTP/reset requests
- Prevent email enumeration where appropriate
- Authenticate provider webhooks
- Avoid putting sensitive information in email
- Avoid logging sensitive tokens

---

# 33. SPF

SPF stands for:

    Sender Policy Framework

It helps specify which servers are authorized to send email for a domain.

Conceptually:

    example.com
        ↓
    DNS SPF record
        ↓
    Authorized email servers

SPF helps protect against unauthorized sending using your domain.

---

# 34. DKIM

DKIM stands for:

    DomainKeys Identified Mail

It adds a cryptographic signature to outgoing emails.

The receiving mail server can verify the signature using a public key published in DNS.

Conceptually:

    Email
      ↓
    DKIM signature
      ↓
    Recipient server
      ↓
    Verify signature

---

# 35. DMARC

DMARC stands for:

    Domain-based Message Authentication, Reporting,
    and Conformance

It builds on email authentication mechanisms such as SPF and DKIM.

It allows domain owners to specify policies for messages that fail authentication checks and provides reporting mechanisms.

Simple mental model:

    SPF → Who can send?

    DKIM → Was the email cryptographically signed?

    DMARC → What should receivers do when authentication fails?

---

# 36. Why SPF, DKIM and DMARC Matter

They help improve:

- Email authentication
- Domain reputation
- Protection against spoofing
- Deliverability

For production applications sending email from your own domain, proper email-domain configuration is important.

---

# 37. Email Deliverability

Deliverability means the ability of emails to successfully reach recipients' mailboxes.

Problems can occur because of:

- Poor sender reputation
- Invalid recipients
- Spam complaints
- Incorrect DNS configuration
- Missing authentication
- High bounce rates
- Suspicious content
- Provider restrictions

Sending an email successfully to an email provider does not guarantee inbox placement.

---

# 38. Transactional Email Architecture

A production-style architecture:

    Client
       ↓
    API
       ↓
    Controller
       ↓
    Business Service
       ↓
    Database
       ↓
    Queue
       ↓
    Email Worker
       ↓
    Email Provider
       ↓
    Recipient

Provider events:

    Email Provider
       ↓
    Webhook
       ↓
    Backend
       ↓
    Email Status

---

# 39. Example: E-Commerce Order

User places order:

    POST /orders
        ↓
    Controller
        ↓
    Order Service
        ↓
    Validate business rules
        ↓
    Create order in database
        ↓
    Add email job
        ↓
    Return order response

Worker:

    Email Queue
        ↓
    Worker
        ↓
    Get order details
        ↓
    Render order template
        ↓
    Email Provider
        ↓
    User

This separates:

    Order creation
    Email delivery

---

# 40. Why Queue Email Instead of Calling Email Provider Directly?

### Direct approach

    API
     ↓
    Email Provider
     ↓
    Response

Problems:

- Slower API
- Provider failure affects request
- Traffic spikes can overwhelm provider
- Difficult retry handling

### Queue approach

    API
     ↓
    Queue
     ↓
    Response

    Worker
     ↓
    Email Provider

Advantages:

- Faster API
- Automatic retries
- Better scalability
- Better failure isolation

---

# 41. Email Service Responsibilities

An Email Service can handle:

    Template selection
    Template rendering
    Provider communication
    Email metadata
    Provider response handling
    Logging
    Error handling

Business services should mainly say:

    sendOrderConfirmation(order)

rather than knowing SMTP details.

---

# 42. Example Project Structure

    src/
    ├── controllers/
    │   └── order.controller.js
    │
    ├── services/
    │   ├── order.service.js
    │   └── email.service.js
    │
    ├── workers/
    │   └── email.worker.js
    │
    ├── queues/
    │   └── email.queue.js
    │
    ├── templates/
    │   ├── verification.html
    │   ├── password-reset.html
    │   └── order-confirmation.html
    │
    ├── models/
    │   └── order.model.js
    │
    └── config/
        └── email.js

---

# 43. Transactional Email Flow With BLL

Since email is often triggered by business events:

    Controller
        ↓
    Order Service
        ↓
    Business rules
        ↓
    Create order
        ↓
    Publish email job
        ↓
    Email Worker
        ↓
    Email Service
        ↓
    Provider

The business service decides:

    "An order was successfully created,
     so an order confirmation should be sent."

The Email Service decides:

    "How should this email actually be sent?"

This is a good separation of responsibilities.

---

# 44. Important Design Principle

Don't make business logic depend heavily on a specific email provider.

Bad:

    Order Service
        ↓
    SendGrid-specific code

Better:

    Order Service
        ↓
    Email Service Interface
        ↓
    Provider Implementation

Then you can potentially change:

    Provider A → Provider B

without rewriting the entire order system.

---

# 45. Handling Provider Failure

Suppose:

    Order successfully created
        ↓
    Email provider unavailable

The order should not necessarily be rolled back just because the email failed.

Instead:

    Order = SUCCESS
    Email Job = RETRY

This is an important distributed-system concept.

The order and email are separate operations.

---

# 46. Database Transaction vs Email

Be careful with:

    Database transaction
        ↓
    Send email
        ↓
    Commit

External email providers usually don't participate in your database transaction.

You cannot assume:

    DB commit
    +
    Email delivery

are one atomic transaction.

A common production pattern is to use an outbox/event mechanism.

---

# 47. Transactional Outbox Pattern

Suppose:

    Create order
    Add email event

Both should be recorded reliably.

Conceptually:

    Database Transaction
       ├── Create Order
       └── Create Outbox Event

After commit:

    Outbox Worker
        ↓
    Email Queue
        ↓
    Email Worker
        ↓
    Email Provider

This reduces the risk of:

    Order saved
    BUT email job never created

The outbox pattern is especially useful in reliable event-driven systems.

---

# 48. Important Terms to Know

    Transactional Email
    SMTP
    Email API
    Email Provider
    Nodemailer
    Email Template
    Email Service
    Queue
    Worker
    Retry
    Exponential Backoff
    Dead-Letter Queue
    Idempotency
    Webhook
    Bounce
    Deliverability
    SPF
    DKIM
    DMARC
    Outbox Pattern

---

# 49. Interview Question

### What is a transactional email?

Answer:

    A transactional email is an automated email triggered by a
    specific user action or system event, such as email
    verification, password reset, order confirmation, or
    payment confirmation.

---

# 50. Interview Question

### How would you design a scalable email system?

Answer:

    I would avoid sending emails directly inside the HTTP request.
    Instead, after the business operation succeeds, I would create
    an email job in a durable queue.

    A background worker would consume the job and send the email
    through an email provider.

    I would add retries with exponential backoff, dead-letter
    handling, idempotency, logging, monitoring, and provider
    webhooks for delivery status.

---

# 51. Interview Question

### Why use a queue for emails?

Answer:

    Email sending is an external and potentially slow operation.
    Using a queue makes it asynchronous, keeps API responses fast,
    isolates provider failures, and allows retries and scalable
    background processing.

---

# 52. Interview Question

### What happens if the email provider is down?

Answer:

    The API should not necessarily fail if the email is a
    non-critical side effect. I would keep the email job in a
    durable queue and retry it using exponential backoff.
    Permanently failed jobs can be moved to a dead-letter queue.

---

# 53. Interview Question

### What is the difference between SMTP and an Email API?

Answer:

    SMTP is a standard protocol used for transferring email,
    while an Email API allows an application to communicate with
    an email provider through HTTP APIs.

---

# 54. Interview Question

### Why shouldn't email logic be inside the controller?

Answer:

    Controllers should handle HTTP-related responsibilities.
    Email delivery is a separate infrastructure concern, so it is
    better to place it in an email service and trigger it through
    business services or asynchronous jobs.

---

# 55. Interview Question

### How would you prevent duplicate emails?

Answer:

    I would design the email job to be idempotent by assigning a
    unique event or idempotency key to the logical email and
    tracking its processing state so retries don't unintentionally
    send duplicate messages.

---

# 56. Interview Question

### What are SPF, DKIM and DMARC?

Answer:

    SPF identifies authorized sending servers for a domain.
    DKIM provides cryptographic signing for outgoing emails.
    DMARC defines how receiving servers should handle messages
    that fail authentication checks and provides reporting.

---

# 57. Interview Scenario

### User places an order, but email sending fails. Should the order fail?

Usually:

    Order creation = successful
    Email = asynchronous side effect

So:

    Order saved
        ↓
    Email job queued
        ↓
    Email fails
        ↓
    Retry

We generally should not tell the user:

    "Your order failed"

just because the confirmation email provider temporarily failed.

The exact behavior depends on business requirements.

---

# 58. Quick Revision

    Transactional Email
        ↓
    Triggered by user/system event
        ↓
    Examples:
        Verification
        Password reset
        Order confirmation
        Payment confirmation
        Shipping notification

Production architecture:

    API
      ↓
    Business Logic
      ↓
    Database
      ↓
    Queue
      ↓
    Email Worker
      ↓
    Email Provider
      ↓
    User

Important production concepts:

    Async processing
    Queue
    Worker
    Retry
    Exponential Backoff
    Dead-Letter Queue
    Idempotency
    Email Templates
    SMTP
    Email APIs
    Webhooks
    SPF
    DKIM
    DMARC
    Deliverability
    Outbox Pattern

---

# 59. One-Line Interview Summary

Transactional emails are event-driven emails such as verification, password-reset, order, and payment notifications; in production they are commonly handled asynchronously using a queue, worker, email provider, retries, idempotency, and delivery webhooks.