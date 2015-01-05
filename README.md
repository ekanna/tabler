**If you see opportunities for improvement (features, simplification, clarity), please submit an issue (or a pull request). Already high on the queue: I will be revising the tag format to make it more canonical and reflect-compliant. Thanks!**

# tabler

Go generate syntactic sugar for SQL-backed structs

## Introduction

Given a struct with tagged fields, `tabler` will generate methods that return strings for the following actions:
- Create Table
- Drop Table
- Insert Row
- Select Row

See the [example](https://github.com/tristanwietsma/tabler/tree/master/example) for more information.

## Installation

```bash
go get github.com/tristanwietsma/tabler
```

## Use

Add the `go:generate` directive to files with SQL-backed structs.

```go
//go:generate tabler $GOFILE
```

Add the `@table` decorator to the comment block for all target structs. Tag each field with the data type and label the primary keys.

```go
// @table
type User struct {
    ID      string    `type:uuid,primary:true`
    Email   string    `type:varchar(128)`
    Created time.Time `type:timestamp`
}
```

Run `generate` and tabler will produce `*_tabler.go` files for those files containing decorated structs.

```bash
go generate
go build
```

## Tags

### Requirements

- Every field must be tagged.
- `type` is required for every field.
- Every table must have at least one primary key.

### Conventions

Fields matching the pattern `<something>ID` are assumed to be foreign keys. For example:

```go
// @table
type Profile struct {
    UserID    string `type:uuid,primary:true`
    Attribute string `type:varchar(64),primary:true`
    Value     string `type:varchar(256)`
}
```

In the above, `UserID` will be defined as `userid uuid REFERENCES user(id)` in the table creation statement.
