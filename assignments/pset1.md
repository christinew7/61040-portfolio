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

3. All usernames in the Users set is unique. It is preserved with the `register` action.
4. **state** <br>
   &nbsp;&nbsp;&nbsp;&nbsp;a set of RegistrationRequests with <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a username String <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a password String <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a token String <br>
   **actions** <br>
   &nbsp;&nbsp;&nbsp;&nbsp;register (username: String, password: String): (token: String) <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** username doesn't exist in Users, username doesn't exist in RegistrationRequests<br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** generates a secret Token to be emailed to the user and saves username, password, and token to RegistrationRequests<br>
   &nbsp;&nbsp;&nbsp;&nbsp;confirm (username: String, token: String): (user: User) <br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** username exists in RegistrationRequests, token matches the token in RegistrationRequests<br>
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** creates a new User with the username and password found from RegistrationRequests
   <br>

## Exercise 3: Comparing concepts

**concept** PersonalAccessToken[User] <br>
**purpose** grants access of Github API or command line to a User <br>
**principle** after authenticated User (who has already signed in with their username and password) creates a PersonalAccessToken, <br>
they can authenticate themselves using their username and generated PersonalAccessToken when using Github API or command line <br>
and be granted access to all repositories in organizations that they have access to <br>
**state** <br>
&nbsp;&nbsp;&nbsp;&nbsp;a set of Tokens with <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a user User <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a token String <br>
**actions** <br>
&nbsp;&nbsp;&nbsp;&nbsp;create (user: User): (token: String) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** user is logged in<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** creates a Token with a randomized and unique token and this user<br>
&nbsp;&nbsp;&nbsp;&nbsp;delete (user: User, token: String): <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** user is logged in and token is associated with this user<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** delete the token from the set of Tokens<br>
&nbsp;&nbsp;&nbsp;&nbsp;authorize (user: User, token: String): <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** this token is associated with this user<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** this user is authorized to all repositories in organizations this user has access to <br>

PersonalAccessToken differs from PasswordAuthentication because a User can have multiple tokens, each of which can have limitations and expirations. I would add to the GitHub documentation that the access tokens are used to sign into their account securely and it should never be shared with anyone else. I would not use the word "password" because it diverges from the concept's purpose and is a little misleading.
**fix this**

## Exercise 4: Defining familiar concepts

### URL Shortener

**concept** URLShortener <br>
**purpose** provides a shortened alias for long URLs that redirect to the original content <br>
**principle** a user provides a long URL and an optional unique alias, <br>
the system creates a shortened URL with the user-defined alias or generates a unique one that redirects any user accessing the shortened URL to the long URL <br>
**state** <br>
&nbsp;&nbsp;&nbsp;&nbsp;a set of ShortenedURLs with <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a longURL String <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;an alias String <br>
**actions** <br>
&nbsp;&nbsp;&nbsp;&nbsp;create (longURL: String, alias: Optional\<String\>): (shortenedURL: ShortenedURL) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** if alias is provided, no ShortenedURL is already associated with this alias<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** creates a ShortenedURL with the provider domain, followed by this alias if provided or a randomly generated unique alias if not provided; the ShortenedURL redirects to this longURL<br>
&nbsp;&nbsp;&nbsp;&nbsp;redirect (alias: String): (longURL: String) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** a ShortenedURL where its alias matches this alias<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** returns the longURL associated with the ShortenedURL with this alias <br>

### Billable Hours Tracking

**concept** BillableHours[Employee, Project, Client] <br>
**purpose** allows employees to keep track of their hours on projects for client billing <br>
**principle** an employee starts a session by selecting their project and fills in their task, in which the system will record the start time<br>
after the employee finishes the task, they will end the session and the system will record the end time <br>
**state** <br>
&nbsp;&nbsp;&nbsp;&nbsp;a set of Sessions with <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;an employee Employee <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a project Project <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a task String <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a startTime DateTime<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;an optional endtime DateTime <br>
**actions** <br>
&nbsp;&nbsp;&nbsp;&nbsp;startSession (employee: Employee, project: Project, task: String): (session: Session) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** this project exists in the set of Projects, this employee exists in the set of Employees, this task isn't already associated with a Session with this project and this employee, this employee doesn't already have an ongoing Session <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** creates a Session with this employee, this project, this task, and the current date/time<br>
&nbsp;&nbsp;&nbsp;&nbsp;endSession (employee: Employee, project: Project, task: String): <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** this employee, this project, and this task are all associated in one Session <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** adds the current date/time to the Session with this employee, this project, and this task <br>
&nbsp;&nbsp;&nbsp;&nbsp;endSessionManual (employee: Employee, project: Project, task: String, endTime: DateTime): <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** this employee, this project, and this task are all associated in one Session, this Session has not ended yet, the endTime is after than this Session's startTime <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** adds the this endTime to the Session with this employee, this project, and this task <br>

### Conference Room Booking

**concept** ConferenceRoomBooking[User] <br>
**purpose** allows users of a group to manage the reservation of conference rooms for specific time intervals <br>
**principle** the company/department of the building adds all the rooms and their room capacity, <br>
a booker can book available rooms within the building's operating hours, <br>
and bookings can be managed by the booker and its designated additional users <br>
**state** <br>
&nbsp;&nbsp;&nbsp;&nbsp;a set of Bookings with <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a booker User<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a set of managers Set<User><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a room Room<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a title String <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a startTime DateTime<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;an endTime DateTime <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;an optional instructions String<br>
&nbsp;&nbsp;&nbsp;&nbsp;a set of Rooms with <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a name String<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a capacity Number<br>
**actions** <br>
&nbsp;&nbsp;&nbsp;&nbsp;addRoom (name: String, capacity: Number): (room: Room) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** there isn't already a Room associated with this name <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** adds a new Room of this name and this capacity <br>
&nbsp;&nbsp;&nbsp;&nbsp;deleteRoom (room: Room) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** this room exists, there is no Booking associated with this Roo <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** removes this Room from the set of Rooms for available bookings <br>
&nbsp;&nbsp;&nbsp;&nbsp;bookRoom (user: User, room: Room, startTime: DateTime, endTime: DateTime, managers: Optional\<Set\<Users\>\>, notes: Optional\<String\>): <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** this room doesn't already have a Booking where the startTime and endTime overlap; this endTime is after this startTime <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** adds a new Booking associated with this user, this room, this startTime, this endTime, this managers, and this notes (if applicable) <br>
&nbsp;&nbsp;&nbsp;&nbsp;editRoom (user: User, room: Room, startTime: Optional\<DateTime\>, endTime: Optional\<DateTime\>, newManagers: Optional\<Users\>, notes: Optional\<String\>): <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** there is a Booking associated with this roomName, this user is in this Booking's set of managers, this endTime ends on or before this room's startTime if provided and are within the building's operating hours, this room doesn't already have a Booking where this startTime and this endTime overlap <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** updates this Booking with the new fields if applicable, including this startTime, this endTime, this managers, and this notes <br>
&nbsp;&nbsp;&nbsp;&nbsp;cancelRoom (user: User, room: Room): <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**requires** there is a Booking associated with this room, this user is in this Booking's booker or managers <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**effects** deletes this Booking <br>
