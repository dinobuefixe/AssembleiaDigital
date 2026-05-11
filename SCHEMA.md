# Collective Decision-Making Platform — Database Schema

## Overview

An application for creating and managing group decisions, where users can form groups, propose decisions with multiple options, and vote before a set deadline.

---

## Entities

### Accounts

Represents registered users on the platform.

| Field      | Type                | Constraints                 |
|------------|---------------------|-----------------------------|
| `id`       | INT                 | PRIMARY KEY, AUTO INCREMENT |
| `name`     | VARCHAR             | NOT NULL                    |
| `email`    | VARCHAR             | NOT NULL, UNIQUE            |
| `password` | VARCHAR             | NOT NULL, ENCRYPTED         |

---

### Groups

Groups users together to share decisions.

| Field  | Type    | Constraints                 |
|--------|---------|-----------------------------|
| `id`   | INT     | PRIMARY KEY, AUTO INCREMENT |
| `name` | VARCHAR | NOT NULL                    |

**Relationships:**
- A group has **many users** → junction table `GroupMembers`
- A group has **many decisions** → junction table `GroupDecisions`

---

### GroupMembers *(junction table)*

Associates users with groups (N-N relationship).

| Field      | Type | Constraints                           |
|------------|------|---------------------------------------|
| `group_id` | INT  | FOREIGN KEY → Groups(id), NOT NULL    |
| `user_id`  | INT  | FOREIGN KEY → Accounts(id), NOT NULL  |

> **Composite PRIMARY KEY:** (`group_id`, `user_id`)

---

### Decisions

Represents a decision to be made within a group.

| Field      | Type     | Constraints                           |
|------------|----------|---------------------------------------|
| `id`       | INT      | PRIMARY KEY, AUTO INCREMENT           |
| `title`    | VARCHAR  | NOT NULL                              |
| `deadline` | DATETIME | NOT NULL                              |
| `group_id` | INT      | FOREIGN KEY → Groups(id), NOT NULL    |

**Relationships:**
- A decision has **many options** → table `Options`
- A decision has **many votes** → table `Votes`

---

### Options

Represents the available options for a given decision.

| Field         | Type    | Constraints                               |
|---------------|---------|-------------------------------------------|
| `id`          | INT     | PRIMARY KEY, AUTO INCREMENT               |
| `label`       | VARCHAR | NOT NULL                                  |
| `decision_id` | INT     | FOREIGN KEY → Decisions(id), NOT NULL     |

---

### Votes

Records a user's vote on an option within a decision.

| Field       | Type | Constraints                           |
|-------------|------|---------------------------------------|
| `id`        | INT  | PRIMARY KEY, AUTO INCREMENT           |
| `user_id`   | INT  | FOREIGN KEY → Accounts(id), NOT NULL  |
| `option_id` | INT  | FOREIGN KEY → Options(id), NOT NULL   |

> **UNIQUE constraint:** (`user_id`, `option_id`) — each user can only vote once per decision.  
> This uniqueness must also be enforced at the application level by verifying that the options belong to the same decision.

---

### Friendships

Manages friend requests between users.

| Field      | Type                                  | Constraints                           |
|------------|---------------------------------------|---------------------------------------|
| `id`       | INT                                   | PRIMARY KEY, AUTO INCREMENT           |
| `user1_id` | INT                                   | FOREIGN KEY → Accounts(id), NOT NULL  |
| `user2_id` | INT                                   | FOREIGN KEY → Accounts(id), NOT NULL  |
| `status`   | ENUM(`pending`, `accepted`, `denied`) | NOT NULL, DEFAULT `pending`           |

> **UNIQUE constraint:** (`user1_id`, `user2_id`) — prevents duplicate requests.  
> By convention, `user1_id` should always be the user with the lower `id` to avoid inverse duplicate records (e.g., (1,2) and (2,1)).

---

## Relationship Diagram

```
Accounts ──< GroupMembers >── Groups ──< GroupDecisions
    │                                          │
    │                                     Decisions
    │                                      │       │
    └──< Votes >── Options >──────────────┘   (deadline)
    │
    └──< Friendships >── Accounts
```

---

## Implementation Notes

- **Vote uniqueness:** The `UNIQUE(user_id, option_id)` constraint on the `Votes` table prevents duplicate votes, but the business logic must also verify that the user has not already voted on another option within the same decision.
- **Deadline:** The application should block votes after the `deadline` of the corresponding decision.
- **Friendships:** The `status` field uses ENUM to ensure only valid values are stored. Denied requests (`denied`) can be kept for auditing purposes or removed depending on the business logic.
- **Passwords:** Must be stored using a secure hash (e.g., bcrypt) — never in plain text.