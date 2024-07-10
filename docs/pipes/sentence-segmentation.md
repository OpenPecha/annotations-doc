# Pipe Metadata

### 1. Pipe Type

value: `automatic` + `manual`

### 2. Pipe Dependency

value: `sapche-boundary-detection` (to discuss if dependency info should be in pipe or in pipeline. related to sub-pipeline discussion)

### 3. Spans of input on which pipe should be run

value: (provided by pipeline manager)

### 4. Subsequent pipes should avoid tagged passages

value: False

# A. Preprocessing (generate view)

1. generate view:
```
def gen_view():
    view containing base-text + sapche-boundary-detection
```

2. format data:

if exec_mode == automatic:
```
def format_automatic():
    simply return string
```

if exec_mode == manual:
```
def format_manual():
    create a .docx file and upload it to Google Drive
    beginning and ending syllable of each sentence should have a different color.
    all passages not needing processing should have colored background
```

# B. Processing

### if exec_mode == automatic:

1. loop over spans(substrings) of input that need processing and apply following function to each span:
```
def sentence_segmentation(in_string, tag_name="sentence"):
    detect sapche boundaries
    add tags "<sentence>xxx</sentence>" to detected boundaries
    return string
```

2. join all intermediary results into a single string for annotation transfer

### if exec_mode == manual:
```
create notification for human annotator with all the required information
(.docx file, OR Google Drive link, ...)

set flag to "waiting for human annotator to finish"
```

# C. Postprocessing

if exec_mode == automatic:
```
def generate_annotation_layer(annotated_string):
    1. use annotation transfer to create layer from:
        a. base-text
        b. annotated_string
    2. create and store layer in OPF
```

if exec_mode == manual and flag is `completed`:

- run sanity tests to ensure no human errors will be brought into pipeline
- extract output string from human-readable file
- run above function to generate annotation layer

elif flag is not `completed`:

pass
