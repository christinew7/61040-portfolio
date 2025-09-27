# Concept Design

## Concept Specifications

<!-- toggle language
- purpose is to switch to language that the user is used to
- principle; there is a default language and the user does not learn in that language, they hit the toggle button and it changes the language

in-line abbreviation
- purpose is to give more details to a vocabulary word


user auth
- saves language preferences
- concept to save patterns - is this too much
- basic concepts should be whatever adds to features?
- user and user auth ? -->

**concept** PasswordAuthentication <br>
**purpose** limit access to known users <br>
**principle** after a user registers with a username and password, <br> they can authenticate with that same username and password <br> and be treated each time as the same user<br>
**state** <br>
a set of Users with <br>
&nbsp;&nbsp; a username String <br>
&nbsp;&nbsp; a password String <br>
**actions** <br>
register (username: String, password: String): (user: User) <br>
&nbsp;&nbsp;**requires** username doesn't already exist <br>
&nbsp;&nbsp;**effects** creates a new User with this username and password<br>
authenticate (username: String, password: String): (user: User) <br>
&nbsp;&nbsp;**requires** username exists in the Users set, input password matches username's preexisting password <br>
&nbsp;&nbsp;**effects** User is successfully authenticated and returns the User

**concept** Dictionary <br>
**purpose** provide a translation between two specific languages <br>
**principle** when a user requests the translation of a term, the dictionary will return the appropriate term in the other language<br>
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
**purpose** maps users to items in their library **probably need to fix to include the row tracking** <br>
**principle** a user adds a File to their Library, which tracks their progress as they go through the file <br>
**state** <br>
a set of Libraries with <br>
&nbsp; an owner User <br>
&nbsp; a set of Files <br>
a set of Files with <br>
&nbsp; an items List\<String\> User <br>
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


## Synchronizations

**sync** a <br>
**when** Request.highlight/click()<br>
**then** Abbreviations.findAbbreviation(abbreviation: String): (USTerm: String) <br>

**need a sync for changelanguage?**

## Note
