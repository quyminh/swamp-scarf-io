
### NAME
HashToScarf - A python module for writing Scarf
### SYNOPSIS
```python
from HashToScarf import HashToScarf
from HashToScarf import CheckStart
from HashToScarf import CheckBug
from HashToScarf import CheckMetric

output = "/path/to/file";

writer = HashToScarf(output, 1, 1);
defaultPretty = writer.GetPretty;
defaultErrorLevel = writer.GetErrorLevel;
writer.setPretty(0);
writer.setErrorLevel(0);

errors = [];
errors.append(CheckStart(INITIALDETAILS));
errors.append(CheckBug(BUG));
errors.append(CheckMetric(METRIC));

writer.AddStartTag(INITIALDETAILS);
writer.AddBugInstance(BUG);
writer.AddMetric(METRIC);
writer.AddSummary;
writer.AddEndTag;

writer->Close;
```
### DESCRIPTION
This module provides the ability to convert python data structures into Scarf formatted documents. It is dependant on the lxml library for writing.

The writer is controlled primarily by the user through method calls which feed data in to be written. This data is to be structured as a dict containing all fields for a specific section. For details on how the data structures are formatted see below.

The user has the ability to set the error level to 0 (none), 1 (warnings), or 2 (exit). Both error levels 1 and 2 will print all error messages found during writing including misformatted elements, required elements not found, wrong value types, and ordering violations. Error level 2 will also exit the program as soon as an error is found to ensure validity of results.

### METHODS
#### new(FILE [, ERRORLEVEL, PRETTYENABLE])
This is a class method used to instantiate the writer. FILE is an object blessed into an IO::Handle, an open file handle, a string file path or a reference to a string to be written to. ERRORLEVEL is 0, 1, or 2 and sets the error level of the writer (default 2). PRETTYENABLE will enable or disable pretty printing of output with a true or false value respectively (default true).

#### GetWriter
Returns the  used for writing.

#### GetHandle
Returns the set handle.

#### GetErrorLevel
Returns the current error level of the writer.

#### SetErrorLevel(ERRORLEVEL)
Allows changing the error level of the writer to 0 (none), 1 (print warnings), or 2 (print warnings and throw exception). If ERRORLEVEL does not equal one of these values does nothing.

#### GetPretty 
Returns the current value set to pretty print.

#### CheckStart(INITIALDATA)
Check for any errors in INITIALDATA structure. Returns all error messages in an array or an empty array if there are none. For details on valid data structures see below.
#### CheckBug(BUGDATA)
Check for any errors in BUGDATA structure. Returns all error messages in an array or an empty array if there are none. For details on valid data structures see below.
#### CheckMetric(METRICDATA)
Check for any errors in METRICDATA structure. Returns all error messages in an array or an empty array if there are none. For details on valid data structures see below.

#### SetPretty(PRETTYENABLE)
If PRETTYENABLE is a true value then enable pretty printing, else disable pretty printing.

#### AddStartTag(INITIALDATA)
Writes a start tag to the file based on INITIALDATA. For details on valid data structures see below. Must be called exactly once before other 'Add' methods below.

#### AddBugInstance(BUGDATA) 
Writes a bug to the file based on BUGDATA. For details on valid data structures see below. May be called 0 or more time and inter-layered with 'AddMetric' calls. Not allowed after 'AddSummary' and 'AddEndTag'.

#### AddMetric(METRICDATA)
Writes a metric to the file based on METRICDATA. For details on valid data structures see below. May be called 0 or more time and inter-layered with 'AddBug' calls. Not allowed after 'AddSummary' and 'AddEndTag'.

#### AddSummary
Writes a summary to the file based on all bugs and metrics already written with this writer. May be called 0 or more times before 'AddEndTag'.

#### AddEndTag
Writes an end tag to the file. Must be called exactly once after which no other 'Add' methods may be called.


### DATA STRUCTURES


The following are the data structures used in the methods listed above. Undefined keys will not be written. 

#### INITIALDATA
InitialData contains information regarding the tool used to test the package. All fields in this structure are required elements therefore must be included in the data structure.
```
{
    tool_name => TOOLNAMEVALUE,            #REQUIRED
    tool_verison => TOOLVERSIONVALUE,      #REQUIRED
    uuid => UUIDVALUE                     #REQUIRED
}
```

#### BUGDATA
BugData contains information on one BugInstance from the SCARF file. All items listed as required should always be present in the data structure. Other items listed are not required, but can be included and written to SCARF.
```
{                          
    BugGroup => GROUPVALUE,
    BugCode => CODEVALUE,
    BugMessage => BUGMESSAGEVALUE,                 # REQUIRED
    BugRank => BUGRANKVALUE,
    BugSeverity => SEVERITYVALUE,
    ResolutionSuggestion => RESOLUTIONSUGGESTIONVALUE,
    AssessmentReportFile => ASSESSREPORTVALUE,    # REQUIRED
    BuildId => BUILDIDVALUE,                       # REQUIRED
    InstanceLocation => {
        Xpath => XPATHVALUE, 
        LineNum => { 
            Start = STARTVALUE,                    # REQUIRED
            End = ENDVALUE                         # REQUIRED
        } 
    }, 
    CweIds => [ 
        CWEIDVALUE, CWEIDVALUE, CWEIDVALUE 
        ], 
    ClassName => CLASSVALUE,
    Methods => [ 
        { 
            name => METHODNAMEVALUE,               # REQUIRED
            primary => PRIMARYVALUE                # REQUIRED
        },
        {
            name => METHODNAMEVALUE,
            primary => PRIMARYVALUE
        } 
    ],
    BugLocations => [                              # REQUIRED
        {
            SourceFile => SOURCEVALUE,             # REQUIRED
            StartLine => STARTLINEVALUE,
            EndLine => ENDLINEVALUE,
            StartColumn => STARTCOLVALUE,
            EndColumn => ENDCOLVALUE,
            primary => PRIMARYVALUE,               # REQUIRED
            Explanation => EXPLANVALUE,
        } 
    ], 
}
```

#### METRICDATA
MetricData contains information on one Metric from the SCARF file. All items listed as required should always be present in the data structure. Other items listed as are not required, but can be written to SCARF.
```
{
    Value => VALUE,              # REQUIRED       
    Type => TYPEVALUE,           # REQUIRED
    Method => METHODVALUE,       # OPTIONAL
    Class => CLASSVALUE,         # OPTIONAL
    SourceFile => SOURCEVALUE,   # REQUIRED
}
```
