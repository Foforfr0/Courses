# OTP

Source: [**OTP System Design**](https://www.instagram.com/p/DZICSXUEn-e)

Tags: **[]**

# Index

- [**What is?**](#whats-it)
- [**Main applications**](#main-applications)
- [**Advantages**](#advantages)
- [**Disadvantages**](#disadvantages)
- [**Inner function**](#inner-function)
  - [**Where does the OTP live?**](#where-does-the-otp-live)
  - [**Redis vs DB**](#redis-vs-db)
  - [**Workflows**](#workflows)

# What´s it?

# Main Applications

OTP's help verify users during:

- Sign up.
- Login.
- Password reset.
- Sensitive actions.

# Advantages

# Disadvantages

# Inner Function

## Where Does The OTP Live?

Is usually stored in:

- A dedicated OTP table.
- Redis with an expiration time (TTL).

Note:

- Never store the raw OTP.
- Store only a hashed version of the OTP.

## Redis vs DB

### Redis

- Extremely fast reads and writes.
- Built-in expiration (TTL).
- Automatically removes expired OTPs.
- Ideal for temporary data like OTPs.

### DB

- Is persistent.
- Require manual cleanup of expired OTPs.
- Useful for auditing and reporting.

## Workflows

### OTP Generation And Delivery

```mermaid
stateDiagram
    state "User request OTP
    Login/SignUp trigger" as s1
    state "Generate OTP
    6 digit, cryptorandom" as s2
    state "Send to user SMS/Email" as s3
    state "Hash & store
    Save hash and expiry" as s4
    s1 --> s2
    s2 --> s3
    s2 --> s4
```

### Verification Flow

```mermaid
flowchart TD
        A[User submits OTP]
        A --> B[Check expired?
                attempt_count >= limit]
        B --> C{Valid}
        C -->|No| D([Reject
                Increment attempt_count])
        C -->|Yes| E([Mark verified
                Set verified_at,
                is_verified])
        E --> F[issue session/JWT
                user is authenticated]
        F --> G[Delete OTP]
```

### Resend OTP

```mermaid
flowchart TD
    A[User click resend
    after cooldown expires]
    A --> B[Check cooldown,
            30-60 sec since last_sent_at]
    B --> C[Invalidate old OTP
            Delete/Overwrite hash]
    C --> D[Generate & send new,
            Fresh hash, new expiry]
```
