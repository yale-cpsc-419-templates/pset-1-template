# CPSC 4190: Command-line Application

## Due Friday Oct 2 10:59 PM NHT (New Haven Time)

## Table of Contents

- [CPSC 4190: Command-line Application](#cpsc-419-command-line-application)
  - [Due Friday Oct 2 10:59 PM NHT (New Haven Time)](#due-friday-Oct-2-1159-pm-nht-new-haven-time)
  - [Table of Contents](#table-of-contents)
  - [Purpose](#purpose)
  - [Rules](#rules)
  - [Getting Started](#getting-started)
  - [Your Task](#your-task)
  - [The Database](#the-database)
    - [Database Schema](#database-schema)
    - [English Description](#english-description)
  - [The `reg.py` Program](#the-regpy-program)
    - [Output Requirements](#output-requirements)
    - [Filtering Requirements](#filtering-requirements)
    - [Sample Output for `reg.py`](#sample-output-for-regpy)
  - [The `regdetails.py` Program](#the-regdetailspy-program)
    - [Detail Sections](#detail-sections)
    - [Sample Output for `regdetails.py`](#sample-output-for-regdetailspy)
  - [Source Code Guide](#source-code-guide)
  - [Input Specification](#input-specification)
  - [Error Handling](#error-handling)
  - [Testing](#testing)
    - [Boundary Testing](#boundary-testing)
    - [Statement Testing](#statement-testing)
    - [Test Automation](#test-automation)
    - [Unit Testing](#unit-testing)
  - [Program Style](#program-style)
  - [Advice](#advice)
  - [Submission](#submission)
  - [**_Submit your assignment solution to Canvas as a link to that release._**](#submit-your-assignment-solution-to-canvas-as-a-link-to-that-release)
    - [Late Submissions](#late-submissions)
    - [Grading](#grading)

## Purpose

The purpose of this assignment is to help you learn or review database programming in Python.
If done properly, the assignment also will give you practice at composing modular code.

## Rules

You must work on this assignment as a group of two students.
If you have not found or been assigned a partner by the time you read this, please let an instructor know.

It must be the case that either you submit all of your team's files or your teammate submits all of your team's files.
(It must not be the case that you submit some of your team's files and your teammate submits some of your team's files.)
Your `README` file and your source code files must contain your name and your teammate's name.
See below in the [Submission](#submission) section for details on what and how to submit.

## Getting Started

> **Note**: this section contains exactly the text on the Canvas assignment, reproduced here only for completeness of this document.
> Since you made it here, you can safely ignore this section.

To get started, you must follow the steps below to successfully set up you group and download the template files.

1. Register your biweekly pset group on Canvas.
    * You can do this by going to People -> Groups and joining a group named "Pset Partnership #" ([guide](https://guides.instructure.com/m/4212/l/64913-how-do-i-join-a-group-as-a-student)).
        * Make sure that both you and your partner are in the same group!
        * **Even if you work alone, sign up to a group.**
    * This step links your group to the canvas assignment.
    * Once this step is finished, only one of you will submit the assignment!

2. Accept the GitHub Classroom assignment (to get here, you must have already done this).
    * GitHub classroom will yet again ask you to create a team, whether you will be working in pairs or individually.
        * **Name this team identically to your Canvas group**, *e.g.* "Pset Partnership 13".
    * Let your other team member join your team.
    * This step creates a GitHub repository for your team and links your team members' GitHub ids.
        * If you do not have a GitHub account, you are required to create one for this course
    * Use this git repository to track your assignment development.
    * If you have never used Git or GitHub before, or if you'd simply like some additional experience, complete the Git/GitHub Starter Assignment (linked from Canvas). There is no due date and it is not graded.

3. The `reg.sqlite` database file is included in your GitHub repository, so no separate download or setup is required.

## Your Task

Any university registrar's office must maintain data about courses and classes offered during the upcoming semester. Those data typically are kept in a database. The registrar's office must provide an interface that allows students and other interested parties to query the database.

Assume that you are working for Yale's Registrar's Office. You are given a database containing data about courses and classes offered during this semester at Yale. Your task is to compose Python programs that allow Yale students and other interested parties to query the database.

For this assignment your programs must have simple *textual* interfaces.
The next assignments will ask you to enhance your programs in a few ways, perhaps most notably such that they have *graphical* user interfaces.
So it will be to your advantage to modularize your code so you easily can replace the textual interfaces with a graphical one.

## The Database

The database is a SQLite database that is stored in a file named `reg.sqlite`.

### Database Schema

The database consists of these tables and fields:

| Table | Fields |
| --- | --- |
| `courses` | `courseid`, `subjectcode`, `coursenum`, `deptcode`, `title`, `descrip`, `prereqs` |
| `departments` | `deptcode`, `deptname` |
| `sections` | `crn`, `courseid`, `sectionid`, `sectionnumber` |
| `meetings` | `crn`, `timestring`, `locstring` |
| `crosslistings` | `primarycourseid`, `secondarycourseid` |
| `profs` | `profid`, `profname` |
| `coursesprofs` | `courseid`, `profid` |

### English Description

These are the relationships among the tables:

**`courses` and `departments`.**

For each row of the `courses` table with `deptcode` x, there is exactly 1 row of the `departments` table with `deptcode` x.

For each row of the `departments` table with `deptcode` x, there are 0 or more rows of the `courses` table with `deptcode` x. (Some departments offer no courses. Some departments offer many courses.)

**`courses` and `sections`.**

For each row of the `courses` table with `courseid` x, there are 0 or more rows of the `sections` table with `courseid` x. (Some courses don't have a corresponding section; that is, a particular course might not be offered this semester. Some courses have 1 corresponding section. Some courses have more than 1 corresponding section.)

For each row of the `sections` table with `courseid` x, there is exactly 1 row of the `courses` table with `courseid` x. (Each section has exactly one corresponding course.)

**`sections` and `meetings`.**

For each row of the `sections` table with `crn` x, there are 0 or more rows of the `meetings` table with `crn` x. (Some sections are not yet scheduled. Some meet once per week (or on a regular schedule). Some meet several times per week on an irregular schedule.)

For each row of the `meetings` table with `crn` x, there is exactly 1 row of the `sections` table with `crn` x. (Each meeting is associated with exactly one section.)

**`courses` and `crosslistings`.**

For each row of the `courses` table with `courseid` x, there are 1 or more rows of the `crosslistings` table with `primarycourseid` x. (Some courses are listed once. Some courses are crosslisted in several subjects (or schools!).)

For each row of the `crosslistings` table with `primarycourseid` x and `secondarycourseid` y, there is exactly one row of the `courses` table with `courseid` x and exactly one row of the `courses` table with `courseid` y.

**`courses` and `coursesprofs`.**

For each row of the `courses` table with `courseid` x, there are 0 or more rows of the `coursesprofs` table with `courseid` x. (Some courses don't have an assigned professor. Some courses have 1 professor. Some courses have more than one professor.)

For each row of the `coursesprofs` table with `courseid` x, there is exactly one row of the `courses` table with `courseid` x.

**`coursesprofs` and `profs`.**

For each row of the `coursesprofs` table with `profid` x, there is exactly 1 row of the `profs` table with `profid` x. (Each professor has exactly one name.)

For each row of the `profs` table with `profid` x, there are 0 or more rows in the `coursesprofs` table with `profid` x. (Some professors are teaching 0 courses. Some are teaching 1 course. Some are teaching more than one course.)

Your first step must be to familiarize yourself with the database.

> **Note:** Yale organizes courses by both department and subject. These two properties have a many-to-many relationship, that is, each subject belongs to potentially many departments and each department oversees potentially many subjects.

## The `reg.py` Program

You must compose two programs, the first of which must be named `reg.py`.
This program will display in the console a table of courses filtered by department code, subject code, course number, and title.

When executed via a command such as `python reg.py -h`, your program must display the following help message:

```text
usage: reg.py [-h] [-d deptcode] [-s subjectcode] [-n num] [-t title]

options:
  -h, --help      show this help message and exit
  -d deptcode     show only those classes whose department code contains dept
  -s subjectcode  show only those classes whose subjectcode contains subject
  -n num         show only those classes whose course number contains num
  -t title       show only those classes whose course title contains title
```

> **Hint**: Use the standard Python `argparse` module with the `allow_abbrev=False` option.
> The required help message is the default behavior of that module.
> The help message may differ slightly based on your platform or Python version; we are looking for the default behavior of `argparse`.

### Output Requirements

The output of your program must be a tabular display of course information containing the following columns:

* `deptname`
* `subjectcode`
* `coursenum`
* `title`
* `crns`

The `deptname` column contains the department name from the `departments` table.

The `subjectcode`, `coursenum`, and `title` columns each map directly to a field in the database `courses` table; their value matches exactly the corresponding value in the database, wrapped to the width of the column.

The `crns` column contains a list of the crn of each section associated with the course.
There is one crn per line.

The courses displayed in the table must be sorted first by `deptcode` in ascending order, then by `subjectcode` in ascending order, then by `coursenum` in ascending order, and finally by `title` in ascending order.
The list of `crns` must be sorted in increasing order.

The output must have the appearance of a table, similar to the view produced by the `sqlite3` command-line tool when a query is executed.

* Each column must have a header row, with the following headers:
    1. "deptname"
    2. "subjectcode"
    3. "coursenum"
    4. "title"
    5. "crns"
* The header row must be visually separated by at least one line from the rest of the table
* Columns of the table must be visually separated by at least 1 character
* No line of output may be more than 100 characters long, or the width of the terminal (whichever is smaller)
    > **Hint**: use the `shutil.get_terminal_size()` function to query the size of the terminal
* The program output should never exceed 1000 courses
* The output of the program, when run with no arguments, must be the first 1000 courses in the database according to the sorting orders specified above.

> **Note**: Precisely formatting textual output as a table is tedious and in this particular case requires some pretty intricate code.
> Since the goal of this assignment is to teach you how to interact with a database and not how to write sneaky formatting code, we have provided for your use a module named `table.py` that you may use to format your table output.
> You do not have to do so, and you may find it easier to "roll your own" or to leverage a third-party package from the Python community.
> (As a reminder, if you use a package that is not part of the Python Standard Library, you must cite it.)

---

> **Note**: Each *row* of the displayed table might span multiple *lines* of output if, for example, the course's title is very long or if there are many sections associated with the course.
> The provided `table.py` uses the `textwrap` module from the Python standard library to handle this, and it is recommended that you also do so if you choose not to use `table.py`.
> If there is a row of the table that spans multiple lines of output, every line break in that row must occur at a word boundary, and never in the middle of a word, unless the width is so restricted as to render this impossible.

### Filtering Requirements

The program must accept any combination of the `-d`, `-s`, `-n`, and `-t` arguments, each appearing at most once.

| Argument | Meaning |
| --- | --- |
| `-d deptcode` | Include courses whose `deptcode` contains the supplied value. |
| `-s subjectcode` | Include courses whose `subjectcode` contains the supplied value. |
| `-n num` | Include courses whose `coursenum` contains the supplied value. |
| `-t title` | Include courses whose `title` contains the supplied value. |

If multiple arguments are supplied, `reg.py` must combine the filters using `AND`.
For example, when both `-s` and `-n` are supplied, the output must contain courses matching both criteria.

Filters must be case-insensitive: `-t web` must match a title such as "Full Stack Web Programming".
Filters must preserve leading and trailing whitespace: `-t 'ing '` must match "Embodying Story" but not "Full Stack Web Programming".

> **Note**: Separate words on a UNIX command line are separate arguments unless surrounded by quotes.
> The argument list `-t full stack` is invalid and your program does not need to handle it.
> The argument list `-t 'full stack'` is valid and must match titles containing that phrase.

### Sample Output for `reg.py`

```text
$ python reg.py -d cpsc -n 4
deptname                subject num  title                                 crns
----------------------- ------- ---- ------------------------------------- -----
Computer Science (CPSC) CPSC    3340 Creative Embedded Systems             10858
Computer Science (CPSC) CPSC    3640 Decentralized Payments, Contracts,    11568
                                     and Finance for Humans and AI              
Computer Science (CPSC) CPSC    4190 Full Stack Web Programming            10859
Computer Science (CPSC) CPSC    4260 Building Distributed Systems          14656
Computer Science (CPSC) CPSC    4310 Computer Music: Algorithmic and       10855
                                     Heuristic Composition                      
Computer Science (CPSC) CPSC    4370 Database Systems                      10829
Computer Science (CPSC) CPSC    4371 Database Design and Implementation    10830
Computer Science (CPSC) CPSC    4380 Big Data Systems: Trends & Challenges 13435
Computer Science (CPSC) CPSC    4390 Software Engineering                  10836
Computer Science (CPSC) CPSC    4410 Verifiable, Private, Decentralized    10820
                                     Computing in the Age of AI                 
Computer Science (CPSC) CPSC    4420 Theory of Computation                 13431
Computer Science (CPSC) CPSC    4480 Silicon Compilation                   12841
Computer Science (CPSC) CPSC    4540 Software Analysis and Verification    10824
Computer Science (CPSC) CPSC    4550 Algorithmic Game Theory               11608
Computer Science (CPSC) CPSC    4570 Sensitive Information in a Connected  10821
                                     World                                      
Computer Science (CPSC) CPSC    4585 Probabilistic Programming             14957
Computer Science (CPSC) CPSC    4590 Building Interactive Machines         10831
Computer Science (CPSC) CPSC    4610 Statistics and Learning Theory for    14653
                                     Computer Scientists                        
Computer Science (CPSC) CPSC    4640 Algorithms and their Societal         10832
                                     Implications                               
Computer Science (CPSC) CPSC    4670 Introduction to Cryptography          10822
Computer Science (CPSC) CPSC    4690 Randomized Algorithms                 10817
Computer Science (CPSC) CPSC    4710 Trustworthy Deep Learning             13427
Computer Science (CPSC) CPSC    4720 Intelligent Robotics                  10827
Computer Science (CPSC) CPSC    4740 Computational Intelligence for Games  10840
Computer Science (CPSC) CPSC    4750 Computational Vision and Biological   13428
                                     Perception                                 
Computer Science (CPSC) CPSC    4791 Building Game Engines                 10861
Computer Science (CPSC) CPSC    4800 Introduction to Computer Vision       10834
Computer Science (CPSC) CPSC    4844 Geometric and Topological Methods in  10877
                                     Machine Learning                           
Computer Science (CPSC) CPSC    4880 Advances in Frontier AI Models        10818
Computer Science (CPSC) CPSC    4900 Senior Project                        10843
Computer Science (CPSC) CPSC    5400 Decentralized Payments, Contracts,    16426
                                     and Finance for Humans and AI              
Computer Science (CPSC) CPSC    5410 Verifiable, Private, Decentralized    10867
                                     Computing in the Age of AI                 
Computer Science (CPSC) CPSC    5420 Theory of Computation                 14690
Computer Science (CPSC) CPSC    5540 Software Analysis and Verification    14686
Computer Science (CPSC) CPSC    5640 Algorithms and their Societal         10887
                                     Implications                               
Computer Science (CPSC) CPSC    5740 Computational Intelligence for Games  10900
Computer Science (CPSC) CPSC    6440 Geometric and Topological Methods in  10875
                                     Machine Learning                           
Computer Science (CPSC) CPSC    6940 MS Thesis Research II                 10897
Computer Science (CPSC) CPSC    7430 Topics in Flow-Based Generative       15527
                                     Modeling and Optimal Transport             
```

```text
$ python reg.py -d chem -n 600
deptname         subject num  title                          crns 
---------------- ------- ---- ------------------------------ -----
Chemistry (CHEM) CHEM    6000 Research Seminar               13753
                                                             13754
                                                             13755
                                                             13756
                                                             13757
                                                             13758
                                                             13759
                                                             13760
                                                             13761
                                                             13762
                                                             13763
                                                             13764
                                                             13765
                                                             13768
                                                             13769
                                                             13770
                                                             13771
                                                             13772
                                                             13773
                                                             13774
                                                             13775
                                                             13776
                                                             13777
                                                             13778
                                                             13779
                                                             13780
                                                             13781
                                                             13782
                                                             13783
                                                             13784
                                                             13785
                                                             13786
                                                             13787
                                                             13788
                                                             13789
                                                             13790
                                                             13791
                                                             13792
                                                             14754
```

```text
$ python reg.py -t 'full stack'
deptname                subject num  title                      crns
----------------------- ------- ---- -------------------------- -----
Computer Science (CPSC) CPSC    4190 Full Stack Web Programming 10859
Computer Science (CPSC) CPSC    5190 Full Stack Web Programming 10902
```

## The `regdetails.py` Program

Your second program must be named `regdetails.py`.
This program takes the `crn` of a section and prints details about its course, including the department, subject, course number, title, description, prerequisites, meeting information, crosslistings, and professors.
If the `crn` does not identify a section in the database, your program must display a meaningful message and exit.

When executed via a command such as `python regdetails.py -h`, your program must display the following help message:

```text
usage: regdetails.py [-h] crn

positional arguments:
  crn         the crn of the class whose details should be shown

options:
  -h, --help  show this help message and exit
```

> **Hint**: Use the standard Python `argparse` module.

### Detail Sections

The output of your `regdetails.py` program must be divided into several sections, each separated from the previous one by a single blank line.
The header for a section must be on its own line.
Those sections are:

1. A table (with the same format as the table you built in `reg.py`) with the following columns: `deptcode`, `deptname`, `subjectcode`, and `coursenum`.
   Each must hold the value of the field in the database associated with the course that has the `courseid` matching the `courseid` of the section with the provided `crn`.

2. A section labeled `title`, containing the course title from the `courses` table.

3. A section labeled `descrip`, containing the course description from the `courses` table, or the string `None` if there is no description.

4. A section labeled `prereqs`, containing the course prerequisites from the `courses` table, or the string `None` if there are no prerequisites.

5. A table (with the same format as the table you built in `reg.py`) with three columns: `sectionnumber`, `crn`, and `meetinginfo`. The `sectionnumber` and `crn` columns should hold values verbatim from the database (from the fields with those names) for the `crn` provided.
    There must be one entry in the `meetinginfo` column for each row in the `meetings` table for the section with the provided `crn`. The entries must each have the format `<timestring>@<locstring>` and be listed one per line.

6. A table (with the same format as the table you built in `reg.py`) with two columns: `subjectcode` and `coursenum`.
   The table must contain the `subjectcode` and `coursenum` of each course associated with the `courseid` of the provided `crn` in the `crosslistings` table.

7. A section labeled `professors`, containing the professors for this course.
   It contains zero or more lines consisting of the names of the professors of the course&mdash;one per line.

> **Hint**: The `title`, `descrip`, `prereqs`, and `professors` sections can each be treated as a single-column table, if your table output functions support such things (a correct implementation of `table.py` does support it).

> **Note**: The description and prerequisites strings for some courses in the database contain HTML-like tags.
> They must be included verbatim in your output.

> **Note**: As with your output from `reg.py`, the width of the output of `regdetails.py` must not exceed the smaller of 100 characters or the width of the terminal.

### Sample Output for `regdetails.py`

```text
$ python regdetails.py 10859
deptcode deptname                subjectcode coursenum
-------- ----------------------- ----------- ---------
CPSC     Computer Science (CPSC) CPSC        4190     

title                     
--------------------------
Full Stack Web Programming

descrip                                                                         
--------------------------------------------------------------------------------
<p>This course introduces students to a variety of advanced software engineering
and programming techniques in the context of full-stack web programming. The    
focus of the course includes both client- and server-side programming (and      
database programming), client/server communication, user interface programming, 
and parallel programming. This course is designed for students who have taken   
CPSC 223 (but do not need CPSC 323 or higher-level computer science systems     
courses) and wish to learn the complete programming framework of Web            
programming. For a systematic treatment of core software engineering techniques,
using Web programming as a running example framework, consider taking CPSC 439, 
which targets students with more extensive programming experiences (after CPSC  
323).</p>                                                                       

prereqs                                            
---------------------------------------------------
<p class="prerequisites">Prerequisite: CPSC 223</p>

sectionnumber crn   meetinginfo            
------------- ----- -----------------------
1             10859 MW 9.00-10.15 @ WTS A74
                    HTBA @ TBA             

subjectcode coursenum
----------- ---------
CPSC        4190     
CPSC        5190     

professors
----------
Alan Weide 
```

```text
$ python regdetails.py 13753
deptcode deptname         subjectcode coursenum
-------- ---------------- ----------- ---------
CHEM     Chemistry (CHEM) CHEM        6000     

title           
----------------
Research Seminar

descrip                                                                         
--------------------------------------------------------------------------------
<p>Presentation of a student’s research results to the student’s adviser and    
fellow research group members. Extensive discussion and literature review are   
normally a part of the series.</p>                                              

prereqs
-------
None   

sectionnumber crn   meetinginfo
------------- ----- -----------
1             13753 HTBA @ TBA 
2             13754 HTBA @ TBA 
3             13755 HTBA @ TBA 
4             13756 HTBA @ TBA 
5             13757 HTBA @ TBA 
6             13758 HTBA @ TBA 
7             13759 HTBA @ TBA 
8             13760 HTBA @ TBA 
9             13761 HTBA @ TBA 
10            13762 HTBA @ TBA 
11            13763 HTBA @ TBA 
12            13764 HTBA @ TBA 
13            13765 HTBA @ TBA 
14            13768 HTBA @ TBA 
15            13769 HTBA @ TBA 
16            13770 HTBA @ TBA 
17            13771 HTBA @ TBA 
18            13772 HTBA @ TBA 
19            13773 HTBA @ TBA 
20            13774 HTBA @ TBA 
21            13775 HTBA @ TBA 
22            13776 HTBA @ TBA 
23            13777 HTBA @ TBA 
24            13778 HTBA @ TBA 
25            13779 HTBA @ TBA 
26            13780 HTBA @ TBA 
27            13781 HTBA @ TBA 
28            13782 HTBA @ TBA 
29            13783 HTBA @ TBA 
30            13784 HTBA @ TBA 
31            13785 HTBA @ TBA 
32            13786 HTBA @ TBA 
33            13787 HTBA @ TBA 
34            13788 HTBA @ TBA 
35            13789 HTBA @ TBA 
36            13790 HTBA @ TBA 
37            13791 HTBA @ TBA 
38            13792 HTBA @ TBA 
39            14754 HTBA @ TBA 

subjectcode coursenum
----------- ---------
CHEM        6000     

professors          
--------------------
Allison Didychuk    
Amymarie Bartholomew
Andrew Miranker     
Anna Marie Pyle     
Caitlin Davis       
Corey O'Hern        
Craig Crews         
David Spiegel       
E. Chui-Ying Yan    
Eric Arsenault      
Gregory Craven      
Hailiang Wang       
Hongying Shen       
J Patrick Loria     
James Mayer         
Jason Crawford      
Jonathan Ellman     
Julie Zimmerman     
Kathryn Ferguson    
Krystal Pollitt     
Lisa Pfefferle      
Mark Johnson        
Matthew Simon       
Nilay Hazari        
Patrick Holland     
Patrick Vaccaro     
Paul Anastas        
Sarah Slavoff       
Scott Miller        
Seth Herzon         
Stacy Malaker       
Stavroula Hatzios   
Stephen Strittmatter
Tianyu Zhu          
Timothy Newhouse    
Victor Batista      
W. Mark Saltzman    
Yang Liu 
```

## Source Code Guide

Here are the *requirements* for the source code of your solution.
* The program must communicate with a SQLite database in a file named `reg.sqlite`, organized as described above.
* The program must use SQL prepared statements for every database query. (This protects the database against SQL injection attacks.)
* Every invocation of the program must use exactly one `cursor` object
    * Note that this implies that it must also use exactly one database `connection` object!
* Every module used by your program must either be from the Python standard library or written by your team (and included in your submission). The only exception to this is the provided `table.py` file that is partially completed (you may use this file). Here are some recommended standard library modules to make your life easier:
    * `sqlite3` for communicating with the database
    * `argparse` for handling command-line arguments
    * `textwrap` for formatting output
    * `itertools` for simplifying some parts of your code

In addition to the functional requirements for your source code, there are additional stylistic requirements on which your program will be partially graded.

* Modularize your code extensively so that your "main" function looks something like this:
```python
def main():
    filters = get_filter_terms()
    courses = get_filtered_courses(filters)
    output_courses(courses)
```
* Encapsulate database code and print statements inside modules that can be replaced if you decide to display courses in a different fashion or retrieve the data from a different source.

> **Hint**: Study the provided `table.py`, implement the unimplemented functions, and use that module in your solution.

## Input Specification

You may assume the users of your `reg.py` and `regdetails.py` programs are acting generally "in good faith". In particular, you may assume...

* The user will only ever provide arguments at the command line that conform to the allowed arguments:
    * The only provided arguments will be `-h`, `-d deptcode`, `-s subjectcode`, `-n num`, and `-t title` for `reg.py`
    * The only provided argument will be a single positional argument or the `-h` flag for `regdetails.py`
* The database exists in a file named `reg.sqlite` and is well-formed according to the database specification above

However, you may *not* assume...

* That there are any courses at all in the database
* That the single argument to `regdetails.py` is a numeric argument
* That the single argument to `regdetails.py`, even if it is numeric, corresponds to a section in the database

## Error Handling

Despite the assumptions you may make about input, your `reg.py` and `regdetails.py` should be reasonably robust.
Since we haven't discussed error handling in this course, whatever previous experience you have handling errors will be sufficient for this assignment.
Keep in mind that as we progress through the course, error handling will become more important (and the specification of program input more relaxed!).

The following recommendations are provided only for your benefit of cases to consider as potentially program-breaking.
We will not test your program on such inputs, but we encourage you to design your solution to handle them nonetheless.

Your `reg.py` could handle erroneous command-line arguments "gracefully". These commands illustrate:
```
$ python reg.py s qr
$ python reg.py "-s " qr
$ python reg.py -d qr st
$ python reg.py -s
$ python reg.py -n qr -t
$ python reg.py -t -s cpsc
$ python reg.py -x
```

Your `regdetails.py` could handle erroneous command-line arguments "gracefully". These commands illustrate:
```
$ python regdetails.py
$ python regdetails.py 13753 10859
$ python regdetails.py abc123
$ python regdetails.py 5718394.7298
```

Your `reg.py` and `regdetails.py` could handle "database cannot be opened" errors.
If the database cannot be opened, then your programs could write a descriptive error message&mdash;the one contained within the thrown Exception object&mdash;to its `stderr`.

Your `reg.py` and `regdetails.py` also could handle "corrupted database" errors.
In the context of this course, a corrupted database is one that causes the database driver, upon interaction with the database, to throw an exception.
In the case of SQLite, a corrupted database might consist of an invalid SQLite file&mdash;for example, a file that's empty or contains simple text.
More generally, a corrupted database might be a database that is missing a table that the interaction requires, or is missing a column/field that the interaction requires.
If the database is corrupted such that the SQLite driver's execution of a `SELECT` statement throws an exception, then your program could write a descriptive error message&mdash;the one contained within the thrown Exception object&mdash;to its `stderr`.

> **Note**: Good question from a student during a recent semester:
>
> Suppose the user runs `regdetails.py` for a particular `crn`, the program queries the `sections` table to find the corresponding `courseid`, and the program then queries the database to fetch the row in the `courses` table with that course ID.
> Furthermore, suppose the database is corrupted such that no row with that course ID exists in the courses table.
> Should our `regdetails.py` handle that particular database corruption error?
>
> **Answer**: No. Generalizing...
>
> Database management systems can enforce *foreign key* integrity constraints on the databases that they manage.
> So any DBMS would not allow its databases to contain the kind of violation that the student described.
> And so client programs would not need to check for such violations.
>
> That is a very good thing.
> With a sufficiently rich database, it is very difficult for a client program systematically to check for foreign key integrity constraint violations.
> And it would be absurdly redundant for *every* client program to check for such violations.
>
> All of this is to say that it's more realistic to compose your `reg.py` and `regdetails.py` such that they don't check for foreign key integrity constraint violations.
> And so it's fine to compose your programs such that they check for only the kinds of database-related errors that are described in this specification: file-level errors and schema-level errors.
> The first easily could happen if the `reg.sqlite` file is missing from the working directory.
> The second could happen if, for example, the `reg.sqlite` file is present but empty.

## Testing

We'll take a (slightly) more systematic approach to software testing techniques in lectures later in the semester.
In the meantime, to test your programs it will be sufficient to rely upon (1) your knowledge of testing from your previous experience, and (2) this [A Software Testing Taxonomy](docs/TestingTaxonomy.pdf) document, courtesy of Princeton University.

Test your `reg.py` and `regdetails.py` programs by (1) reviewing this assignment specification thoroughly, making sure that your programs conform to every aspect of it, and (2) comparing the behavior of your program with the example outputs given in this document.

### Boundary Testing

Focus on *boundary* (alias *corner case*) testing. Of course, make sure that your programs handle normal data. But also make sure that your programs handle unusual data: courses that have multiple cross-referenced departments/numbers, long titles, long descriptions, multiple professors, no professors, and so forth.

### Statement Testing

Next, focus on statement (alias coverage) testing.
Your tests should cause every statement of your `reg.py` and `regdetails.py` to be executed.

You're encouraged, but not required, to use the Python `coverage` tool to generate a coverage report showing which lines of your programs have and have not been executed by your tests. These are the steps:

1. Repeatedly issue commands of the form `python -m coverage run -p reg.py arguments`.
    Each of those commands runs your `reg.py` with the specified arguments, and generates a coverage report in a file named `.coverageX` (for some `X`).
    The report indicates which lines of your `reg.py` were executed and which were not.

1. Repeatedly issue commands of the form `python -m coverage run -p regdetails.py argument`.
    Each of those commands runs your `regdetails.py` with the specified argument, and generates a coverage report in a file named `.coverageX` (for some `X`).
    The report indicates which lines of your `regdetails.py` were executed and which were not.

1. Issue the command `python -m coverage combine` to combine the coverage reports generated by steps 1 and 2 into one large coverage report in a file named `.coverage`.
1. Issue the command `python -m coverage html` to use the .coverage file to generate a human-readable report as a set of HTML documents in a directory named `htmlcov`.
1. Browse to `htmlcov/index.html` to check the report.
1. The files in your `htmlcov` directory should show that 100% of your programs' lines were executed.
    If the report doesn't show 100% coverage, then we recommend that you revise your testing plan accordingly, delete the `.coverage` file and the `htmlcov` directory, and repeat steps 1 through 5.

### Test Automation

You are encouraged, but not required, to automate your testing.
Automating your testing could reduce the amount of typing that you must do, and also could increase the quality of your programs.
It's also likely to save you time in future assignments to have some automated test scaffolding built early on.

To automate your testing of `reg.py` you might compose a program named, say, `testreg.py`.

At the core of the `testreg.py` program might be function calls of the form `os.system('somecommand')`.
Each such function call would execute `somecommand`, just as if `somecommand` were entered as a command at a shell prompt.
For example, the function call `os.system('python reg.py -t web')` would execute the command `python reg.py -t web`.

You might design your `testreg.py` program to execute `reg.py` multiple times with various command-line arguments.
You then might:

Run `testreg.py` using your `reg.py` and capture the output in a file (`python testreg.py > out1 2>&1`).
Manually inspect the database to determine what the correct output *should* be, and type that in a file called, *e.g.*, `outc`.
Compare the two output files via a `diff` command (`diff -y out1 outc`).
The contents of the two output files should be the same, except for the name of the program within error messages.
You might automate your testing of `regdetails.py` in a similar way.

Incidentally, if you're clever you could use your `testreg.py` and `testregdetails.py` programs to generate your coverage report.

### Unit Testing

The kind of testing described above, in which the entire program is repeatedly run and the output compared to correct output, is called *system testing*.
You probably will notice that generating correct output files against which to compare your program's output is quite challenging and error-prone.
A more scalable method of testing is called *unit testing*.

In unit testing, rather than testing the behavior of the project as a whole, each "unit" (*e.g.* function) is tested in isolation.
By doing so, correctness can be assessed with much finer granularity than simply running the whole program.
The beauty of unit testing is that if every individual function is correct, then the entire program is correct (because if you followed Python convention, the entire program is enclosed in an single function, `main()`)!

The challenge of unit testing is: how do you know what a particular function should do, and how do you check that it's right?
The answer is to write many small functions with well-defined semantics.
This method of designing software has the consequence that if you intend to unit test, your functions should be designed such that you can check their execution for correct behavior.
This is an additional level of design for software beyond the purely operational, so we encourage you to think about this and set aside some additional design time for your psets to enable unit tests.

Unit testing will be covered briefly later in the semester; for now, it is sufficient to read a blog post or two and study the documentation for the Python `unittest` module.

## Program Style

Your programs must be well styled.
Generally, by consensus of the Python community, good Python style is defined by the [PEP 8 -- Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/) website.

The Python community has developed a static code analysis tool named `pylint`, installable via `$ pip install pylint`.
The pylint tool (configured with a `.pylintrc` file) generates a report critiquing the style of given Python code.
The pylint tool enforces many of the PEP 8 guidelines, and some additional guidelines too.
Although the lecture example programs and the scaffolding programs mostly cause pylint to generate perfect reports, they sometimes do not.
Your assignment programs, however, must generate a good report (you may leave a few intentional or unavoidable exceptions&mdash;a score above 9.00 is fine and the report should be free of easy-to-fix issues such as "trailing whitespace").
Part of your grade on this assignment is the score from pylint when run in the default configuration with all of your submitted files&mdash;including the scaffolding file(s)!&mdash;rounded *up* to the nearest integer.

Using pylint is easy.
You can critique one file comprising your programs at a time:

```shell
python -m pylint reg.py
python -m pylint regdetails.py
...
```

However, a better approach is to critique all files comprising your programs at the same time:
```shell
python -m pylint reg.py regdetails.py ...
```
When given multiple files, pylint performs some cross-file critiquing.
So the "all files at the same time" approach may generate warnings in addition to those generated by the "one file at a time" approach.
Make sure you use the "all files at the same time" approach, as your grader will.

## Advice

The command-line format of `reg.py` is incomplete in the logical sense.
Most notably the format doesn't allow the user to express `OR` relationships in queries.
For example, the format doesn't allow the user to express queries of the form "Display data for all classes whose subject is 'chem' OR 'biol'." or "Display data for all classes whose dept is 'cpsc' OR whose subject is 'eeng'."
Don't be concerned about that.
Remember that the purpose of the assignment is not to develop a great registrar's office application *per se*.
Instead its purpose is to give you experience with database programming&mdash;experience that, we hope, will help you when developing your project, and beyond.

Modularize your code as much as you can.
In particular, isolate the user-interface-related code and the database-related code in distinct modules.
Good modularity will help you to reuse your code from this assignment in the next assignment.

Make sure that the output of your `reg.py` conforms to the specified format.
If it doesn't, then your program will create more work for our graders, putting them in a bad mood.
The same goes for your `regdetails.py`.

## Submission

Rename this file `TEMPLATE_README.md` and replace it with a new `README.md` file.
Your new `README` file must contain:

* Your name and netid and your teammate's name and netid, at the beginning of the file
* A paragraph describing your contribution, and another paragraph describing your teammate's contribution
    * Please be thorough; we are looking for two substantial paragraphs, not a sentence or two
* A description of whatever help (if any) you received from other people while doing the assignment
* A description of the sources of information that you used while doing the assignment, that are not direct help from other people
* An indication of how much time you spent doing the assignment, rounded to the nearest hour
* Your assessment of the assignment:
    * Did it help you to learn?
    * What did it help you to learn?
    * Do you have any suggestions for improvement? *Etc.*
* (Optionally) Any information that will help us to grade your work in the most favorable light
    * In particular, describe all known bugs and explain why any pylint style warnings you received are unavoidable or why you know better than pylint (a convincing argument may negate some pylint style penalties you accrue)

Your README file must be a plain text file: don't create it using Microsoft Word or any other word processor, although you are encouraged to format it using [markdown](https://www.markdownguide.org/).

Package your assignment files by [creating a release](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository#creating-a-release) on GitHub in your assignment repository.
There must be at least the following files with the following (exact) names in that repository when you submit it:

* `README.md`
* `reg.py`
* `regdetails.py`

Ensure that any additional files needed by your program (such as other Python modules) are in the repository snapshot captured by the release.
If&mdash;because you omitted some necessary files&mdash;the grader cannot run your program by downloading your release, you will be asked to resubmit for a substantial penalty.

> **Note**: If you have installed external packages, you must also include a file named `requirements.txt` containing the dependencies of your project.
> It can be created from your virtual environment by running the following command:
> ```
> $ pip freeze > requirements.txt
> ```
> 
> Failure to include a `requirements.txt` file if you use third-party packages will result in an automatic 10% penalty and a request that you submit an appropriate `requirements.txt` file to the graders.

---

**_Submit your assignment solution to Canvas as a [link to that release](https://docs.github.com/en/repositories/releasing-projects-on-github/linking-to-releases)._**

As noted above in the [Rules](#rules) section, it must be the case that either you submit all of your team's files or your teammate submits all of your team's files.
(It must not be the case that you submit some of your team's files and your teammate submits some of your team's files.)
You and your partner may submit multiple times; we will grade the latest files that you submit before the deadline *unless a particular version is requested as the canonical version*.

Please follow the rules on what to submit and how.
It will be a big help to us if you get the filenames right and submit exactly what's asked for.
Thanks.

## Late Submissions

The deadline for this assignment is **10:59 PM NHT (New Haven Time) on Oct 2, 2025**.
There is a strict 60-minute grace period beyond the deadline.

Late submissions will receive a 5% deduction for every 12-hour period (or part thereof) after the deadline and grace period.
After 48 hours, the Canvas assignment will close and submissions after that time will not receive any credit.

Except for submissions after the 48-hour deadline (*which are not accepted*), the timestamp on the commit associated with the linked release will determine what late penalties, if any, are applied.

## Grading

Your grade will be based upon:

* Correctness, that is, how closely your programs conform to the specifications in this document, from 0-12 (0-6 for each of `reg.py` and `regdetails.py`).
* Style, that is, the quality of your program style. This includes not only style as manually assessed by the graders (including modularity, cleanliness, and pythonicity) but also style as reported by the pylint tool. Style is graded from 0-10 for pylint and 0-6 for manual style assessment
* Performance. We do not enforce specific time bounds for queries. At this stage of the semester, we do not expect you to have optimized your queries&mdash;that will come later. You are however required to adhere to the [source code requirements](#source-code-guide) above, including always using prepared statements and using a single database cursor/connection. Performance is graded from 0-2 (0-1 for each of prepared statements and single cursor).

Your score in each category will be an **integer**&mdash;that is, you will not receive, for example, 5.25/6 for the correctness of your program.
The 0-6 grading scale for correctness and style has the following meaning:
* 6: The submission is excellent and exceeds expectations
* 5: The submission satisfies all or nearly all requirements
* 4: The submission satisfies a majority, but not nearly all, of the requirements
* 3: The submission satisfies about half of the assignments or slightly fewer
* 2-1: The submission satisfies hardly any of the requirements
* 0: The submission is unacceptable

> **Note**: The scale puts "satisfies all requirements" at 5/6.
> **You will not earn 100% on psets unless your work is exemplary.**
> You do not need to earn 100% on psets to earn an A in this course.

These three category scores will then be weighted as follows:
* Correctness: 60%
* Style: 20%
  * 10% pylint
  * 10% manual assessment
* Performance: 10%

If your code fails the tests on some particular functionality, your grader will inspect your code manually to try to assign partial credit for that functionality.
Partial credit will be given only if there is an *obvious* "quick fix" (*e.g.*, you have accidentally changed the name of the database file and your solution points to a file with a name that does not match the grader's copy of the database); if no such quick fix exists then no partial credit for that feature will be given.

---

Adapted from Assignment 1 for COS 333 &copy; 2021 by Robert M. Dondero, Jr., Princeton University

This version &copy; 2026 by Alan Weide, Yale University