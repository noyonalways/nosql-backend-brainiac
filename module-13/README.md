<h1 align='center'>Building PH University Management System Part-3</h1>

## Topics:

1. Fix your previous bugs
2. Create Academic Faculty, Interface, model ,and validation
3. Create Academic faculty routes, controllers and services
4. Test Academic Faculty Routes using Postman
5. Create Academic Department interface , model , validation ,service
6. Create Academic Department controller , route and test using postman
7. Handle department validation when creating & updating document
8. How to populate referencing fields & implement AppError class
9. Implement transaction & rollback
10. Delete student using transaction & rollback
11. Dynamically update both primitive & non primitive fields
12. Implement logic to handle dynamically update non-primitive fields

## Table of Contents:

- [Fix your previous bugs](#fix-your-previous-bugs)
  - [What we will learn?](#what-we-will-learn)
- [Implement Transaction \& Rollback](#implement-transaction--rollback)
  - [4 Principles of Transaction and Rollback](#4-principles-of-transaction-and-rollback)
  - [A - Atomicity](#a---atomicity)
  - [C - Consistency](#c---consistency)
  - [I - Isolation](#i---isolation)
  - [D - Durability](#d---durability)
  - [When should we use Transaction?](#when-should-we-use-transaction)
  - [Transaction Steps:](#transaction-steps)
- [Dynamically update both primitive \& non primitive fields](#dynamically-update-both-primitive--non-primitive-fields)
  - [Updating Nested object with sub Schemas](#updating-nested-object-with-sub-schemas)
  - [Example](#example)

# Fix your previous bugs

### What we will learn?

- Bug fix
- Academic Faculty (CRUD)
- Academic Department (CRUD)
- Transaction and Rollback
- Custom AppError
- Dynamic Update Primitive & Non-Primitive Field
- Faculty (CRUD)

# Implement Transaction & Rollback

### 4 Principles of Transaction and Rollback

- **A → Atomicity**
- **C → Consistency**
- **I → Isolation**
- **D → Durability**

### A - Atomicity

- The entire transaction takes place at once or doesn’t happen at all.
- It involves the following two operations -
  - **Abort:** If a transaction aborts, changes made to the database are not visible.
  - **Commit:** If a transaction commits, changes made are visible.
- Atomicity is also known as the “**All or nothing rule”**

### C - Consistency

- Ensure that the database maintains its **integrity** and keep its **balance**
- It guarantees that relationships between **pieces of data remain intact**. For example, if you have data about a person and their address, consistency ensures that the person and their address are still connected after a transaction.
- Makes sure that the changes made by one transaction don’t interfere with **correctness** of another.

### I - Isolation

- Ensure that each transaction operates **independently**
- Transactions **don’t see** each other’s **unfinished work**. Each gets its own space to **complete without disturbance.**
- Even if lots of things are happening, **transactions don’t interrupt each other;** they wait for their chance.

### D - Durability

- Once you save something in the database, **it stays saved**, even if the power goes out or the system crashes.
- Changes made by a transaction are **permanent**. They don’t disappear, no matter what happens next.
- Once you commit a change, it’s there to **stay**.
- Even if the system has a hiccup (like a sudden shutdown), the data you saved remains **safe and sound.**

### When should we use Transaction?

Two or more database write operation.

### Transaction Steps:

- startSession()
- startTransaction()
- commitTransaction() - when succeed
- abortTransaction() - when failed
- endSession()

# Dynamically update both primitive & non primitive fields

### Updating Nested object with sub Schemas

```ts
/students/:studentId (PATCH)

{
  "name": {
    "lastName": "Doe"
  }
}
```

But we have to keep a **consistent data structure** for creating and updating students. So we need to covert nested objects to flattened fields in the service layer for updating the student.

**Handle Logic in Backend**

### Example

```ts
// update student
const updateSingle = async (id: string, payload: IStudent) => {
  if (!(await Student.isStudentExists("id", id))) {
    throw customError(false, httpStatus.NOT_FOUND, "student not found");
  }

  // destructure the non-primitive data
  const { name, guardian, localGuardian, ...remainingData } = payload;

  const modifiedObj: Record<string, unknown> = {
    ...remainingData,
  };

  // name
  if (name && Object.keys(name).length) {
    for (const [key, value] of Object.entries(name)) {
      modifiedObj[`name.${key}`] = value;
    }
  }
  // guardian
  if (guardian && Object.keys(guardian).length) {
    for (const [key, value] of Object.entries(guardian)) {
      modifiedObj[`guardian.${key}`] = value;
    }
  }
  // local guardian
  if (localGuardian && Object.keys(localGuardian).length) {
    for (const [key, value] of Object.entries(localGuardian)) {
      modifiedObj[`localGuardian.${key}`] = value;
    }
  }

  const result = Student.findOneAndUpdate({ id }, modifiedObj, {
    new: true,
    runValidators: true,
  });

  return result;
};
```
