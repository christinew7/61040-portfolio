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

**concept** Dictionary <br>
**purpose** provide a translation from one language to another
**principle** when a user requests the translation of a term, the dictionary will return the appropriate term in their preferred langauge
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
translateTerm(language1: String): (language2: String=) <br>
&nbsp;&nbsp;**requires** this language1 exists in the set of Terms<br>
&nbsp;&nbsp;**effect** returns this language2 associated with this language1<br>

**concept** Abbreviations <br>
**purpose** provide the meaning of an abbreviation <br>
**principle** a user is confused on what an abbreviation is short for, so they highlight it to get the full phrase it represents <br>
**state** <br>
a set of AbbreviationMappings with <br>
&nbsp; an abbreviation String <br>
&nbsp; a USTerm String <br>
**actions** <br>
addAbbreviation(abbreviation: String, USTerm: String) <br>
&nbsp;&nbsp;**requires** this abbreviation isn't already in the AbbreviationMapping<br>
&nbsp;&nbsp;**effect** adds this USTerm to this abbreviation in the AbbreviationMappings <br>
modifyAbbreviation(oldAbbreviation: String, newAbbreviation: String) <br>
&nbsp;&nbsp;**requires** this oldAbbreviation exists in the AbbreviationMappings, this newAbbreviation does not exist in the AbbreviationMappings<br>
&nbsp;&nbsp;**effect** changes this oldAbbreviation in the AbbreviationMappings to this newAbbreviation <br>
deleteAbbreviation(abbreviation: String) <br>
&nbsp;&nbsp;**requires** this abbreviation exists in the AbbreviationMapping<br>
&nbsp;&nbsp;**effect** deletes this abbreviation from the AbbreviationMapping <br>
findAbbreviation(abbreviation: String): (USTerm: String) <br>
&nbsp;&nbsp;**requires** this abbreviation exists in the AbbreviationMapping<br>
&nbsp;&nbsp;**effect** returns the USTerm associated with this abbreviation <br>

**concept** PatternLibrary <br>
**purpose** stores all the patterns that have been uploaded <br>
**principle** a user adds their pattern to the database and can view the pattern at any time **confused** <br>
**state** <br>
a set of Patterns with <br>
&nbsp; a title String <br>
&nbsp; a patternText String <br>
&nbsp; a language String <br>
**actions** <br>
uploadPattern(title: String, patternText: String, language: String) <br>
&nbsp;&nbsp;**requires** this title, this patternText, and this language doesn't already exist in the set of patterns<br>
&nbsp;&nbsp;**effect** creates a new Pattern with this title, this patternText, and this language<br>
getPattern(title: String): (patternText: String, language: String) <br>
&nbsp;&nbsp;**requires** this title exists in the set of Patterns<br>
&nbsp;&nbsp;**effect** returns the patternText and language associated with this title<br>

<!-- getAllPatterns() : (patterns: Set\<Pattern\>) <br>
&nbsp;&nbsp;**effect** returns all the patterns in the set<br> -->

**PatternLibrary.getPattern(title: String): (patternText, language) , PatternLibrary.listPatterns(): (set of Patterns)**

<!-- confused with my concept of togglelanguage -->

**concept** ToggleLanguage[Pattern] <br>
**purpose** translate a pattern to another language <br>
**principle** a user has a primary language and translate their pattern in another language to their primary language <br>
**state** <br>
a set of TranslatedPatterns with <br>
&nbsp; a pattern Pattern <br>
&nbsp; a translatedPattern Pattern <br>
**actions** <br>
uploadPattern(title: String, patternText: String, language: String) <br>
&nbsp;&nbsp;**requires** this title, this patternText, and this language doesn't already exist in the set of patterns<br>
&nbsp;&nbsp;**effect** creates a new Pattern with this title, this patternText, and this language<br>

<!-- not sure what it should be to actually translate everything because we need to translate the entire words?
 -->

## Synchronizations

**sync** a <br>
**when** Request.highlight/click()<br>
**then** Abbreviations.findAbbreviation(abbreviation: String): (USTerm: String) <br>

**need a sync for changelanguage?**

## Note
