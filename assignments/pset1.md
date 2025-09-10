# Problem Set 1: Reading and Writing Concepts

## Exercise 1: Reading a concept

1. Invariants

- One invariant is the purchase count of an item is never negative and less than or equal to the total count of the requested item.
- The second invariant is every Purchase must always have an associated Request in an active registry.
- The second invariant is more important because the whole reason a Purchase exists is because of an open Request with at least `count` in an active Registry. If the second invariant is violated, the first invariant is meaningless. The `purchase` action is most affected and it preserves the invariant because it will only add a purchase to the registry if it knows the registry exists, is active, and has a request for the item with at least `count`.

2. Fixing an action

- An action that potentially breaks this invariant is `removeItem`. If a purchase already exists for an item, and we remove the item, the purchase will no longer have an associated request. This problem might be fixed by adding an additional requirement in `removeItem` to check that no purchase already exists for the item.

3. Inferring behavior

- A reason to allow this is if the owner wants to modify their registry, but doesn't want other users to see their registry until they are fully done with their updates.

4. Registry deletion

- This doesn't matter in practice because `close` has a similar effect.

5. Queries

- Two common queries are viewing all purchases of a registry and viewing available requests (whose `count` is positive).

6. Hiding purchases

- I would add to the purpose that the owner has the option to not see purchases. The principle will be modified so the recipient can choose if they want to be surprised and not see purchases when they create a registry. The state will have a `surprise` Flag with an additional action of
  ```text
  enableSurprise(registry: Registry)
      **requires** registry exists
      **effects** sets the `surprise` flag to true
  ```

7. Generic types

- This is preferable because the descriptions and prices can be automatically updated in real time.

## Exercise 2: Extending a familiar concept

1. **state** <br>
   &nbsp;&nbsp;&nbsp;&nbsp;a set of Users with <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a username String <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a password String <br>
2. **actions**<br>
   &nbsp;&nbsp;&nbsp;&nbsp;register (username: String, password: String): (user: User) <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** username doesn't already exist <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** creates a new User with this username and password<br>

   &nbsp;&nbsp;&nbsp;&nbsp;authenticate (username: String, password: String): (user: User) <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** username exists in the Users set, input password matches username's preexisting password <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** User is successfully authenticated and returns the User
