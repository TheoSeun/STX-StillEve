
# STX-StillEve - Event Attendance & Rewards Smart Contract

This Clarity smart contract enables organizers to create blockchain-based events, track attendance, verify participants, and issue rewards based on engagement.

---

## 📖 Table of Contents

* [Features](#features)
* [Data Structures](#data-structures)
* [Constants & Errors](#constants--errors)
* [Functions](#functions)

  * [Public Functions](#public-functions)
  * [Read-Only Functions](#read-only-functions)
* [Deployment Notes](#deployment-notes)
* [Future Improvements](#future-improvements)

---

## ✅ Features

* Event creation by contract owner
* Attendee check-in/check-out tracking
* Attendance duration calculation
* Verification by approved verifiers
* Reward tracking and claiming
* Strict validations for event parameters and attendance rules

---

## 📦 Data Structures

### `events` (map)

Holds metadata about created events.

```clojure
{ 
  name: string-ascii(50),
  description: string-ascii(200),
  start-height: uint,
  end-height: uint,
  base-reward: uint,
  bonus-reward: uint,
  min-attendance-duration: uint,
  organizer: principal,
  is-active: bool
}
```

### `event-attendance` (map)

Tracks attendee check-in and check-out.

```clojure
{
  check-in-height: uint,
  check-out-height: uint,
  duration: uint,
  verified: bool
}
```

### `verification-details` (map)

Stores who verified attendance and when.

```clojure
{
  verified-by: principal,
  verified-at: uint
}
```

### `rewards-claimed` (map)

Tracks if a user has claimed their reward.

```clojure
{
  amount: uint,
  claimed-at: uint,
  reward-tier: uint
}
```

### `verifiers` (map)

Authorized verifiers for attendance.

```clojure
principal → bool
```

---

## 🧱 Constants & Errors

### Validation Ranges

* `MIN-NAME-LENGTH`: 3
* `MAX-NAME-LENGTH`: 50
* `MIN-DESC-LENGTH`: 10
* `MAX-DESC-LENGTH`: 200
* `MIN-DURATION`: 144 (blocks)
* `MAX-DURATION`: 52560 (blocks)
* `MAX-REWARD`: 1,000,000,000,000

### Error Codes

| Constant                            | Description                         |
| ----------------------------------- | ----------------------------------- |
| `ERR-NOT-AUTHORIZED (u100)`         | Caller is not the contract owner    |
| `ERR-ALREADY-CLAIMED (u101)`        | Reward already claimed              |
| `ERR-EVENT-NOT-ENDED (u102)`        | Event hasn't ended yet              |
| `ERR-EVENT-ENDED (u103)`            | Event already ended                 |
| `ERR-NO-REWARD (u104)`              | No reward available                 |
| `ERR-EVENT-NOT-FOUND (u105)`        | Event not found                     |
| `ERR-INSUFFICIENT-FUNDS (u106)`     | Treasury doesn't have enough funds  |
| `ERR-INVALID-DURATION (u107)`       | Duration is invalid                 |
| `ERR-ALREADY-REGISTERED (u108)`     | Attendee already checked in         |
| `ERR-INVALID-START-HEIGHT (u110)`   | Start height is in the past         |
| `ERR-INVALID-REWARD (u111)`         | Reward value is invalid             |
| `ERR-INVALID-MIN-ATTENDANCE (u112)` | Minimum attendance duration invalid |
| `ERR-INVALID-NAME (u2000)`          | Event name invalid                  |
| `ERR-INVALID-DESCRIPTION (u2001)`   | Event description invalid           |

---

## 🔧 Functions

### 🟩 Public Functions

#### `(create-event name description start-height duration base-reward bonus-reward min-attendance)`

Creates a new event.

* Only the contract owner can call this.
* Validates name, description, start height, reward amounts, and attendance.

Returns: `ok event-id` or error

---

#### `(check-in event-id)`

Allows an attendee to check into an event.

* Must not be registered already.
* Current block must be within event window.

Returns: `ok true` or error

---

#### `(check-out event-id)`

Allows an attendee to check out from an event.

* Must have checked in before.
* Computes the duration based on block height.

Returns: `ok duration` or error

---

### 🟨 Read-Only Functions

#### `(get-owner)`

Returns the contract owner's principal.

---

#### `(get-event event-id)`

Fetches the metadata for a given event ID.

---

#### `(get-attendance-record event-id attendee)`

Gets check-in/out and duration for an attendee in a given event.

---

#### `(get-reward-claim event-id attendee)`

Returns reward claim status for a specific event and user.

---

#### `(is-verifier address)`

Checks if a given address is an authorized verifier.

---

#### `(event-exists event-id)`

Returns `true` if the event exists.

---
