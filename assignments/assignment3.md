# Assignment 3: An AI-Augmented Concept

## Concept Specifications

### Original Concept

**concept** FileTracker [User, File] <br>
**purpose** track current position and enable navigation within files <br>
**principle** a user can start tracking their file from the first item, <br> they can move through file items sequentially without losing their place, or skip to a file item <br> and control how progress is displayed <br>
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

### AI Augmented Concept
Context: The generic File type includes the set of Items (which are line items in plain text). For my project, the files the user will be submitting are crochet patterns. 

**concept** FileTracker [User, File] <br>
**purpose** track current position and enable navigation within files <br>
**principle** a user can start tracking their file from the first listed item (which might not be the first item), <br> they can move through file items sequentially without losing their place, or skip to a file item <br> and control how progress is displayed <br>
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
startTracking(owner: User, file: File, llm: GeminiLLM): <br>
&nbsp;&nbsp;**effect** uses this llm to start tracking this file with this owner at a smart-assigned currentIndex<br>
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
