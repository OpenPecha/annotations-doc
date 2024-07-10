# Pipe Metadata

### 1. Pipe Type

value: `automatic` + `manual`

### 2. Pipe Dependency

value: `None`

### 3. Spans of input on which pipe should be run

value: (given by pipeline manager)

### 4. Subsequent pipes should avoid tagged passages

value: True

# A. Preprocessing (generate view)

1. generate view:
```
def gen_view():
    view containing base-view with nothing else
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
    all portions to avoid should be formatted distinctively
```

# B. Processing

### if exec_mode == automatic:

1. loop over spans(substrings) of input that need processing and apply following function to each span:
```
def sapche_boundary_detection(in_string, tag_name="sapche"):
    detect sapche boundaries
    add tags "<sapche>xxx</sapche>" to detected boundaries
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
