
### Simple State Form
- easy to read and understand
- translatable to database schema (such as collection databases as seen in MongoDB)

### Set and Relation View
- can be one-to-many (username: User -> String)
- can be many-to-many (group: Member -> Group)

- User can be a type parameter
- in the concept of UserGroup[User], User can exist in other concepts and we don't care
- User is defined elsewhere (such as in PasswordAuth)

### Role-Based Access Control
concept RBAC[User, File]

We're going Users <-> Roles <-> Files.
**state**
a set of Roles with a set of Users and a set of Files

- state changing actions go into the effect
- query actions don't modify the state, just check whether a condition holds (must be in requires)
