# Lab 03: University Enrollment DDD

**Name:** [Your Full Name]
**Index Number:** [Your Index Number]

## Objective
Model the **CourseEnrollment** aggregate root, enforce invariant limits, and emit domain events, using Domain-Driven Design (DDD).

---

## 1. Business Context
A university lets students enrol in courses each semester. The registrar's office must make sure students do not register for too many credits, do not enrol twice in the same course, and do not enrol in a course that is already full.

## 2. Bounded Context and Ubiquitous Language

**Bounded Context:** Enrollment Context

| Term | Meaning in this context |
|---|---|
| Student | A person registered at the university who can enrol in courses |
| Course | A unit of study offered in a semester, with a credit value and a capacity |
| Enrollment | A student's registration in one course for a given semester |
| Credit Load | The total credit hours a student is registered for in a semester |
| Capacity | The maximum number of students allowed in a course |

Other contexts such as Billing (fees) and Academic Records (grades) are outside this model and would communicate with it through domain events.

## 3. Subdomain Classification

| Subdomain | Type | Strategy |
|---|---|---|
| Course Enrollment | Core | Build in-house with the strongest engineers |
| Timetable Scheduling | Supporting | Build simply or outsource |
| Authentication and Notifications | Generic | Buy a commercial service or use a standard tool |

---

## 4. Tactical Design

### Aggregate Root: `CourseEnrollment`
The single entry point for every change to a student's enrollments in one semester. Nothing outside the aggregate may change its internal parts directly.

### Entities
- **CourseEnrollment** (root): identified by `EnrollmentId`; holds the student's registrations for a semester.
- **EnrolledCourse** (child entity): identified by `CourseId` within the aggregate; represents one course the student is registered in.

### Value Objects (immutable, no identity)
- `StudentId`
- `CourseId`
- `Semester` (for example: 2026, Semester 1)
- `CreditHours` (a whole number between 1 and 6)
- `EnrollmentStatus` (Active, Dropped)

### Aggregate Structure
```
CourseEnrollment (Aggregate Root)
 |-- EnrollmentId
 |-- StudentId (value object)
 |-- Semester (value object)
 |-- EnrolledCourse[] (child entities)
 |     |-- CourseId (value object)
 |     |-- CreditHours (value object)
 |     |-- EnrollmentStatus (value object)
```

---

## 5. Invariants (Business Rules the Aggregate Must Always Protect)

| # | Invariant | What happens if broken |
|---|---|---|
| 1 | A student's total credit hours in a semester must not exceed **18** | Enrollment rejected |
| 2 | A student cannot enrol in the **same course twice** in the same semester | Enrollment rejected |
| 3 | A student cannot enrol in a course that has **reached its capacity** | Enrollment rejected |
| 4 | A student cannot **drop a course after the drop deadline** | Drop rejected |
| 5 | A student can only enrol in a course if the **prerequisites are met** | Enrollment rejected |

Invariants 1, 2 and 4 live inside the aggregate. Invariants 3 and 5 need information from other aggregates (course capacity and academic history), so the application service passes that information in as values (such as `seatsAvailable` and `prerequisitesMet`) when it calls the aggregate.

---

## 6. Domain Events
Immutable records of things that have already happened.

| Event | Triggered when | Data carried |
|---|---|---|
| `StudentEnrolledInCourse` | A student is successfully enrolled | enrollmentId, studentId, courseId, semester, occurredAt |
| `EnrollmentRejected` | An invariant blocks the request | studentId, courseId, reason, occurredAt |
| `StudentDroppedCourse` | A student successfully drops a course | enrollmentId, studentId, courseId, occurredAt |
| `CreditLimitReached` | A student's credit load reaches 18 | studentId, semester, occurredAt |

**Who listens:**
- The Billing Context listens to `StudentEnrolledInCourse` to add fees.
- The Notification service listens to all events to send emails or SMS.
- The Course Capacity service listens to enrolment and drop events to update seat counts.

---

## 7. Repository
```
interface CourseEnrollmentRepository {
    findByStudentAndSemester(studentId, semester): CourseEnrollment
    save(courseEnrollment): void
}
```
The repository works only with the aggregate root, hides the database from the domain model, and saves the whole aggregate in one transaction.

---

## 8. Sample Code (Python)

```python
from dataclasses import dataclass, field
from datetime import datetime

MAX_CREDITS = 18

@dataclass(frozen=True)
class CourseId:
    value: str

@dataclass(frozen=True)
class CreditHours:
    value: int
    def __post_init__(self):
        if not 1 <= self.value <= 6:
            raise ValueError("Credit hours must be between 1 and 6")

@dataclass(frozen=True)
class StudentEnrolledInCourse:
    student_id: str
    course_id: str
    semester: str
    occurred_at: datetime = field(default_factory=datetime.utcnow)

@dataclass(frozen=True)
class StudentDroppedCourse:
    student_id: str
    course_id: str
    occurred_at: datetime = field(default_factory=datetime.utcnow)

class EnrollmentError(Exception):
    pass

class CourseEnrollment:  # Aggregate Root
    def __init__(self, enrollment_id, student_id, semester):
        self.enrollment_id = enrollment_id
        self.student_id = student_id
        self.semester = semester
        self._courses = {}     # CourseId -> CreditHours
        self._events = []

    def total_credits(self):
        return sum(c.value for c in self._courses.values())

    def enroll(self, course_id: CourseId, credits: CreditHours,
               seats_available: int, prerequisites_met: bool):
        if course_id in self._courses:
            raise EnrollmentError("Already enrolled in this course")
        if seats_available <= 0:
            raise EnrollmentError("Course is full")
        if not prerequisites_met:
            raise EnrollmentError("Prerequisites not met")
        if self.total_credits() + credits.value > MAX_CREDITS:
            raise EnrollmentError("Credit limit of 18 exceeded")

        self._courses[course_id] = credits
        self._events.append(
            StudentEnrolledInCourse(self.student_id, course_id.value, self.semester)
        )

    def drop(self, course_id: CourseId, before_deadline: bool):
        if course_id not in self._courses:
            raise EnrollmentError("Not enrolled in this course")
        if not before_deadline:
            raise EnrollmentError("Drop deadline has passed")

        del self._courses[course_id]
        self._events.append(StudentDroppedCourse(self.student_id, course_id.value))

    def pull_events(self):
        events, self._events = self._events, []
        return events
```

### Example of use
```python
enrollment = CourseEnrollment("E001", "S1234", "2026-S1")
enrollment.enroll(CourseId("CS101"), CreditHours(3), seats_available=5, prerequisites_met=True)
enrollment.enroll(CourseId("CS101"), CreditHours(3), seats_available=5, prerequisites_met=True)
# raises EnrollmentError: Already enrolled in this course
```

---

## 9. Test Scenarios (Given-When-Then)

```
Scenario: Successful enrollment
  GIVEN a student with 12 credits enrolled this semester
  AND course CS101 has 3 credits and 5 seats free
  WHEN the student enrolls in CS101
  THEN the student's total becomes 15 credits
  AND a StudentEnrolledInCourse event is emitted

Scenario: Credit limit exceeded
  GIVEN a student with 16 credits enrolled this semester
  WHEN the student tries to enroll in a 3-credit course
  THEN the enrollment is rejected with "Credit limit of 18 exceeded"
  AND no event is emitted

Scenario: Duplicate enrollment
  GIVEN a student already enrolled in CS101
  WHEN the student tries to enroll in CS101 again
  THEN the enrollment is rejected with "Already enrolled in this course"
```

---

## 10. Summary
- **CourseEnrollment** is the aggregate root, the only gateway for changing a student's enrollments.
- **Value objects** such as `CourseId` and `CreditHours` are immutable and validate themselves.
- **Invariants** (18-credit limit, no duplicates, drop deadline) are enforced inside the aggregate, so they can never be broken from outside.
- **Domain events** announce what happened so other contexts (billing, notifications) can react without being tightly coupled.
- The **repository** loads and saves the aggregate without exposing database details.
