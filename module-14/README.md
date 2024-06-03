<h1 align='center'>Building PH University Management System Part-4</h1>

## Topics:

1. What is Error Handling
2. Understanding Error Patterns in Zod and Mongoose
3. How to Convert Zod Error
4. How to convert mongoose validation error
5. How to handle castError and 11000 error
6. How to handle Error, AppError, UnhandledRejection, UncaughtException
7. How to do raw Searching
8. How to do raw filtering ,sorting and limiting
9. How to do pagination and field limiting
10. Refactor your code and build a Query Builder
11. Complete Query Builder, test using postman
12. Practice Task Challenge
13. GitHub Link: Practice Task Solutions

## Table of Contents:

- [What is Error Handling](#what-is-error-handling)
  - [What we will learn?](#what-we-will-learn)
  - [Types of Error](#types-of-error)
- [Understanding Error Patterns in Zod and Mongoose](#understanding-error-patterns-in-zod-and-mongoose)
- [How to do raw Searching](#how-to-do-raw-searching)
  - [Search term](#search-term)
  - [Filters](#filters)
- [Raw Pagination and Filed Filtering](#raw-pagination-and-filed-filtering)
- [Using Query Builder](#using-query-builder)

# What is Error Handling

### What we will learn?

- Error Handling
- Searching
- Filtering
- Pagination
- Practice Task

![error-handler.png](https://i.ibb.co/PtjCmtg/error-handler.png)

### Types of Error

- **Operational Error**
  - invalid user inputs
  - failed to run server
  - failed to connect database
  - invalid auth token
- **Programmetical Error**
  - Error that developers produce when developing
  - using undefined variables
  - using properties that do not exist
  - passing number instead of string
  - using `req.params` instead of `req.query`
- **Unhandled Rejection (Asynchronous Code)**
- **Uncaught Exception (Synchronous Code)**

![type-of-error.png](https://i.ibb.co/wQGQ3y1/type-of-error.png)

![error-from-anywhere.png](https://i.ibb.co/BVJtM1R/error-from-anywhere.png)

# Understanding Error Patterns in Zod and Mongoose

# How to do raw Searching

### Search term

Partial Match

### Filters

Exact Match

```ts
await Student.find().skip().sort().limit()
    returns  queary  queary  queary  queary
```

# Raw Pagination and Filed Filtering

```tsx
const getAll = (query: Record<string, unknown>) => {
  const queryObj = { ...query };

  const studentSearchableFields = [
    "email",
    "name.firstName",
    "name.middleName",
    "presentAddress",
  ];
  let searchTerm: string = "";

  if (query.searchTerm) {
    searchTerm = query?.searchTerm as string;
  }

  const searchQuery = Student.find({
    $or: studentSearchableFields.map((field) => ({
      [field]: { $regex: searchTerm, $options: "i" },
    })),
  });

  // filtering
  const excludeFields = ["searchTerm", "sort", "limit", "page", "fields"];
  excludeFields.forEach((item) => delete queryObj[item]);
  console.log({ query }, { queryObj });

  const filterQuery = searchQuery
    .find(queryObj)
    .populate("admissionSemester")
    .populate({
      path: "academicDepartment",
      populate: {
        path: "academicFaculty",
      },
    });

  let sort = "-createdAt";
  if (query.sort) {
    sort = query.sort as string;
  }

  const sortQuery = filterQuery.sort(sort);

  let page = 1;
  let limit = 5;
  let skip = 0;

  if (query.limit) {
    limit = parseInt(query.limit as string);
  }

  if (query.page) {
    page = parseInt(query.page as string);
    skip = (page - 1) * limit;
  }

  const paginateQuery = sortQuery.skip(skip);

  const limitQuery = paginateQuery.limit(limit);

  // field limiting
  let fields = "-__v";

  // fields: 'name,email';
  // fields: 'name email';

  if (query.fields) {
    fields = (query.fields as string).split(",").join(" ");
  }

  const fieldsQuery = limitQuery.select(fields);
  return fieldsQuery;
};
```

# Using Query Builder

```ts
import { FilterQuery, Query } from "mongoose";

class QueryBuilder<T> {
  public modelQuery: Query<T[], T>;
  public query: Record<string, unknown>;

  constructor(modelQuery: Query<T[], T>, query: Record<string, unknown>) {
    this.modelQuery = modelQuery;
    this.query = query;
  }

  // methods
  search(searchableFields: string[]) {
    const searchTerm = this?.query?.searchTerm;
    if (searchTerm) {
      this.modelQuery = this.modelQuery.find({
        $or: searchableFields.map(
          (field) =>
            ({
              [field]: { $regex: searchTerm, $options: "i" },
            } as FilterQuery<T>)
        ),
      });
    }
    return this;
  }

  filter() {
    const queryObj = { ...this.query };
    const excludeFields = ["searchTerm", "sort", "limit", "page", "fields"];
    excludeFields.forEach((item) => delete queryObj[item]);

    this.modelQuery = this.modelQuery.find(queryObj as FilterQuery<T>);
    return this;
  }

  sort() {
    const sort = this?.query?.sort || "-createdAt";
    this.modelQuery = this.modelQuery.sort(sort as string);
    return this;
  }

  paginate() {
    const page = Number(this?.query?.page) || 1;
    const limit = Number(this?.query?.limit) || 5;
    const skip = (page - 1) * limit;

    this.modelQuery = this.modelQuery.skip(skip).limit(limit);
    return this;
  }

  fields() {
    const fields =
      (this?.query?.fields as string).split(",").join(" ") || "-__v";

    this.modelQuery = this.modelQuery.select(fields as string);
    return this;
  }
}

export default QueryBuilder;
```
