# Pipeline Metadata

### 1. List of Input Texts

To discuss: either put files in a different location, or adopt a naming convention to filter out files belonging to other pipelines

### 2. Input Text Packages

A text package to run through the pipeline should contain:
- raw .txt file: raw tibetan text
- metadata of that text: table containing author, titles, etc.

### 3. State of Texts in the Pipeline

The pipeline should know how far each individual text has gone, and what is the next step.

### 4. Ordered List of Pipes

- list of pipes: names of all the pipes that need to be traversed, in the correct order.
- optionality status of each pipe: state if traversing the pipeline can be continued if pipe is not completed.

### 5. Execution Mode

2 modes:
- automatic: runs all steps not requiring manual processing
- manual: when a manual processing is required, runs preprocessing of pipe. in next execution of manager, if the flag of completion is True, executes postprocessing of pipe and resumes pipeline traversal.

# A. Preprocessing (parse text package into OPF)

1. parse + normalize raw text (normalize spaces, punctuation and linebreaks).
2. parse metadata file.

# B. Traversing Pipeline

The pipeline manager loops over all texts and does the following to each one:
- checks the state of each text and moves to the corresponding pipe

- advances in the pipeline
  - if mode == automatic:
    - all manual steps are bypassed
      - manual-only steps are avoided altogether
      - exits pipeline when a non-optional pipe is not completed
  - if mode == manual:
    - all the automatic pipes not yet completed are executed
      - when encountering a manual step that is not yet completed, 
        - run pipe preprocessing (produce the human-readable file)
        - updates state of text
        - exits
      - when encountering a manual step that has been completed,
        - run pipe post-processing (generate annotation layer)
        - rerun all subsequent automatic pipes
    
- produces/updates status report (in Google Spreadsheet) of all the texts

# C. Postprocessing

Is executed when a text has traversed the pipeline and has not exited due to a non-optional pipe not being completed.
This happens in 3 situations: 
- execution mode is automatic and no manual steps have been run.
- one or more manual steps have been completed and the subsequent automatic pipes have been rerun.
- all the manual steps have been completed and the pipeline is completed for this text

At this point, the output file is produced.

In order to allow smooth data sharing between similar projects and adoption of pipeline:
- The output file format should be a compromise between being human-readable and machine-parsable.
- Any formatting or information not strictly related to the pipeline and the annotations should be avoided.
- Annotation tags such as "<root-text>xxxx</root-text>" should be fully written, to maximize readability.