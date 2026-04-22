# Security Specification - CANTV System

## Data Invariants
1. An **Activity** must have a technician assigned and a valid date.
2. Only **Admins** can create or modify Technicians.
3. Only **Admins** or the **Owner** (if technicianId matches) can modify an Activity.
4. Users can only read their own **UserProfile** unless they are Admins.
5. **Notifications** are only readable by the target user.

## The "Dirty Dozen" Payloads (Attack Vectors)
1. **Role Escalation**: A technician tries to update their own role to 'admin'.
2. **Identity Spoofing**: A user tries to create an activity for another user.
3. **Invalid ID Poisoning**: Trying to create a document with a massive 1MB string as ID.
4. **Shadow Field Injection**: Adding a `verified: true` field to a document that shouldn't have it.
5. **PII Leak**: A technician trying to list all users' profiles.
6. **Immutable Field Tampering**: Trying to change the `createdAt` timestamp of a document.
7. **Type Poisoning**: Sending a string where a number (overtimeHours) is expected.
8. **Orphaned Writes**: Creating an activity with a technician ID that doesn't exist.
9. **Bulk Export Scraping**: Trying to list all activities without a where clause (if restricted).
10. **Terminal State Bypass**: Modifying an activity after it's been marked as 'deleted'.
11. **Email Spoofing**: Using an admin email with `email_verified: false`.
12. **Unbounded Array Attack**: Trying to inject 10,000 participants.

## The Test Runner (Plan)
We will use `@firebase/rules-unit-testing` logic (conceptually) to ensure:
- Unauthenticated access is denied everywhere.
- Non-verified emails are denied write access.
- Role-based access is strictly enforced.
