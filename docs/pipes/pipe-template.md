# Pipe Metadata

### 1. Pipe Type

1. automatic processing
2. manual processing
3. automatic followed by manual processing

Notes: 
- for type 3, the pipeline should tell the pipe which one should be executed next.
- for manual processing, pipe should have a flag/argument indicating that manual processing is completed and that Post-processing should be run

### 2. Pipe Dependency

Pipe should contain the information of what annotation layers must be present (as result of running other pipes) to generate the view that becomes the input data of this layer. The layers are listed in the order they should be applied on the root-text to generate the pipe's input.

is constituted of:
- list of dependencies. The pipes that must be completed before pipe is allowed to be executed.

### 3. Spans of input on which pipe should be run

portions of input to be avoided: (`[(start_idx, end_idx), (..., ...)]`). 
   provided by the pipeline manager.

Pipe should contain the information of which portions of the input data should undergo the processing. 
This information can be used differently depending on whether the processing is automatic or manual: either to provide the chunks of text to avoid for automatic steps, or to highlight the chunks not needing manual processing.

This will allow to skip the passages in the base text to which the current processing should not be applied. For example, in the pipe detecting the interspersed syllables of the root text in the commentary, the passages containing the root text citations should be avoided to correctly identify the first occurence of each root-text syllable in the commentary.

### 4. Subsequent pipes should avoid tagged passages

Pipe should tell to pipeline if the tagged substrings of input should be avoided by subsequent pipes or not.
value is `True` or `False`

# A. Preprocessing (generate view)

1. Generate view: by using pipe dependency information
2. Format input data: by using pipe type and pipe specific knowledge about processing

# B. Processing

if automatic, run input data through processing function, store output and set flag to "True"

if manual, human performs processing, then manually sets flag to "True" to indicate processing is complete

# C. Postprocessing (generate annotation layer)

if flag is "True":
1. Parse output data
2. Generate annotation layer in .opf file.
3. set pipe state to "Done"