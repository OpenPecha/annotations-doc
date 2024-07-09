# Pipe Metadata

## Pipe Type

1. automatic processing
2. manual processing
3. automatic followed by manual processing

Notes: 
- for type 3, the pipeline should tell the pipe which processing should be applied.
- for manual processing, pipe should have a flag/argument indicating that manual processing is completed and that Post-processing should be run

## Pipe Dependency

Pipe should contain the information of what annotation layers must be present (as result of running other pipes) to generate the view that becomes the input data of this layer. The layers are listed in the order they should be applied on the root-text to generate the pipe's input.

## Spans of input on which pipe should be run

Pipe should contain the information of which portions of the input data should undergo the processing. 
This information can be used differently depending on whether the processing is automatic or manual

# 1. Preprocessing (generate view)

1. Generate view: by using pipe dependency information
2. Format input data: by using pipe type and pipe specific knowledge about processing

# 2. Processing

if automatic, run input data through processing function, store output and set flag to "True"
if manual, human performs processing, then manually sets flag to "True" to indicate processing is complete

# 3. Postprocessing (generate annotation layer)

if flag is "True":
1. Parse output data
2. Generate annotation layer in .opf file.
3. set pipe state as "Done"