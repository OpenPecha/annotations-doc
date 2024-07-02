
# Pecha Data Preparation Pipeline

Documents the pipeline implemented to prepare the data for [Pecha](https://www.pecha.org).

## Pecha Requirements

- Texts that have no outline (root texts)
  - are segmented in meaningful minimal sentences to ensure no commentary will ever contain an outline boundary located in the middle of a sentence. (traditional division of 4 lined verses have this issue)
- Texts with one level of outline (some root texts and others)
  - are split into chapters
  - have the content of each chapter segmented into meaningful minimal sentences as above.
- Texts with more than one level of outline (commentary or other treatises)
  - have outline (sapche) content tagged and separated into independent segments
  - have quoted passages of the root text tagged and constitute independent segments if they are big. They are included inside other segments if they are small.
  - have citations tagged and always constitute independent segments.
  - have syllables of the root text integrated into the commentary tagged. Syllables may be omitted, modified or replaced by other words.
  - have the content in between segments split into bite-sized paragraphs. Paragraphs are segmented using size as a primary criterion, but must constitute a complete semantic unit (mostly phrases and sentences).

Furthermore:
- segments containing root text syllables will be linked to the corresponding segments in the root text.
- the full outline will be manually parsed so that texts with multiple outline levels can be browsed using the outline.
- In the future, citations will be linked to the corresponding segments in the text of origin.

_Note: see individual pipes for a more complete description of each step._

## Pipeline In/Out

### Input: 

- `.txt` raw text files of texts. The texts have already undergone a full workflow that includes comparing various versions of the same text to get the most accurate spelling. 
- metadata pertaining to each text, in one or more languages, such as:
  - its author
  - its usage title
  - its short title
  - its long title
  - its alternative titles
  - ...

Texts can be of three types:
1. simple texts with **no outline** (sapche) structure, like most prayers and root texts,
2. texts with **one level of outline**. For ex. the སྤྱོད་འཇུག། which is divided into 10 chapters.
3. texts with **multiple levels of outline**, like most of the Tibetan literature (the standard depth of sapches in texts is 20 levels).

### Output: 

TBD, but most probably a `.json` file per text containing all the annotations in a format that can be ingested by Pecha's db.

## Pipeline Steps and Rationale

### Order of Precedence and Rationale

Annotation pipes should be traversed in the order defined below in order to ensure best tagging results for each annotation and minimize false positives.

#### texts with depth 0:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**1.** sentence segmentation

#### texts with depth 1:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**1.** sapche boundary detection

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**2.** sentence segmentation

Rationale: 
- the sapche (outline) delimits the meaningful sections of a text. Thus, a sentence necessarily stops where a sapche begins. 
- sapches follow strict structural patterns that don't correspond to sentence boundaries. Thus, a sentence tokenizer could consider them part of a sentence, or even add a sentence break in its middle. Furthermore, giving smaller chunks of raw text to a sentence tokenizer yields a better segmentation.

#### texts with depth > 2:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**1.** sapche boundary detection

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**2.** citation detection

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**3.** root-text citation detection

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**4.** root-text syllable annotation

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**5.** paragraph segmentation

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**6.** sapche tree structure creation

Rationale:
- since sapches (**1.**) establish the structure of the text, they take precedence over any other segmentation. Nothing overrides them and their content can't be anything else but sapche content. This is why this pipe is placed first.
- citations (**2.**) are always separate segments, unless they only span a few syllables. Modern book publications of Tibetan texts always place them in separate paragraphs and indent them. We follow this convention that allows the reader to either spend time analyzing the citation, or to quickly move over to the next part of the author's argumentation. Citations may occur at similar locations within sections of a text (either beginning, or end, or middle, etc.), so having clearly delimited sections when detecting citations makes human quality control easier and more consistent.
- root-text citations (**3.**) are essentially regular citations and are treated similarly, although they should have a slightly different formatting (colors, etc.) to highlight their special status. Having text sections already delimited and visually identifying the places inside sections where citations are found makes it easier for human quality control to look where root-text citations are expected.
- root-text syllables (**4.**) are also positioned at similar locations inside all sections of the text, making it easier for human quality control to be conducted. In order to minimize false-positives, root-text syllable detection should not be applied to sapches (**1.**), neither to citations (**2.**). Root-text citations (**3.**) being independent of the actual commentary of each word of the root text, they are false positives that should be avoided when detecting root-text syllables.
- paragraph segmentation (**5.**) should be applied only where there is actual commentary, not in segments containing only structural segments (sapches 1.) and citation segments (**2.** and **3.**). Furthermore, the presence of root text syllables (**4.**) in a given passage shows it constitutes a meaningful unit, and because of that, paragraph boundaries should be searched for after the end of root-text syllables.
- sapche tree structure creation (**6.**) is a step that (still) can't be performed automatically because of its complexity. Because of that, the more information visually provided to the human annotator, the better and faster he will be able to perform this task. This is why this annotation pipe is left at the very end of the pipeline.

#### Statuses of Texts Traversing the Pipeline

1. Texts will undergo a completely automatic annotation before the initial publication.
2. Human annotators then check the output of each pipe, modifying and improving the annotations where necessary. Every pipe is validated by a human annotator.
3. After the human validation of each annotation pipe, the text traverses the subsequent pipes automatically, in order to leverage the improvements.
4. A status for each text is provided to Pecha to notify the users that the text they are reading has not been fully prepared. The initial status is "automatic pipeline completed", which will then be complemented to include the human-validated pipes.
