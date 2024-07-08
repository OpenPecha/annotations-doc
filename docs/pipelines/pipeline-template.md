# What is a Pipeline?

A structure that allows a text to undergo one or more processing pipes, each generating an annotation layer that is stored in the .opf file.

The processing of each pipe can be conducted manually, automatically, or a combination of both.

A pipe may require as input the output of other pipes.

A pipe will take as input a list of sub-strings of the base-text found in the .opf file. This will allow to skip the passages in the base text to which the current processing should not be applied. For example, in the pipe detecting the interspersed syllables of the root text in the commentary, the passages containing the root text citations should be avoided to correctly identify the first occurence of each root-text syllable in the commentary.

When a pipe is rerun or manually corrected, all the subsequent pipes should be rerun and the updated annotation layers for each one should be produced.

