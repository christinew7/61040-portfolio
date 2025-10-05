# Concept Design

## Concept Specifications

**concept** PasswordAuthentication <br>
**purpose** limit access to known users <br>
**principle** after a user registers with a username and password, <br> they can authenticate with that same username and password <br> and be treated each time as the same user<br>
**state** <br>
a set of Users with <br>
&nbsp;&nbsp; a username String <br>
&nbsp;&nbsp; a password String <br>
**actions** <br>
register (username: String, password: String): (user: User) <br>
&nbsp;&nbsp;**requires** this username doesn't already exist <br>
&nbsp;&nbsp;**effects** creates a new User with this username and this password<br>
authenticate (username: String, password: String): (user: User) <br>
&nbsp;&nbsp;**requires** this username exists in the Users set, input password matches username's preexisting password <br>
&nbsp;&nbsp;**effects** User is successfully authenticated and returns the User

**concept** Dictionary <br>
**purpose** provide a translation between two specific languages <br>
**principle** the dictionary maintains a mapping of terms between two languages, <br> a user can request the translation of a term and the dictionary will provide the appriopriate term in the other language <br>
**state** <br>
a set of Terms with <br>
&nbsp; a language1 String <br>
&nbsp; a language2 String <br>
**actions** <br>
addTerm(language1: String, language2: String) <br>
&nbsp;&nbsp;**requires** this language1 and this language2 as a set isn't already in the set of Terms <br>
&nbsp;&nbsp;**effect** adds this language1 and this language2 to the set of Terms <br>
deleteTerm(language1: String, language2: String) <br>
&nbsp;&nbsp;**requires** this language1 and this language2 exist in the set of Terms<br>
&nbsp;&nbsp;**effect** deletes this language1 and this language2 from the set of Terms <br>
translateTerm(language1: String): (language2: String) <br>
&nbsp;&nbsp;**requires** this language1 exists in the set of Terms<br>
&nbsp;&nbsp;**effect** returns this language2 associated with this language1<br>

**concept** Library [User] <br>
**purpose** manage collection of files for users <br>
**principle** a user creates a library to store their files, <br> the user can add files to their library, <br> the user can retrieve any file in their library to view its contents, <br> and modify or delete the file as needed, <br> they can also delete the library if it's no longer needed<br>
**state** <br>
a set of Libraries with <br>
&nbsp; an owner User <br>
&nbsp; a set of Files <br>
a set of Files with <br>
&nbsp; an items List\<String\> <br>
&nbsp; a dateAdded DateTime <br>

**actions** <br>
create(owner: User): (library: Library) <br>
&nbsp;&nbsp;**requires** this owner doesn't already have a library<br>
&nbsp;&nbsp;**effect** creates a new library with this owner and an empty set of files <br>
delete(owner: User): <br>
&nbsp;&nbsp;**requires** this owner has a library<br>
&nbsp;&nbsp;**effect** deletes this owner's library<br>
addFile(owner: User, items: List\<String\>) <br>
&nbsp;&nbsp;**requires** this owner has a library, this items doesn't exist in this owner's library of files<br>
&nbsp;&nbsp;**effect** creates a file with this items and the DateTime it was added, adds this file to this owner's library<br>
modifyFile(owner: User, file: File, items: List\<String\>) <br>
&nbsp;&nbsp;**requires** this owner has a library, this file is in this owner's library <br>
&nbsp;&nbsp;**effect** change this file's items to this items<br>
deleteFile(owner: User, file: File) <br>
&nbsp;&nbsp;**requires** this owner has a library, this file is in this owner's library<br>
&nbsp;&nbsp;**effect** deletes this file from this owner's library<br>
getAllFiles(owner: User): (files: Set\<File\>) <br>
&nbsp;&nbsp;**requires** this owner has a library<br>
&nbsp;&nbsp;**effect** returns all files in this owner's library<br>

**concept** FileTracker [User, File] <br>
**purpose** track current position and enable navigation within files <br>
**principle** a user can move through file items sequentially without losing their place, or skip to a file item <br> the user can control how progress is displayed <br>
**state** <br>
a set of TrackedFiles with <br>
&nbsp; an owner User <br>
&nbsp; a file File <br>
&nbsp; a currentIndex Number <br>
&nbsp; a maxIndex Number <br>
&nbsp; a isVisible Flag

**actions** <br>
startTracking(owner: User, file: File): <br>
&nbsp;&nbsp;**requires** this owner exists, this file exists, this owner and this file isn't already in the set of TrackedFiles <br>
&nbsp;&nbsp;**effect** create a new TrackedFile with this owner and this file, currentIndex is initialized to 0, maxIndex is the length of the file's items, isVisible set to true<br>
deleteTracking(owner: User, file: File): <br>
&nbsp;&nbsp;**requires** this owner and this file is in the set of TrackedFiles <br>
&nbsp;&nbsp;**effect** delete this TrackedFile <br>
jumpTo(owner: User, file: File, index: Number): <br>
&nbsp;&nbsp;**requires** this owner and this file exists in the TrackedFiles, this index is a valid index between 0 and the maxIndex <br>
&nbsp;&nbsp;**effect** updates the currentIndex of the TrackedFile with this owner and this file to this index <br>
next(owner: User, file: File) <br>
&nbsp;&nbsp;**requires** this owner and this file exists in the TrackedFiles, the currentIndex of this TrackedFile is less than the maxIndex <br>
&nbsp;&nbsp;**effect** increments the TrackedFile with this owner and this file by 1 <br>
back(owner: User, file: File) <br>
&nbsp;&nbsp;**requires** this owner and this file exists in the TrackedFiles, the currentIndex of this TrackedFile is greater than 0 <br>
&nbsp;&nbsp;**effect** decrements the TrackedFile with this owner and this file by 1 <br>
getCurrentItem(owner: User, file: File): (index: Number) <br>
&nbsp;&nbsp;**requires** this owner and this file exists in the TrackedFiles <br>
&nbsp;&nbsp;**effect** in the TrackedFile with this owner and this file, return the currentIndex <br>
setVisibility(owner: User, file: File, visible: Flag) <br>
&nbsp;&nbsp;**requires** this owner and this file exists in the TrackedFiles <br>
&nbsp;&nbsp;**effect** in the TrackedFile with this owner and this file, set isVisible to this visible<br>

## Synchronizations

**sync** createLibrary <br>
**when** PasswordAuthentication.register(username: String, password: String)<br>
**then** Library.create(owner: User) <br>

**sync** addFile <br>
**when** Library.addFile(user, items: List\<Items\>)<br>
**then** FileTracker.startTracking(user, file) <br>

**sync** stopTrackingOnDeleteFile <br>
**when** Library.deleteFile(user, file) <br>
**then** FileTracker.deleteTracking(user, file) <br>

**sync** stopTrackingOnDeleteLibrary <br>
**when** Library.delete(user) <br>
**where** file in FileTracker.TrackedFiles with owner = user <br>
**then** FileTracker.deleteTracking(user, file) <br>

**sync** translate <br>
**when** Request.translate(page) <br>
**where** word is in page <br>
**then** Dictionary.translate(word) <br>

## Note

The PasswordAuthentication concept helps with creating a user and instantiates the generic user type used in the Library and FileTracker concepts. The generic file type will be bound to the files added in the Library concept. The Dictionary concept will be used in my first two features: translate an abbreviation into the full phrase and translating between US and UK terminology. The Library concept manages each user's collection of crochet patterns, where each file repesents an individual crochet pattern. The FileTracking concept will be the core of the Row Tracking feature. When the `isVisible` flag is true, it will highlight the current step of the pattern (which is the currentIndex/item of the file).
