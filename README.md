TILAK MAHARASHTRA VIDYAPEETH

Department of Computer Science
BACHELOR OF COMPUTER APPLICATIONS (BCA)
Data Science & Machine Learning
￼
EXPLORATORY DATA ANALYSIS ASSIGNMENT REPORT

Submitted By:
Name: ABHISHEK KUMAR RAJ 
PRN: 78625000111

Name: ASHWET JADHAV
PRN: 78625000295


Semester: III
Submitted To:
Mr Mayur Dutta
Department of Computer Science
Academic Year: 2026–27

Contents
1. Problem Statement	1
2. Dataset Information	1
3. Data Dictionary	2
4. Importing Libraries	4
5. Loading the Dataset	4
6. Initial Dataset Inspection	5
7. Data Quality Analysis	7
8. Data Preprocessing	17
9. Descriptive Statistics	19
10. Univariate Analysis	21
11. Outlier Analysis	24
12. Bivariate Analysis	28
13. Correlation Analysis	34
14. Multivariate Analysis	37
15. Group Comparison	40
16. Time-Based Analysis	42
17. Analytical Questions	43
18. Visualization Challenge	47
19. Before-and-After Analysis	49
20. Major Findings	52
21. Limitations	53
22. Final Data Story	54
23. Conclusion	55
1. Problem Statement -
Universities collect large amounts of information about their students – attendance, marks, grades, study habits, family background, well-being, internships and placement – but the raw records are rarely clean. This project investigates a 100,000-row student dataset that has been deliberately corrupted with the kinds of defects found in real administrative data: text in numeric fields, impossible values, spelling variants, blank and repeated records and contradictory entries.
The problem is to understand the data, assess and repair its quality without losing or inventing information, and then discover evidence-based patterns about what is associated with academic performance, stress and campus placement, while recognising what the data cannot support.
The analysis addresses the following guiding questions:
How is the data structured and what does each column mean?
What data-quality problems exist and how severe are they?
How are the important variables distributed and where are the outliers?
Which variables are related, in pairs and jointly?
Do groups (internship, scholarship, hostel, department, gender) differ in meaningful ways?
What factors accompany placement success?
How did cleaning change the conclusions, and what are the limits of the analysis?
2. Dataset Information -
The workbook Dataset.xlsx contains one sheet, university_student_eda_messy, with 100,000 rows and 43 columns. The sheet name already hints at the main feature of the data: it is deliberately messy. The records describe undergraduate students of Indian colleges in eight states and 25 cities, across seven departments and 20 programmes, observed between January 2021 and December 2026.
The 43 columns fall into seven logical groups, summarised below.
Table 2. Logical groups of columns
Group
Columns
Identity and contact (not analysed)
Student_ID, Student_Name, Email, Phone
Demographics and location
Age, Gender, City, State
Academic placement
Department, Programme, Academic_Year, Semester
Assessment and results
Attendance_Percent, Internal_Marks, Assignment_Marks, Quiz_Marks, End_Sem_Marks, SGPA, CGPA, Backlogs, Project_Score, Lab_Attendance_Percent
Study behaviour and engagement
Study_Hours_Per_Day, Library_Hours_Per_Week, LMS_Logins_Per_Week, Assignment_Submission_Rate, Certifications
Socio-economic and well-being
Scholarship_Status, Family_Income, Hostel_Status, Part_Time_Job, Travel_Time_Minutes, Sleep_Hours, Stress_Level, Extracurricular_Hours, Sports_Participation, Student_Satisfaction
Career outcomes
Internship_Status, Internship_Duration_Months, Placement_Preparation_Score, Placement_Status, Placement_Package_LPA, Record_Date

3. Data Dictionary -
The dictionary lists each of the 43 columns with its (cleaned) data type, meaning, unit or categories and an interpretation based on the evidence found in the data.
Table 3. Data dictionary of the 43 columns
Column Name
Data Type
Meaning
Unit / Category
I nterpretation
Student_ID
String (identifier)
Student identifier
STU + 6 digits (STU100000–STU121999)
Not unique per row: 21,729 distinct IDs over 99,950 rows (≈4.6 rows per ID), so a row is a student–term record, not a student. 995 IDs were typed in other formats.
Student_Name
String (identifier)
Name of the student
Free text, 700 distinct names
Personal data; the same ID always carries the same name. Excluded from analysis.
Age
Integer, discrete
Age of student
Years (valid 16–35)
Genuine values span 17–30; 0, –5, 12, 15, 45, 67, 99, 120, 999 are impossible for undergraduates.
Gender
Nominal categorical
Gender
Male / Female / Other
17 spellings reduced to 3 groups, each ≈ one third of records.
City
Nominal categorical
City of the student / institution
25 cities
87 spellings (typos such as “Hydrabad”, “Mumabi”) reduced to 25.
State
Nominal categorical
State of the city
8 states
32 spellings reduced to 8. Disagrees with City in 2,104 records.
Department
Nominal categorical
Academic department
7 departments
41 labels (IT, I.T., Info Tech …) reduced to 7.
Programme
Nominal categorical
Degree programme enrolled
20 programmes (BCA, B.Com, B.Tech …)
67 labels reduced to 20; programme and department disagree in 1,866 records.
Academic_Year
Ordinal (time period)
Academic year of the record
2021-22 … 2025-26
11 different formats (“21-22”, “2023_24”, “2022”) reduced to 5 years.
Semester
Ordinal, discrete
Semester of study
1–8
Text forms (“Second”, “1st”, “sem 3”) repaired; 0, –1, 9, 10, 12 are invalid for an 8-semester degree.
Attendance_Percent
Continuous
Class attendance
Percent (0–100)
Mean ≈ 63%; sentinel values –999, 999, 150 removed.
Internal_Marks
Continuous
Internal assessment marks
Marks out of 40
Strong ceiling: 59.8% of students score exactly 40.
Assignment_Marks
Continuous
Assignment marks
Marks out of 20
Extreme ceiling: 89.6% score exactly 20.
Quiz_Marks
Continuous
Quiz marks
Marks out of 20
Spread across the full range.
End_Sem_Marks
Continuous
End-semester exam marks
Marks out of 80
Right-skewed; median 26.
SGPA
Continuous
Semester grade point average
0–10 grade points
Mean 5.30; values 10.5–99 and negatives are invalid.
CGPA
Continuous
Cumulative grade point average
0–10 grade points
Mean 5.30; spike of implausible 9.5–10 values removed.
Backlogs
Integer, discrete (ordinal)
Number of failed / pending subjects
Count (valid 0–9)
Genuine values 0–4; 10, 20, 30, 50, 99 are sentinels.
Study_Hours_Per_Day
Continuous
Self-reported study time
Hours per day (valid 0–16)
Right-skewed, median 3.4 h; capped at 14 h in genuine data.
Library_Hours_Per_Week
Continuous
Time spent in the library
Hours per week
18.5% record exactly 0 h.
LMS_Logins_Per_Week
Continuous (count-like)
Learning-management-system logins
Logins per week (valid 0–70)
Values 100–1000 are sentinels.
Assignment_Submission_Rate
Continuous
Share of assignments submitted
Percent (20–100)
Clean in range; floor at 20%.
Lab_Attendance_Percent
Continuous
Laboratory attendance
Percent (30–100)
Tracks Attendance_Percent closely (r = 0.83).
Project_Score
Continuous
Project evaluation score
Score out of 100 (observed 20–78)
Floor effect: 74% score exactly 20.
Certifications
Integer, discrete
Certifications earned
Count (valid 0–10)
“many”, –1, –5, 50, 100 repaired or removed.
Scholarship_Status
Binary categorical
Receives a scholarship
Yes / No
34.5% Yes; strongly linked to family income.
Family_Income
Continuous
Annual family income (assumed ₹ per year)
Rupees (valid ₹10,000 – ₹99.99 lakh)
Right-skewed; round-number sentinels 1 crore – 100 crore removed.
Hostel_Status
Binary categorical
Residence type
Hostel / Day Scholar
41% hostel residents.
Part_Time_Job
Binary categorical
Has a part-time job
Yes / No
≈15% Yes.
Travel_Time_Minutes
Continuous
Daily travel time (assumed one way)
Minutes (valid 0–240)
Right-skewed; strongly tied to hostel status.
Sleep_Hours
Continuous
Average sleep
Hours per night (valid 3–12)
Symmetric around 6.7 h.
Stress_Level
Continuous / ordinal score
Self-reported stress
Scale 1–10
Symmetric around 5.0.
Extracurricular_Hours
Continuous
Extracurricular activity time
Hours per week (valid 0–30)
Right-skewed; unrelated to every other variable.
Sports_Participation
Binary categorical
Plays sports
Yes / No
≈10.7% Yes.
Student_Satisfaction
Continuous / ordinal score
Satisfaction with the institution
Scale 1–10
Left-skewed; 7.8% give the maximum 10.
Internship_Status
Binary categorical
Has done an internship
Yes / No
Almost entirely in semesters 5–8.
Internship_Duration_Months
Integer, discrete
Internship length
Months (0–12)
0 for non-interns; conflicts with status in 953 records.
Placement_Preparation_Score
Continuous
Placement-readiness test score
Score 0–100
Roughly normal, mean 63.5.
Placement_Status
Binary categorical (target)
Campus placement outcome
Placed / Not Placed
Overall 25.0% placed; practically only in semesters 6–8.
Placement_Package_LPA
Continuous
Salary package offered
Lakh rupees per annum (valid 0–50)
0 for non-placed students; text such as “₹7.5”, “Rs 7.5”, “7.5 LPA” converted.
Record_Date
Datetime
Date the record was captured
Calendar date, 2021-01-01 … 2026-12-31
Mixed formats; invalid dates such as 2024-99-99 and 31/31/2024.
Email
String (personal data)
Student e-mail
name@domain
1,945 malformed (e.g. “student@unknown”). Excluded from analysis.
Phone
String (personal data)
Mobile number
10 digits
Placeholders 9876543210, 999999, 0 and “abcdefghij”; “+91-“ prefixes stripped. Excluded.

4. Importing Libraries
The analysis uses the standard Python data-science stack.
Code-
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats
import warnings, re
 
warnings.filterwarnings('ignore')
sns.set_theme(style='whitegrid')
pd.set_option('display.max_columns', 50)

5. Loading the Dataset
The workbook is read with pandas without any conversion arguments, so every defect of the file is visible.
Code-
df_raw = pd.read_excel('Dataset.xlsx')          # single sheet: university_student_eda_messy
print(df_raw.shape)                              # (100000, 43)
print(df_raw.dtypes.value_counts())              # object 30, str 13  -> no numeric columns!
df_raw.head()

The data frame has 100,000 rows and 43 columns. The first five records are shown below.
Table 4. First five records 
Student_ID
Age
Gender
Department
Semester
Attendance_Percent
CGPA
Placement_Status
Placement_Package_LPA
STU114325
20
Other
Science
4
75.43
5.55
Not Placed
0
STU103625
19
Male
Science
6
49.02
4.26
Placed
8.74
STU117230
23
Male
Computer Science
5
68.61
4.68
Not Placed
0
STU103068
21
Male
Engineering
4
48.86
3.57
Not Placed
0
STU109851
20
Female
Management
2
70.04
4.58
Not Placed
0

6. Initial Dataset Inspection
Code-
df_raw.info(memory_usage='deep')                 # 181.9 MB
df_raw.isna().all(axis=1).sum()                  # 47 completely blank rows
df_raw['Age'].map(type).value_counts()           # int + str ('unknown')
df_raw.nunique().sort_values(ascending=False).head()

Table 5. Basic structure of the dataset
Property
Value
Rows (raw)
100,000
Columns
43
Fully blank rows
47 (removed; 99,953 rows remain)
Raw data types
30 object (mixed) + 13 string; 0 numeric columns
Memory (raw)
181.9 MB
Memory (typed: numbers as float64, labels as category)
46.9 MB
Numeric columns hiding text
26 of 26
Distinct Student_IDs
21,729

Suspicious Data Types- Although 26 columns are numeric in meaning, pandas reads none of them as numbers: every one contains at least one text entry. The text is of three kinds: disguised missing values (“unknown”, “?”, “-“, “missing”, “not recorded”, “not disclosed”), unit-decorated numbers in Placement_Package_LPA (“₹7.5”, “Rs 7.5”, “7.5 LPA”) and a few word-numbers (“many” certifications, “Second” / “sem 3” for Semester). Record_Date mixes real dates with strings, and Phone mixes integers with strings. In total 12,137 cells in numeric columns are text.
Table 6. Unique values of each categorical variable, before and after standardisation
Variable
Raw distinct values
Clean distinct values
Cells with disguised-missing text
Gender
17
3
946
City
87
25
501
State
32
8
493
Department
41
7
491
Programme
67
20
775
Academic_Year
11
5
230
Scholarship_Status
6
2
519
Hostel_Status
6
2
513
Part_Time_Job
6
2
487
Sports_Participation
6
2
484
Internship_Status
6
2
497
Placement_Status
6
2
513

Table 7. Minimum and maximum of important numeric variables: as typed in the file versus after validation
Variable
Raw min
Raw max
Validated min
Validated max
Age
-5.0
999.0
17.0
30.0
Attendance_Percent
-999.0
999.0
0.0
100.0
SGPA
-999.0
99.0
0.0
8.1
CGPA
-999.0
99.0
0.0
9.0
Study_Hours_Per_Day
-999.0
100.0
0.0
14.0
Family_Income
-999.0
999,999,999.0
50,000.0
9,566,686.3
Travel_Time_Minutes
-30.0
5,000.0
0.0
180.0
LMS_Logins_Per_Week
-10.0
1,000.0
0.0
38.4
Stress_Level
-5.0
99.0
1.0
10.0
Student_Satisfaction
-999.0
99.0
1.0
10.0
Placement_Preparation_Score
-10.0
500.0
0.0
100.0
Placement_Package_LPA
-999.0
1,000.0
0.0
14.2

The contrast is the first signal of data-quality trouble: CGPA appears to range from –999 to 99 and Family_Income up to ₹100 crore, while the validated ranges (CGPA 0–9.0, Family_Income ₹50,000 – ₹95.7 lakh) look like real students.
7. Data Quality Analysis
Twenty data-quality checks were run. Table 8 lists them all and Figure 1 compares their size; sections below discuss each family of problems.
Table 8. All data-quality checks and the number of records or cells they flag
Check
Rule
Flagged
% of tested
Blank records
All 43 fields empty
47
0.05%
Disguised missing tokens
Text such as “unknown”, “?”, “-“, “missing”, “not recorded”, “not disclosed”, “not available” in a data cell
17,695
0.41%
Numbers stored as text
Numeric columns holding text (tokens, “₹7.5”, “Rs 7.5”, “7.5 LPA”, “many”, “sem 3”)
12,137
0.47%
Out-of-domain numeric values
Value outside plausible range (e.g. Age 999, Attendance 150%, CGPA 99, Sleep 30 h, Study 100 h/day)
17,941
0.69%
Unparseable dates
Record_Date like “2024-99-99”, “31/31/2024”, “unknown”
1,013
1.01%
Future-dated records
Record_Date later than 7 Nov 2026 (date of analysis)
2,411
2.44%
Invalid e-mail
Does not match name@domain.tld (e.g. “student@unknown”)
1,945
1.95%
Invalid / placeholder phone
Not a 10-digit Indian mobile number or a placeholder (9876543210, 999999, 0, “abcdefghij”)
1,388
1.40%
City–State mismatch
City does not belong to the recorded state
2,104
2.21%
Programme–Department mismatch
Programme taught outside its expected department (e.g. B.Com in Engineering)
1,866
2.00%
Placed but package = 0
Placement_Status = Placed and Package = 0 LPA
11
0.05%
Not placed but package > 0
Placement_Status = Not Placed and Package > 0 LPA
234
0.32%
Placed in semesters 1–5
Placement_Status = Placed while Semester ≤ 5
1,016
4.21%
Internship = Yes with 0 months
Internship_Status = Yes and duration = 0
155
1.13%
Internship = No with months > 0
Internship_Status = No and duration > 0
798
0.97%
Age–Semester implausible
Semester ≥ 7 and Age ≤ 18
2,882
11.62%
Implausibly high CGPA
CGPA ≥ 9.5 although SGPA ≤ 8.1 and total marks ≈ 48% (spikes at 9.5, 9.7, 9.8, 10.0)
1,767
1.83%
Semester-1 CGPA ≠ SGPA
In the first semester CGPA must equal SGPA
10,967
98.12%
Record date before academic year
Record_Date year earlier than the start year of Academic_Year
32,317
32.95%
Non-standard Student_ID
ID not in STU###### format (e.g. “Student113369”)
995
1.00%
Repeated Student_ID + Semester
Same student and semester appear in more than one row
25,052
25.06%

￼
Figure 1. Number of records or cells flagged by each data-quality check (log scale).
7.1 Missing Values-
Code- 
miss = pd.DataFrame({
    'empty'  : df_raw.isna().sum(),
    'tokens' : df_raw.apply(lambda s: s.map(is_missing_token).sum()),
    'invalid': invalid_counts})                  # impossible values (see section 9)
miss['pct_unusable'] = miss.sum(axis=1) / len(df_raw) * 100
sns.heatmap(df.sample(250, random_state=7).isna(), cbar=False)   # missingness map

Table 9. Missing and unusable values in every column (100,000 raw rows)
Column
Empty
Disguised text
Out of range / invalid
Total unusable
% of rows
Student_ID
50
0
0
50
0.05%
Student_Name
50
0
0
50
0.05%
Age
636
318
1,191
2,145
2.15%
Gender
3,015
946
0
3,961
3.96%
City
2,325
501
0
2,826
2.83%
State
1,747
493
0
2,240
2.24%
Department
2,538
491
0
3,029
3.03%
Programme
3,244
775
0
4,019
4.02%
Academic_Year
262
230
0
709
0.71%
Semester
50
95
443
588
0.59%
Attendance_Percent
4,279
736
1,156
6,171
6.17%
Internal_Marks
3,075
268
679
4,022
4.02%
Assignment_Marks
3,603
295
685
4,583
4.58%
Quiz_Marks
3,123
287
686
4,096
4.10%
End_Sem_Marks
4,585
268
678
5,531
5.53%
SGPA
3,310
743
813
4,866
4.87%
CGPA
2,569
735
2,562
5,866
5.87%
Backlogs
1,838
315
1,393
3,546
3.55%
Study_Hours_Per_Day
4,813
732
967
6,512
6.51%
Library_Hours_Per_Week
5,611
269
0
5,880
5.88%
LMS_Logins_Per_Week
5,081
290
600
5,971
5.97%
Assignment_Submission_Rate
4,116
285
0
4,401
4.40%
Lab_Attendance_Percent
5,635
283
0
5,918
5.92%
Project_Score
4,604
290
690
5,584
5.58%
Certifications
638
381
403
1,533
1.53%
Scholarship_Status
2,503
519
0
3,022
3.02%
Family_Income
7,263
766
624
8,653
8.65%
Hostel_Status
2,517
513
0
3,030
3.03%
Part_Time_Job
3,041
487
0
3,528
3.53%
Travel_Time_Minutes
5,049
309
760
6,118
6.12%
Sleep_Hours
4,567
298
695
5,560
5.56%
Stress_Level
4,118
281
698
5,097
5.10%
Extracurricular_Hours
4,617
262
485
5,364
5.36%
Sports_Participation
3,039
484
0
3,523
3.52%
Student_Satisfaction
5,735
761
795
7,291
7.29%
Internship_Status
3,517
497
0
4,014
4.01%
Internship_Duration_Months
5,530
276
0
5,806
5.81%
Placement_Preparation_Score
4,628
255
491
5,374
5.37%
Placement_Status
2,975
513
0
3,488
3.49%
Placement_Package_LPA
8,945
1,045
447
10,443
10.44%
Record_Date
469
403
0
1,277
1.28%
Email
323
0
1,945
2,268
2.27%
Phone
621
0
1,388
2,009
2.01%

￼
Figure 2. Share of empty, disguised-missing and impossible entries in each column.
￼
Figure 3. Missingness map for 250 random rows after cleaning; dark cells are missing.
Where is the missing information concentrated?
Missingness is spread thinly over almost every column rather than concentrated in a few. The six most incomplete columns are Placement_Package_LPA (10.4%), Family_Income (8.7%), Student_Satisfaction (7.3%), Study_Hours_Per_Day (6.5%), Attendance_Percent (6.2%), Travel_Time_Minutes (6.1%). No column exceeds about 10% unusable entries, so no variable has to be abandoned. Across records the mean number of unusable analytic fields is 1.74 (median 2, 95th percentile 4); only 17,055 rows (17.1%) are completely filled, which is why complete-case analysis over many variables would discard most of the data.
Random or patterned?
Three direct checks suggest the gaps are random. (1) The correlation between missing-value indicators of any two numeric columns never exceeds 0.175, i.e. a student missing one value is no more likely to miss another. (2) CGPA is missing in 5.66%–6.05% of records in every department (χ² p = 0.88). (3) Package is missing in 10.14% of placed and 10.47% of non-placed students (p = 0.26) and Family_Income in 8.55% versus 8.63% of scholarship and non-scholarship students (p = 0.74). This is the signature of data that are missing completely at random (MCAR), although randomness can never be proven from the observed data alone.
Variables of analytical concern and consequences of ignoring the gaps
Placement_Package_LPA (10.4%) and Family_Income (8.7%) are the most incomplete, but still usable.
Silent Failure- If the text tokens were left in place, any numeric operation on 26 columns would either crash or, with coercion, hide the problem; a naive average of a column containing −999 would be badly wrong.
Shrinking Samples- Pairwise sample sizes in this report range from roughly 82,000 to 95,000, so every result states its n.
Possible Bias- Random gaps only cost precision, but if (say) high-income families declined to report income, any income-based mean would be biased. The tests above give no sign of that, yet the risk cannot be excluded.
Decision- Missing values were not replaced. They remain NaN and each analysis uses the cases available for the variables involved (pairwise deletion).
7.2 Duplicate Records-
Table 10. Duplicate investigation
Test
Result
Exact duplicate rows (all 43 columns)
46 duplicates
…of which are blank rows
46 (all of them)
Exact duplicates among non-blank rows
0
Duplicates ignoring Student_ID and Record_Date
0
Distinct Student_IDs / rows carrying an ID
21,729 / 99,950
Average rows per Student_ID (maximum)
4.6 (15)
Rows repeating an earlier Student_ID + Semester
25,052
…even with the same Academic_Year
7,850
IDs whose Gender / Department differ between rows
0.0% / 0.0%
IDs whose Programme / City / State differ
9.7% / 4.1% / 5.9%

Exact Duplicates- Pandas reports 46 duplicated rows, but every one of them is an empty row: the file contains 47 fully blank records, and the second to the 47th are ‘duplicates’ of the first. After removing the blank rows no exact duplicate remains, and none appears even when Student_ID and Record_Date are ignored. Decision: the 47 blank rows carry no information and were deleted (100,000 → 99,953 rows).
Potentially Repeated Observations- Student_ID is a different matter. Only 21,729 IDs cover 99,950 rows, so 95% of IDs occur more than once. Gender and Department never change within an ID, which suggests the ID does identify one person, and the Semester column varies across an ID’s rows, which suggests repeated terms. However, 25,052 rows repeat a Student_ID + Semester pair that already occurred (7,850 with the same academic year too), the marks differ between these rows, and Programme changes for 9.7% of IDs while e-mail and phone differ across most rows of the same ID. Decision: these rows are not exact copies and we cannot tell which one is correct, so they were kept as separate observations. This is recorded as a limitation: results describe student–term records, and students with many rows weigh more.
7.3 Invalid Values-
A value is ‘invalid’ here when it is impossible by definition (a percentage above 100, a negative count) or is a recognisable sentinel code. Limits were derived from the meaning of each column and checked against the data: genuine values form a smooth bell or skewed curve, whereas bad values appear as isolated spikes of round numbers (999, –999, 1000, 5000, 1e9) with roughly 100–150 records each. In total 17,941 numeric cells (0.69% of all numeric cells) were rejected and set to missing.
Table 11. Validity rules for numeric columns and the number of cells they reject
Variable
Valid range
Reason
Cells rejected
Examples of rejected values
Age
16 – 35
Undergraduates cannot be 0, 12 or 999 years old
1,191
–5, 0, 12, 15, 45, 67, 99, 120, 999
Semester
1 – 8
Degrees run for at most eight semesters
443
–1, 0, 9, 10, 12
Attendance_Percent
0 – 100
A percentage
1,156
–999, –20, –5, 101, 105, 120, 150, 999
Internal_Marks
0 – 40
Maximum marks of the component
679
–10, 999
Assignment_Marks
0 – 20
Maximum marks
685
–10, 999
Quiz_Marks
0 – 20
Maximum marks
686
–10, 999
End_Sem_Marks
0 – 80
Maximum marks
678
–10, 101, 110, 150, 999
SGPA
0 – 10
Ten-point grade scale
813
–999, –2, –1, 10.5, 11, 12, 15, 99
CGPA
0 – 9.4
Ten-point scale; ≥ 9.5 rejected (logic rule below)
2,562
–999, –2, –1, 10.5–99; spike at 9.5, 9.7, 9.8, 10
Backlogs
0 – 9
Genuine values are 0–4
1,393
–5, –1, 10, 20, 30, 50, 99
Study_Hours_Per_Day
0 – 16
Study plus sleep must fit into 24 h
967
–999, –5, –1, 18, 20, 24, 30, 100
Library_Hours_Per_Week
0 – 60
Plausible weekly maximum
0
—
LMS_Logins_Per_Week
0 – 70
At most ten logins per day
600
–10, –1, 100, 200, 500, 1000
Project_Score
0 – 100
Score out of 100
690
–10, –1, 101, 110, 150, 999
Certifications
0 – 10
Counts above 10 are sentinels
403
–5, –1, 50, 100, “many”
Family_Income
₹10,000 – ₹99.99 lakh
Round values ≥ ₹1 crore (1e7, 5e7, 1e8, 5e8, 1e9) are sentinels
624
–999, 0, 100, 500, 1e7, 5e7, 1e8, 5e8, 1e9
Travel_Time_Minutes
0 – 240
Four hours is the practical maximum
760
–30, –5, 500, 999, 1000, 2000, 5000
Sleep_Hours
3 – 12
0, 1, 15, 20, 30 h are impossible
695
–3, 0, 1, 15, 20, 30
Stress_Level
1 – 10
Stated scale
698
–5, –1, 0, 11, 15, 99
Extracurricular_Hours
0 – 30
Weekly hours
485
–5, 40, 60, 100, 200
Student_Satisfaction
1 – 10
Stated scale
795
–999, –5, –1, 0, 11, 15, 99
Placement_Preparation_Score
0 – 100
Score out of 100
491
–10, –1, 101, 120, 500
Placement_Package_LPA
0 – 50
Genuine offers are 6–14 LPA; 100, 250, 500, 1000 are sentinels
447
–999, –5, –1, 100, 250, 500, 1000

Zero Spikes- Small spikes of exact zeros (about 100–150 records) also occur in Age, Attendance_Percent, Study_Hours_Per_Day, Quiz_Marks, SGPA and CGPA. Zero is legal for attendance, study time and marks, so these values were kept but flagged as suspicious; a zero Age or Semester was rejected.
Logical and Cross-Field Checks-
Table 12. Logical consistency checks
Check
Rule
Records flagged
% of tested records
Unparseable dates
Record_Date like “2024-99-99”, “31/31/2024”, “unknown”
1,013
1.01%
Future-dated records
Record_Date later than 7 Nov 2026 (date of analysis)
2,411
2.44%
Invalid e-mail
Does not match name@domain.tld (e.g. “student@unknown”)
1,945
1.95%
Invalid / placeholder phone
Not a 10-digit Indian mobile number or a placeholder (9876543210, 999999, 0, “abcdefghij”)
1,388
1.40%
Implausibly high CGPA
CGPA ≥ 9.5 although SGPA ≤ 8.1 and total marks ≈ 48% (spikes at 9.5, 9.7, 9.8, 10.0)
1,767
1.83%
Placed but package = 0
Placement_Status = Placed and Package = 0 LPA
11
0.05%
Not placed but package > 0
Placement_Status = Not Placed and Package > 0 LPA
234
0.32%
Placed in semesters 1–5
Placement_Status = Placed while Semester ≤ 5
1,016
4.21%
Internship = Yes with 0 months
Internship_Status = Yes and duration = 0
155
1.13%
Internship = No with months > 0
Internship_Status = No and duration > 0
798
0.97%
Age–Semester implausible
Semester ≥ 7 and Age ≤ 18
2,882
11.62%
Semester-1 CGPA ≠ SGPA
In the first semester CGPA must equal SGPA
10,967
98.12%
Record date before academic year
Record_Date year earlier than the start year of Academic_Year
32,317
32.95%

Placement Logic- 1,016 students are marked Placed in semesters 1–5 although placement is otherwise a final-year event (see Question 1 in the advanced analysis); 234 non-placed students have a salary and 11 placed students have none.
Internship Logic- 798 students without an internship report months of internship, and 155 with an internship report none.
Time Logic- 33.0% of records are dated in a calendar year earlier than the start of their stated Academic_Year, 2,411 are dated after today (7 Nov 2026) and 1,013 dates cannot be parsed at all (“2024-99-99”, “31/31/2024”). Record_Date therefore cannot be a reliable academic-term date.
Semester-1 CGPA- In the first semester CGPA must equal SGPA, yet they differ in 98.1% of the tested rows – the two grade columns were evidently recorded independently.
Age vs Semester- 2,882 final-year records (semester ≥ 7) belong to students aged 18 or less.
7.4 Inconsistent Values-
Inconsistent values are entries that mean the same thing but are written differently, or that contradict another field. They would fragment group counts: counting ‘Department’ without cleaning would report 41 departments instead of 7.
Table 13. Examples of inconsistent labels found and the standard form used
Variable
Examples of variants found
Standardised to
Gender
Male, MALE, male, M, m, Female, F, f, Other, O, other, “Unknown”, “?”, “-“
Male / Female / Other (+ missing)
State
Maharashtra, Mahashtra, Maharastra, MAHARASHTRA; Tamil Nadu, Tamilnadu, tamil nadu; Karnatak
8 official names
City
Hyderabad, Hydrabad, hyderabad; Mumbai, Mumabi; Kolkata, Kolkatta; Pune, Punee; Jaipur, Jaipurr
25 cities
Department
Information Technology, IT, I.T., Info Tech; Computer Science, CS, C.S., Comp. Science; Arts, Humanities, Humanity; “BBA Department”, “B.Com Department”
7 departments
Programme
B.Sc Computer Science, BSc Computer Science, B.Sc CS; BCA, B.C.A, bca; B.Tech, BTECH
20 programmes
Academic_Year
2022-23, 2022-2023, 22-23, 2022_23, 2022/23, 2022
YYYY-YY (5 years)
Semester
4, “Second”, “1st”, “sem 3”
Integer 1–8
Yes/No fields
Yes, No, Unknown, unknown, ?, -
Yes / No (+ missing)
Placement_Package_LPA
7.5, “₹7.5”, “Rs 7.5”, “7.5 LPA”, “₹0.0”, “not disclosed”
Number (LPA)
Student_ID
Student113369, stu104049, Student105313, STUDENT112312
STU######
Phone
8233205603, +91-8233205603, 98765 43210
10 digits

￼
Figure 4. Number of distinct labels in the categorical variables before and after standardisation.
City vs State- 2,104 records (2.2%) place a city in the wrong state (for example Kolkata with Maharashtra). The mismatches are tiny compared with the 92,949 consistent records, so they look like entry errors; both fields were kept and the pair is only used as a quality check.
Programme vs Department- 1,866 records (2.0%) assign a programme to a department that does not teach it (for example B.Com in Engineering). The generic programme labels BCA, B.Com, BBA, B.Tech and B.Sc Computer Science account for almost all of them.
Delhi and New Delhi are kept as separate city labels (about 6,000 records each) because the file treats them as separate cities, although they probably refer to one metropolitan area.
Capitalisation and Spelling- After lower-casing and mapping typos, no unmapped label remained in any categorical column.
8. Data Preprocessing
Table 14. Transformations applied, why they were needed and their effect
#
Transformation
Why necessary
Effect on the dataset
Effect on interpretation
1
Delete 47 fully blank rows
They hold no information and create 46 fake duplicates
100,000 → 99,953 rows; exact duplicates 46 → 0
None
2
Convert disguised-missing text (“unknown”, “?”, “-“, “missing”, “not recorded”, “not disclosed”, “not available”) to NaN
Text blocks numeric computation and hides missingness
17,695 cells converted
Makes missingness measurable
3
Parse numbers stored as text (₹/Rs/LPA symbols, “sem 3”, “Second”, “+91-“)
26 numeric columns were typed as text
All 26 numeric columns now numeric; Package parsed from 4 text formats
Package text such as “₹0.0” becomes a valid zero
4
Reject impossible numeric values (domain rules, Table 11 and Table 12)
Sentinels such as 999 and –999 distort every statistic
17,941 cells → NaN
Large changes in mean, SD and correlations (see Before-and-After)
5
Standardise category spelling and case (Gender, State, City, Department, Programme, Yes/No)
Fragmented labels split groups
Gender 17→3, State 32→8, City 87→25, Department 41→7, Programme 67→20
Group counts become meaningful
6
Standardise Academic_Year and Semester
11 year formats; text semesters
11 → 5 academic years; Semester numeric 1–8
Allows ordering and grouping
7
Parse Record_Date to datetime; invalid dates → NaT
Mixed formats / impossible dates
1,230 dates missing after parsing (incl. 1,013 invalid)
Enables time analysis
8
Normalise Student_ID to STU######
Mixed case and prefixes
995 IDs corrected
IDs can be grouped reliably
9
Reject CGPA ≥ 9.5 (logic rule)
Spike of 9.5–10 inconsistent with SGPA ≤ 8.1 and marks ≈ 48%
1,767 values → NaN
SGPA–CGPA correlation rises from 0.61 to 0.75
10
Derive Total_Marks, Total_Marks_Pct, Placed_Flag, Academic_Year_Start, Semester_Band, CGPA band
Convenient summaries; no information invented
Total_Marks = Internal + Assignment + Quiz + End-Sem (max 160, assumed)
Total_Marks_Pct available for 83,044 students
11
Log₁₀ transform of Family_Income (for plotting and group plots only)
Skew 3.15 after validation
Skew of log₁₀(income) = 0.02
Shape becomes symmetric; original rupee scale kept for statistics

Code-
MISSING = {'unknown','?','-','not recorded','missing',
           'not available','not disclosed','n/a',''}
def to_num(s):
    s = s.map(lambda x: np.nan if isinstance(x,str)
                        and x.strip().lower() in MISSING else x)
    return pd.to_numeric(s, errors='coerce')
 
LIMITS = {'Age':(16,35), 'Attendance_Percent':(0,100), 'Internal_Marks':(0,40),
          'SGPA':(0,10), 'Study_Hours_Per_Day':(0,16),
          'Family_Income':(10_000,9_999_999),
          'Placement_Package_LPA':(0,50)}          # ... 25 rules in total
for col,(lo,hi) in LIMITS.items():
    parsed = to_num(df[col])
    df[col] = parsed.where(parsed.between(lo,hi))      # impossible values -> NaN, nothing is imputed
 
g = {'m':'Male','male':'Male','f':'Female','female':'Female','o':'Other','other':'Other'}
df['Gender'] = df['Gender'].str.strip().str.lower().map(g)
df['Record_Date'] = pd.to_datetime(df['Record_Date'], errors='coerce')
df = df.dropna(how='all')                              # 47 blank rows

9. Descriptive Statistics
All statistics use validated data (impossible values removed, nothing imputed) and the students for whom the variable is available. Placement_Package_LPA is summarised for placed students only. For continuous variables the mode is that of the values rounded to a sensible precision (nearest rupee 10,000 for income, 0.1 for GPA and hours, 1 for marks), so it is informative only for Age, the capped marks and similar discrete-like variables.
Table 15. Descriptive statistics (1): count, centre and range
Variable
n
Mean
Median
Mode
Min
Max
Range
Age
97,855
20.83
21.00
21.0
17.0
30.0
13.0
Attendance Percent
93,829
62.92
62.82
65.0
0.0
100.0
100.0
Internal Marks
95,978
36.90
40.00
40.0
0.0
40.0
40.0
Quiz Marks
95,904
11.85
11.78
20.0
0.0
20.0
20.0
End Sem Marks
94,469
27.83
26.07
10.0
0.0
80.0
80.0
Total Marks Pct
83,044
60.08
59.09
52.0
13.9
100.0
86.1
SGPA
95,134
5.30
5.30
5.2
0.0
8.1
8.1
CGPA
94,134
5.30
5.30
5.2
0.0
9.0
9.0
Study Hours Per Day
93,488
3.95
3.37
2.0
0.0
14.0
14.0
Library Hours Per Week
94,120
3.06
2.65
0.0
0.0
17.7
17.7
LMS Logins Per Week
94,029
8.18
7.66
0.0
0.0
38.4
38.4
Lab Attendance Percent
94,082
63.02
62.89
64.0
30.0
100.0
70.0
Family Income
91,347
596,844
449,190
240,000
50,000.0
9,566,686
9,516,686
Travel Time Minutes
93,882
24.98
16.98
2.0
0.0
180.0
180.0
Sleep Hours
94,440
6.70
6.70
6.6
3.0
10.0
7.0
Stress Level
94,903
5.03
5.03
5.0
1.0
10.0
9.0
Extracurricular Hours
94,636
3.59
2.96
2.0
0.0
20.0
20.0
Student Satisfaction
92,709
7.77
7.82
10.0
1.0
10.0
9.0
Placement Preparation Score
94,626
63.55
63.22
60.0
0.0
100.0
100.0
Placement Package LPA
21,711
9.96
9.99
7.5
0.0
14.2
14.2

Table 16. Descriptive statistics (2): quartiles, spread and shape
Variable
Q1
Q3
IQR
Variance
Std dev
Skewness
Excess kurtosis
Age
19.00
22.00
3.00
3.87
1.97
0.15
-0.26
Attendance Percent
53.97
71.71
17.74
172
13.13
-0.03
0.38
Internal Marks
35.70
40.00
4.30
35.6
5.97
-3.10
12.63
Quiz Marks
8.03
15.91
7.88
27.6
5.25
-0.12
-0.79
End Sem Marks
15.99
36.82
20.83
206
14.35
0.73
0.20
Total Marks Pct
51.02
68.24
17.22
156
12.48
0.34
0.00
SGPA
4.83
5.78
0.95
0.514
0.72
-0.34
2.42
CGPA
4.71
5.91
1.20
0.818
0.90
-0.18
1.03
Study Hours Per Day
2.00
5.29
3.29
6.92
2.63
1.18
1.49
Library Hours Per Week
0.65
4.76
4.11
7.28
2.70
0.85
0.41
LMS Logins Per Week
3.55
11.96
8.41
34.9
5.91
0.61
0.12
Lab Attendance Percent
52.58
73.22
20.64
224
14.96
0.08
-0.37
Family Income
270,494
748,135
477,641
2.7e+11
519,548
3.15
19.32
Travel Time Minutes
8.22
34.38
26.16
555
23.56
1.84
4.38
Sleep Hours
5.89
7.51
1.62
1.43
1.19
-0.01
-0.12
Stress Level
3.94
6.10
2.16
2.53
1.59
0.05
-0.15
Extracurricular Hours
1.62
4.87
3.25
7.2
2.68
1.45
2.97
Student Satisfaction
6.80
8.85
2.05
2.03
1.42
-0.33
-0.34
Placement Preparation Score
52.90
73.91
21.01
242
15.55
0.01
0.00
Placement Package LPA
9.22
10.76
1.54
1.43
1.20
-0.39
2.09

What the Numbers Say-
Symmetric Variables- Attendance (mean 62.92, median 62.82, skew -0.03), Placement_Preparation_Score, Sleep_Hours, Stress_Level, SGPA and CGPA have mean ≈ median and |skew| < 0.4, so the mean is a fair summary. CGPA has mean 5.30 and standard deviation 0.90; half of the students lie between 4.71 and 5.91 (IQR 1.20).
Right-Skewed Variables- Family_Income has mean ₹5.97 lakh but median ₹4.49 lakh and skewness 3.15; Travel_Time (mean 25.0 vs median 17.0 min, skew 1.84) and Study_Hours (mean 3.95 vs median 3.37 h, skew 1.18) behave similarly. For these, the median describes the typical student better than the mean.
Ceiling Effect- Internal_Marks has median 40 = maximum 40; 59.8% of students score full marks, which produces skewness -3.10 and kurtosis 12.63. The statistic is driven by the cap, not by a genuine long tail.
Zero-Inflation- 18.5% of students log 0 library hours and 10.0% log 0 LMS logins; their means (3.06 h, 8.2 logins) blend non-users with users.
Variability- Relative spread (coefficient of variation) is lowest for Age (9.4%) and CGPA (17.1%) and highest for Family_Income (87%), Library hours (88%) and Travel time (94%).
Attendance- The average attendance is 62.9% and 82.2% of students are below a 75% norm, which is low for an institutional record and may indicate simulated or differently-defined data.
10. Univariate Analysis
￼
Figure 5. Distributions of nine important numeric variables (histogram + density curve, orange line = median).
￼
Figure 6. Frequency of six categorical variables.
￼
Figure 7. Number of records per programme.
CGPA-
Visible in The Plot- A single, nearly symmetric bell centred at 5.30; mean 5.30, median 5.30, SD 0.90, skew -0.18, kurtosis 1.03. The IQR is 4.71–5.91. A tiny spike sits at 0 (about 100 students) and the IQR rule flags 721 students (0.77%), mostly on the low side.
Inferred- Grades are concentrated in the 4.7–5.9 band, which is a middling performance for a ten-point scale; the zero spike is probably a recording artefact rather than students who failed everything.
Attendance Percent-
Visible in The Plot- A symmetric bell between about 27 and 98, mean 62.92, SD 13.13; only 0.30% reach exactly 100.
Inferred- Typical attendance is far below a 75% norm (82.2% of students are under it); the data show no ceiling pile-up, so the variable can discriminate between students at every level.
Study Hours Per Day-
Visible in The Plot- A right-skewed hump with its mode near 2 h, median 3.37 h, mean 3.95 h, and a long tail up to the 14 h cap (0.5% sit at 14). The IQR rule flags 3.1% of students (above 10.2 h).
Inferred- Most students study 2–5 hours, but a minority study 10+ hours; because the tail is plausible (not a sentinel) these students are genuine heavy studiers.
Family Income-
Visible in The Plot- On the log scale the histogram is a clean bell, on the rupee scale it would be a long right tail. Median ₹4.49 lakh, mean ₹5.97 lakh, skew 3.15, maximum ₹95.67 lakh.
Inferred- Income is approximately log-normal, as real incomes usually are; geometric or median summaries and log scales are appropriate. About 5.8% of households are flagged as high-income outliers by the IQR rule, but they are plausible.
Placement Preparation Score-
Visible in The Plot- A symmetric bell, mean 63.6, SD 15.6, with 1.4% piled at the maximum 100.
Inferred: scores are well spread; the small pile at 100 shows a mild ceiling but does not distort the shape.
Internal Marks-
Visible in The Plot- An extreme left-skew: a tall spike at 40 (maximum) containing 59.8% of students and a thin tail of lower scores; median = max = 40, mean 36.9, skew -3.10.
Inferred- The assessment is too easy or the marks are capped, so it cannot distinguish the top 60% of students; the apparent ‘outliers’ on the low side are just the rest of the distribution.
Stress Level-
Visible in The Plot- A symmetric bell on the 1–10 scale, mean 5.03, SD 1.59; small pile-ups at 1 (0.5%) and 10 (0.1%).
Inferred- Stress is centred at the scale midpoint; the self-reported nature and the clipping at the scale ends mean extreme values are ‘at least’ values.
Travel Time Minutes-
Visible in The Plot: A right-skewed distribution with a sharp peak at short commutes and a long tail past 100 minutes: median 17.0, mean 25.0, skew 1.84.
Inferred- The shape looks like a blend of two groups – hostel residents with a few minutes and day scholars with long commutes – which is confirmed in the bivariate section (hostel median 8 min vs day scholar 30 min).
Placement Package LPA (placed students)-
Visible in The Plot- A compact symmetric bell around 10.0 LPA between about 6 and 14 LPA, SD 1.20.
Inferred- Offers are very homogeneous, and nobody earns far above the rest; this uniformity is unusual for real campuses.
Categorical Variables-
Visible in The Plot- Gender is split almost equally (Female 33.8%, Other 33.3%, Male 33.0%); the eight states each hold 11.6–12.8% of records; the seven departments each 13–14.3%; the eight semesters about 12% each; 25.0% of students with a known status are Placed and 14.3% have an internship.
Inferred- The categories are almost perfectly balanced, an unusual feature that again suggests a designed or simulated sample; only the Yes/No outcomes (placement, internship, scholarship, hostel, job, sports) are imbalanced. Because every category is large, group comparisons will have ample sample size. BCA and B.Sc Information Technology are the two largest programmes (about 10,100 and 9,200 records) because their labels were merged during cleaning.
11. Outlier Analysis
Code-
def iqr_flags(s):
    q1, q3 = s.quantile([.25, .75]); iqr = q3 - q1
    return (s < q1 - 1.5*iqr) | (s > q3 + 1.5*iqr)
 
def z_flags(s):
    return ((s - s.mean()) / s.std()).abs() > 3
 
for col in ['Study_Hours_Per_Day', 'Travel_Time_Minutes', 'Family_Income']:
    s = df[col].dropna()
    print(col, iqr_flags(s).sum(), z_flags(s).sum())

Outliers were investigated with three approaches that rest on different logic: (1) the Tukey IQR rule (flag values beyond Q1 – 1.5·IQR or Q3 + 1.5·IQR), a robust distribution-based rule; (2) the z-score rule (flag |z| > 3, i.e. more than three standard deviations from the mean), which assumes a roughly bell-shaped variable and is itself influenced by the outliers; and (3) the domain / sentinel rule already applied during validation (impossible or round-number codes). The modified z-score (median/MAD) was added as a robust cross-check. Methods (1)–(2) were applied to the validated data and, for contrast, to the raw numbers.
Table 17. Outliers flagged on validated data by each method
Variable
n
IQR fences (low / high)
IQR flagged
|z| > 3 flagged
MAD flagged
Flagged by both
Rejected earlier by domain rule
Study Hours Per Day
93,488
-2.9 / 10.2
2,885 (3.1%)
1,384 (1.5%)
1,614
1,384
967
Travel Time Minutes
93,882
-31.0 / 73.6
4,526 (4.8%)
1,653 (1.8%)
4,706
1,653
760
Family Income
91,347
≤ 0 / 1,464,597
5,340 (5.8%)
1,675 (1.8%)
4,596
1,675
624
Library Hours Per Week
94,120
-5.5 / 10.9
802 (0.9%)
689 (0.7%)
132
689
0
LMS Logins Per Week
94,029
-9.1 / 24.6
868 (0.9%)
527 (0.6%)
124
527
600
Extracurricular Hours
94,636
-3.3 / 9.7
3,229 (3.4%)
1,376 (1.5%)
1,937
1,376
485
Placement Package LPA
21,700
6.9 / 13.1
76 (0.4%)
21 (0.1%)
1
21
447
CGPA
94,134
2.9 / 7.7
721 (0.8%)
296 (0.3%)
139
296
2,562
Attendance Percent
93,829
27.4 / 98.3
557 (0.6%)
144 (0.2%)
144
144
1,156

￼
Figure 8. Share of values flagged by the IQR, z-score and MAD rules.
￼
Figure 9. Box plots before (raw) and after validation; symmetric-log axes are used so that the sentinel values remain visible.
Comparing the Methods-
The IQR rule is the most sensitive- For Family_Income it flags 5.8% of households versus 1.8% for z-scores; every z-flag is also an IQR flag (z-only = 0 for all variables) so the z-rule is a strict subset. The reason is skewness: a long right tail inflates the standard deviation, which pushes the z-threshold (₹21.55 lakh) far above the IQR fence (₹14.65 lakh).
Masking in Raw Data- With the sentinels still present, Family_Income has standard deviation ₹25.6 million; the z-rule then flags only 191 values (the sentinels themselves), while the 5,000+ genuinely high incomes slip below the inflated threshold. Likewise Travel_Time’s SD is 133 min raw versus 23.6 validated.
Symmetric variables agree better- For Attendance and CGPA both rules flag few values, and z-flags are a subset of IQR flags (144 of 557; 296 of 721).
The MAD rule behaves like the IQR rule on skewed variables (Family_Income 4,596) and like z on bell-shaped ones (Attendance 144); for Package it flags just 1 because the median absolute deviation is tiny.
What are the flagged observations?
Table 18. Classification of flagged observations
Group of flagged values
Evidence
Verdict
Action
Sentinel codes (999, –999, 1000, 5000, 1e9, 250 LPA …)
Isolated round-number spikes of ~100–150 records; outside any possible range
Data-entry / system errors
Set to missing (17,941 cells)
Study hours above ≈10 h, travel time above ≈74 min, extracurricular above ≈10 h
Smooth continuation of the distribution, inside the possible range
Genuine extreme observations
Kept
Family income above ≈₹14.6 lakh up to ₹95.7 lakh
Continuous tail of a log-normal curve; no round-number pile-up
Rare but valid cases
Kept; analysed on log scale where needed
Packages above 13.1 LPA (up to 14.2)
Top end of a smooth bell
Rare but valid cases (best offers)
Kept
CGPA below 2.9 (and exact 0)
Low tail; a zero spike of ≈100 records
Mostly genuine at-risk students; zeros need checking
Kept; zero spike flagged
Semester-1–5 placements, placed with zero package
Logically inconsistent combinations
Records requiring further investigation
Kept but noted as a data issue

Keep, Remove or Replace? Effect on Family_Income
Table 19. Effect of outlier treatment on Family_Income (₹)
Scenario
n
Mean
Median
Std dev
Skew
Raw, sentinels kept
91,971
1,497,644
449,468
25,626,246
35.52
Validated, outliers kept
91,347
596,844
449,190
519,548
3.15
IQR outliers removed
86,007
502,553
425,072
312,474
0.95
Winsorised at 1st/99th pct
91,347
588,502
449,190
468,237
1.91
Outliers replaced by median
91,347
499,434
449,190
303,461
1.01

Keep the validated extremes- mean ₹5.97 lakh, SD ₹5.20 lakh; the mean is pulled above the median (₹4.49 lakh) by 5,340 wealthy households, but the median barely moves. Right choice when the rich households are real and the analysis uses medians, ranks or logs.
Remove them (IQR rule)- 5,340 households disappear, the mean falls to ₹5.03 lakh, SD to ₹3.12 lakh and skew to 0.95. This makes the sample look artificially homogeneous, deletes the wealthiest 6% of families and shifts the median down to ₹4.25 lakh.
Replace them by the median- the mean becomes ₹4.99 lakh, the SD collapses to ₹3.03 lakh and a spike of 5,340 identical values is created, distorting the distribution. Winsorising at the 1st/99th percentile is a gentler compromise (mean ₹5.89 lakh).
Sensitivity- The correlation between income and package (placed students) is 0.000 if outliers are kept, -0.008 if removed and -0.001 if winsorised – essentially zero in every case – so the conclusion is robust to the choice.
Decision- Only sentinel errors were removed (set to missing). Plausible extremes were kept, and skewed variables are summarised with medians, ranks or log scales.
12. Bivariate Analysis
12.1 Numerical vs Numerical-
￼
Figure 10. Six numeric–numeric relationships (blue = sample of students, orange = binned mean).
Table 20. Numeric–numeric relationships investigated
Pair
n
Pearson r
Spearman ρ
Slope
Mean of Y: lowest → highest decile of X
Study Hours Per Day × SGPA
89,006
0.57
0.57
0.155
4.72 → 6.13
Attendance Percent × Lab Attendance Percent
88,304
0.83
0.84
0.947
41.62 → 85.69
Stress Level × Student Satisfaction
88,013
-0.34
-0.33
-0.305
8.59 → 6.90
Placement Preparation Score × CGPA
89,115
0.46
0.46
0.027
4.64 → 6.11
Sleep Hours × Stress Level
89,656
-0.26
-0.25
-0.346
5.76 → 4.29
Age × CGPA
92,148
0.00
0.00
0.001
5.30 → 5.31
Internal Marks × CGPA
90,353
0.35
0.37
0.066
4.57 → 5.50
Quiz Marks × SGPA
91,290
0.57
0.58
0.078
4.67 → 5.94
Travel Time Minutes × Stress Level
89,160
0.23
0.19
0.015
4.70 → 5.86

Pearson r measures linear association, Spearman ρ monotonic association; large gaps between them signal non-linearity or outliers. The first six pairs are plotted above; the last three (Internal Marks, Quiz Marks, Travel time) are additional checks.
Study Hours and SGPA-
Direction and Strength- Positive and moderate (r = 0.57, ρ = 0.57; R² = 0.32). Each extra daily study hour goes with about 0.16 more SGPA points; the lowest decile of study time averages SGPA 4.72 versus 6.13 for the highest. Shape: the cloud is linear with no visible curvature (Pearson ≈ Spearman); there is a vertical wall at 14 h where the variable is capped. Outliers/clusters: a handful of SGPA = 0 points lie far below the cloud. Meaning: plausible and meaningful, but it is association, not proof that studying longer raises grades (capable or motivated students may both study more and score more).
Attendance and Lab Attendance-
Very strong and positive (r = 0.83, ρ = 0.84, R² = 0.69). A student who attends lectures also attends labs; the lab column floors at 30% and the relation is close to a straight line with slope 0.95. The two variables are nearly redundant, and including both in a model would add little new information.
Stress and Satisfaction-
Moderate negative association (r = -0.34, ρ = -0.33): the average satisfaction falls from 8.6 to 6.9 between the least and most stressed decile. The scatter is wide (R² = 0.12) and satisfaction is bunched at its maximum of 10, so stress explains only about one-ninth of satisfaction. Direction of influence cannot be read from the data.
Placement Preparation and CGPA-
Moderate positive association (r = 0.46, ρ = 0.46). Spearman is higher than Pearson, which hints at mild non-linearity or outlying CGPA values: preparation scores rise steadily across deciles of CGPA (4.64 to 6.11 on the CGPA axis). Students who perform well academically also prepare better, which is intuitive, but both may be driven by a common trait (diligence).
Sleep and Stress, Age and CGPA-
Sleep and stress are weakly negatively related (r = -0.26); more sleep goes with slightly lower stress (a drop of 1.5 stress points from the shortest to longest sleepers). Age and CGPA show no relationship at all (r = 0.003), and the binned means are flat – a useful ‘null’ example showing that not every plausible pair is related. Another surprise: Internal Marks and CGPA are related only moderately (r = 0.35, ρ = 0.37) because internal marks are capped at 40, which compresses their variance.
12.2 Numerical vs Categorical-
￼
Figure 11. Numeric variable by category: four comparisons.
Each comparison uses grouped summaries, box plots and a non-parametric test (Mann–Whitney U for two groups, Kruskal–Wallis for more) together with an effect size. With 90,000 observations almost any difference is ‘statistically significant’ (p < 0.001), so the effect size is what matters: Cohen’s d ≈ 0.2 is small, 0.5 medium, 0.8 large.
Table 21. CGPA by internship status
Internship Status
n
Mean
Median
Std dev
IQR
No
77,507
5.26
5.27
0.88
1.17
Yes
12,908
5.58
5.57
0.98
1.40

CGPA and Internship- Students with an internship have a higher median CGPA (5.57 vs 5.27), a higher mean (5.58 vs 5.26) and wider spread (IQR 1.40 vs 1.17). The effect is small-to-moderate (Cohen’s d = 0.36, rank-biserial r = 0.19) and distributions overlap heavily. Mean and median tell the same story, so the shift is not an artefact of outliers.
Table 22. Family income (₹) by scholarship status
Scholarship Status
n
Mean
Median
Std dev
IQR
No
57,069
715,253
566,988
542,960
478,942
Yes
31,569
384,094
271,515
397,120
185,838

Income and Scholarship- Scholarship holders come from poorer families: median income ₹2.72 lakh vs ₹5.67 lakh, and IQR is much narrower (₹1.86 lakh vs ₹4.79 lakh). Rank-biserial r = 0.57 is the largest effect among these comparisons: scholarships are clearly means-tested. Note that the means (₹3.84 lakh vs ₹7.15 lakh) are larger than the medians in both groups because of right-skew, and Cohen’s d (computed on raw rupees) understates the gap for the same reason, which is why the rank-based effect is reported. Yet some wealthy households hold scholarships: the ‘Yes’ group has high-income extremes too.
Table 23. Stress level by residence
Hostel Status
n
Mean
Median
Std dev
IQR
Day Scholar
53,250
5.20
5.20
1.59
2.16
Hostel
38,814
4.80
4.79
1.55
2.09

Stress and Hostel- Day scholars are more stressed (median 5.20 vs 4.79; d = 0.25, a small effect). Spread is similar (IQR 2.16 vs 2.09) and extreme stress scores occur in both groups. The reason is investigated in advanced Question 4.
Table 24. SGPA by department
Department
n
Mean
Median
Std dev
IQR
Arts
13,277
5.31
5.30
0.71
0.95
Commerce
13,246
5.30
5.30
0.72
0.95
Computer Science
13,464
5.30
5.30
0.71
0.92
Engineering
12,613
5.29
5.30
0.72
0.94
Information Technology
13,558
5.31
5.31
0.71
0.94
Management
12,754
5.31
5.30
0.72
0.96
Science
13,384
5.30
5.31
0.72
0.95

SGPA and Department- All seven departments have the same median SGPA (5.30–5.31), the same IQR (0.92–0.96) and the same range; Kruskal–Wallis H = 3.5, p = 0.75, ε² = 0.000. Department makes no difference to semester results in this data, an example of a comparison that is not related.
Table 25. Travel time (minutes) by residence
Hostel Status
n
Mean
Median
Std dev
IQR
Day Scholar
52,679
35.82
29.95
25.36
31.04
Hostel
38,388
10.09
8.44
7.10
8.71

Travel Time and Hostel- The strongest contrast: hostel residents travel a median 8.4 min versus 29.9 for day scholars (Cohen’s d = 1.30, rank-biserial 0.75). Day scholars also vary three times more (SD 25.4 vs 7.1) and account for nearly all commutes beyond 60 minutes – extreme observations are concentrated in this group.
Gender and CGPA- The three genders have the same mean CGPA (5.30–5.31) and median (5.29–5.31); Kruskal–Wallis p = 0.24. No difference.
12.3 Categorical vs Categorical-
￼
Figure 12. Four categorical–categorical relationships shown as 100%-stacked bars.
Table 26. Placement by internship (χ² = 8,768.4, df = 1, p < 0.001, Cramér’s V = 0.308, n = 92,678)
Internship Status
Not Placed (n)
Placed (n)
% Placed
No
63,831
15,556
19.6%
Yes
5,632
7,659
57.6%

Students with an internship are placed far more often (57.6% versus 19.6%), a moderate association (V = 0.31). But internships are almost exclusively held in semesters 5–8, so part of the gap simply reflects who is old enough to be placed, the stratified analysis in the multivariate section addresses this.
Table 27. Placement by semester band (χ² = 46,504.5, df = 1, p < 0.001, Cramér’s V = 0.696, n = 95,990)
Semester Band
Not Placed (n)
Placed (n)
% Placed
Semester 1–5
59,031
1,016
1.7%
Semester 6–8
12,946
22,997
64.0%

Placement is overwhelmingly a semesters 6–8 phenomenon: 64.0% of final-year records are placed versus 1.7% earlier, a very strong association (V = 0.70, the strongest relationship in the whole file). The 1,016 early placements are suspicious (see data quality).
Table 28. Scholarship by hostel status (χ² = 4.0, df = 1, p = 0.046, Cramér’s V = 0.006, n = 94,085)
Hostel Status
No (n)
Yes (n)
% Yes
Day Scholar
34,918
19,540
35.9%
Hostel
25,659
13,968
35.2%

Hostel residents and day scholars hold scholarships at the same rate (35.9% vs 35.2%); V = 0.006. The χ² p-value is borderline only because n is large, and the effect is negligible: residence and scholarship are independent.
Table 29. Gender by department (χ² = 36.7, df = 12, p < 0.001, Cramér’s V = 0.014, n = 93,194)
Department
Female (n)
Male (n)
Other (n)
% Other
Arts
4,695
4,164
4,525
33.8%
Commerce
4,426
4,510
4,504
33.5%
Computer Science
4,499
4,549
4,589
33.7%
Engineering
4,339
4,182
4,200
33.0%
Information Technology
4,552
4,575
4,540
33.2%
Management
4,328
4,252
4,226
33.0%
Science
4,611
4,526
4,402
32.5%

The gender mix is the same in every department (each 32–35% per gender); V = 0.014. A small χ² significance (p = 0.0003) with n ≈ 97,000 corresponds to differences of about one percentage point, which is practically irrelevant.
13. Correlation Analysis
Code-
num_cols = ['Age','Attendance_Percent','Internal_Marks','SGPA','CGPA','Study_Hours_Per_Day', ...]  # 23 variables
corr = df[num_cols].corr()                       # pairwise-complete Pearson
sns.heatmap(corr, mask=np.triu(np.ones_like(corr, bool), 1), cmap='RdBu_r', vmin=-1, vmax=1, annot=True, fmt='.1f')

A correlation matrix was computed for 23 validated numeric variables with pairwise-complete observations (about 82,000–95,000 students per pair). The full matrix is shown as a heat-map in Figure 13; the strongest pairs are ranked in Figure 14 and Table 30.
￼
Figure 13. Pearson correlation matrix (lower triangle).
￼
Figure 14. The fourteen strongest correlations.
Table 30. Strongest correlations (Pearson, with Spearman for comparison)
Pair
Pearson r
Spearman ρ
Attendance Percent × Lab Attendance Percent
0.83
0.84
SGPA × CGPA
0.75
0.77
Quiz Marks × Study Hours Per Day
0.67
0.67
End Sem Marks × Study Hours Per Day
0.64
0.58
Study Hours Per Day × Assignment Submission Rate
0.64
0.64
Study Hours Per Day × LMS Logins Per Week
0.63
0.57
Study Hours Per Day × Library Hours Per Week
0.61
0.54
End Sem Marks × SGPA
0.61
0.60
SGPA × Study Hours Per Day
0.57
0.57
Quiz Marks × SGPA
0.57
0.58
Study Hours Per Day × Placement Preparation Score
0.53
0.50
Quiz Marks × Assignment Submission Rate
0.48
0.49
End Sem Marks × CGPA
0.48
0.47
End Sem Marks × LMS Logins Per Week
0.47
0.42

Relatively Strong Relationships-
Attendance - Lab Attendance r = 0.83 – the strongest pair and almost a duplicate variable.
An ‘Academic Engagement’ Cluster- Study hours is related to Quiz marks (0.67), End-Sem marks (0.64), Assignment submission rate (0.64), LMS logins (0.63) and Library hours (0.61); these in turn relate to SGPA (0.35–0.61) and CGPA (0.23–0.45). Study hours is the hub of the cluster.
SGPA - CGPA r = 0.75; End-Sem marks - SGPA 0.61.
Wellbeing Cluster- Stress - Satisfaction -0.34, Sleep - Stress -0.26, Sleep - Satisfaction 0.20, Travel time - Stress 0.23.
Weak or Absent Relationships-
Three variables have no correlation above 0.01 with anything: Age, Family Income, Extracurricular Hours (Travel time and Sleep also correlate with nothing outside the wellbeing cluster). The wellbeing cluster (stress, sleep, satisfaction, travel) is uncorrelated with the academic variables (|r| ≤ 0.14, the largest being Satisfaction × CGPA), and Backlogs shows only weak negative links (–0.02 to –0.21). Age, income and extracurricular time apparently play no role in academic results.
Unexpected Relationships-
Certifications - Placement Preparation (r = 0.38) and Certifications - Internship months (0.29) are among the few links outside the academic cluster: students who collect certifications also prepare for placement and intern.
Internship Duration - Package (0.27) is the strongest correlate of salary; package is only weakly related to CGPA (0.13) and Preparation score and unrelated to income (0.00).
Family income is unrelated to academic results (|r| < 0.01) although it determines scholarship status – a socio-economic gradient in marks might have been expected.
Raw-Data Illusion- Before validation, income seemed to correlate with package (0.07); after removing sentinel codes it is 0.004. The ‘relationship’ was produced by bad values (see Before-and-After).
Redundant or Strongly Related Variables-
Attendance and Lab attendance (0.83) are practically interchangeable. Total_Marks_Pct is by construction highly correlated with its components (r = 0.91 with End-Sem marks, 0.71 with SGPA) and should not be entered into a model alongside them. Study hours, LMS logins, Library hours and Assignment-submission rate share a common factor (pairwise r ≈ 0.4–0.64) and would be collinear in a regression. Pearson and Spearman agree closely for nearly all pairs; correlations involving capped variables (Internal and Assignment marks) deserve extra caution because the ceiling compresses their variance.
14. Multivariate Analysis
Three or more variables were examined at once to see whether a relationship between two of them changes when a third is considered. Four investigations were made.
￼
Figure 15. Left: Study hours vs SGPA by internship status. Right: Travel time vs stress by residence.
￼
Figure 16. Final-year (semester 6–8) placement rate by CGPA band, internship and preparation quartile.
￼
Figure 17. Pair plot of four variables for final-year students, coloured by placement outcome.
Does the study-hours - SGPA relationship depend on internship status?
No, the regression slope is 0.156 SGPA points per study hour for students without an internship (r = 0.56) and 0.147 for those with one (r = 0.57) – nearly parallel lines. The intercept is a little higher for interns (4.80 vs 4.68): interns study more (median 4.3 vs 3.2 h) and therefore sit further to the right on the same line, which explains most of their higher SGPA. The relationship itself is the same in both groups.
Does the travel-time - stress relationship depend on residence?
Yes in strength, no in direction. Among day scholars each extra travel minute adds 0.015 stress points (r = 0.22); among hostel residents the slope is 0.016 (r = 0.07), almost flat because their commutes are short (median 8 min, almost none above 40). The overall pooled correlation of 0.22 is therefore driven mainly by the day scholars’ wide range of commute times.
Is the internship - placement gap just a CGPA or semester effect?
No, restricting to final-year students (semesters 6–8, where placement actually happens) and splitting by CGPA band, interns are placed more often in every band: 66% vs 52% (CGPA ≤ 5), 75% vs 61%, 85% vs 70% and 91% vs 80% (CGPA > 7, though only 369 non-interns and 592 interns). The advantage is 12–15 percentage points in every band, and placement also rises with CGPA among non-interns (from 52% to 80%): CGPA and internship contribute separately (an additive pattern, no interaction). The same holds across preparation-score quartiles (placed 51%-69% without internship, 64%-83% with).
Pair Plot: what separates placed from non-placed final-year students?
In the pair plot the placed (blue) and non-placed (grey) clouds overlap heavily on CGPA, study hours and preparation score, with the placed group shifted slightly towards higher values; the only visibly different pattern is internship months, where the 0-month column holds mostly non-placed students and the longer-internship bars hold mostly placed. No single variable separates the groups, so placement is only partly predictable from the recorded variables.
15. Group Comparison
The grouping variable chosen is Internship_Status (Yes / No), because it divides students into meaningful groups and is linked to the outcomes of interest. Group sizes are very unequal: 12,908 interns versus 77,507 non-interns (≈ 1 : 6), and interns are concentrated in semesters 5–8 (74% of interns are in semesters 6–8 versus 31% of non-interns).
￼
Figure 18. Distribution of three variables by internship status (violins with quartile lines).
Table 31. Group comparison by internship status
Variable
Internship
n
Mean
Median
Std dev
IQR
Range
CGPA
No
77,507
5.26
5.27
0.88
1.17
0.0 – 9.0

Yes
12,908
5.58
5.57
0.98
1.40
0.0 – 9.0
SGPA
No
78,313
5.27
5.27
0.71
0.92
0.0 – 7.9

Yes
13,053
5.51
5.52
0.75
1.00
0.0 – 8.1
Study Hours Per Day
No
76,963
3.80
3.23
2.54
3.16
0.0 – 14.0

Yes
12,840
4.84
4.27
2.92
3.86
0.0 – 14.0
Placement Preparation Score
No
77,875
62.53
62.19
15.36
20.59
0.0 – 100.0

Yes
12,992
69.70
69.46
15.17
21.05
0.0 – 100.0
Attendance Percent
No
77,205
62.57
62.47
13.04
17.59
0.0 – 100.0

Yes
12,912
64.97
64.95
13.55
18.17
0.0 – 100.0
Certifications
No
81,045
1.22
1.00
1.18
2.00
0.0 – 10.0

Yes
13,527
2.45
2.00
1.09
1.00
0.0 – 9.0

Table 32. Size of the group differences
Variable
Cohen’s d
Rank-biserial r
Distribution overlap
Mean difference
Median difference
CGPA
0.36
0.19
86%
0.32
0.30
SGPA
0.34
0.20
86%
0.24
0.25
Study Hours Per Day
0.40
0.22
84%
1.04
1.04
Placement Preparation Score
0.47
0.26
81%
7.18
7.27
Attendance Percent
0.18
0.10
92%
2.40
2.48
Certifications
1.06
0.59
42%
1.24
1.00

Interpretation-
Central Tendency and Spread- Interns have higher medians on every variable (CGPA 5.57 vs 5.27; study hours 4.27 vs 3.23; preparation score 69.5 vs 62.2) and slightly larger spread (IQR of CGPA 1.40 vs 1.17).
Overlap is Large- The two distributions share 86% of their area for CGPA and 84% for study hours; effect sizes are small to medium (d = 0.2–0.5 for CGPA, study, preparation; 1.06 for certifications). A randomly chosen intern has a CGPA above a randomly chosen non-intern only about 60% of the time – ‘interns have higher CGPA’ is true on average but would be wrong for many individual pairs.
Mean vs Median- They agree for CGPA and preparation score; for study hours both differ by about one hour, but the mean difference (1.04) is larger than the median difference (1.04) because interns also have a heavier right tail.
Outliers- 5.8% of interns exceed the study-hours IQR fence versus 2.6% of non-interns; heavy studiers are concentrated among interns.
Group-Size and Composition Caution- The unequal sizes do not threaten the comparison (the smaller group still has 12,908 students), but interns are mostly final-year. Because CGPA, study hours and preparation do not vary with semester in this data, restricting the comparison to semesters 5–8 gives the same picture (CGPA 5.58 vs 5.19, d = 0.44), so the difference is not a semester artefact.
Contrast Gender- Three gender groups of almost equal size have the same CGPA (mean 5.30–5.31), proving that a larger average is not always a real difference.
16. Time-Based Analysis
Valid Record_Date values exist for 98,723 records (98.8%) and span 01 Jan 2021 to 31 Dec 2026: exactly six calendar years. 1,230 dates are missing or invalid and 2,411 lie in the future relative to today (7 Nov 2026). Because Record_Date does not match Academic_Year in 33% of records, it is treated as the date the record was captured, not the academic term.
Daily counts average only ≈ 45 records and would be mostly noise; weekly counts (≈ 320) are still erratic; yearly aggregation leaves just six points and would hide any seasonal or one-off event. Monthly aggregation gives 72 points of about 1371 records each – enough for a stable count and enough points to see trend, seasonality and spikes. Outcome measures (CGPA, placement rate) were summarised by calendar year, because their monthly values would depend on much smaller subsets.
￼
Figure 19. Records per month (top) and records per day by calendar month (bottom).
￼
Figure 20. Yearly averages of key outcomes by record year.
Trend- Monthly counts show no trend: the fitted slope is +0.60 records per month (r = 0.14, p = 0.23). The series fluctuates around 1371 (SD 87).
Sudden Change- May 2024 stands out with 1,819 records, 5.1 standard deviations above average, with April 2024 also high (z = 2.2); this single burst is much larger than anything else and is the only genuinely unusual period. The lowest months, Feb 2021 and Feb 2026 (about 1,192), are explained by the short month.
Seasonality- Normalised per day, records are highest in April–June (≈ 276–279 per day) and lowest in January (≈ 262); the difference is small, about 6%, but statistically detectable (χ² = 44, p < 0.001), partly because of the 2024 spike. There is no sharp seasonal rhythm.
Outcomes Over Time- Mean CGPA is 5.29–5.31 in every year (Kruskal–Wallis p = 0.14); placement rate among semester 6–8 records stays between 63.5% and 64.5%; mean stress and study hours are flat. No trend or structural break is visible.
Academic Year- The five Academic_Year groups hold 19,400–20,100 records each and have the same mean CGPA (5.30–5.31).
Caution- Since the record date is unrelated to the academic year and contains impossible values, the time analysis describes data-entry activity, not students’ progression over time.
17. Analytical Questions
Six original questions (the assignment asks for five) were formulated from the patterns seen so far. None repeats the questions of the earlier sections.
At which stage of study does placement occur, and are early placements credible?
￼
Figure 21. Placement rate by semester.
Placement is a step function of semester: 1.7% of students in semesters 1–5 are recorded as Placed (1,016 of 60,047) versus 64.0% in semesters 6–8 (22,997 of 35,943); within each block the rate is flat. Internship experience shows the same step (0.3% of semester-1 students vs 28.7% in semester 5). The median package of early ‘placements’ is 7.5 LPA versus 10.0 for later ones, a different, rounded value. 
Placement belongs to the final three semesters; the 1,016 placements recorded earlier are implausible and should be verified. Any model of placement must control for semester or use only semesters 6–8.
Does a longer internship go with a higher placement rate and package?
￼
Figure 22. Final-year placement rate and median package by internship length.
Among final-year students, placement is 59% with no internship but 75–77% for any internship; length beyond 1–3 months adds nothing (the rate is flat at about 75–77% for 1–3, 4–6, 7–9 and 10–12 months). The median package of placed students rises from 9.9 LPA to about 10.5 LPA with any internship and then plateaus. So the benefit looks like a threshold (having done an internship) rather than a dose-response. Internship months correlate only 0.19 with package among placed final-year students.
Do ceiling effects distort the continuous-assessment marks?
￼
Figure 23. Histograms of Internal, Assignment and Quiz marks with the maximum marked.
59.8% of students score the full 40 internal marks and 89.6% the full 20 assignment marks, against only 10.6% for quizzes and 0.19% for the end-semester exam. Correlation with SGPA reflects this: 0.22 for assignments, 0.36 for internal marks, but 0.57 for quizzes and 0.61 for end-sem marks. Excluding students at the cap, internal marks correlate 0.39 with SGPA (n = 31,815), so the cap hides some information rather than creating false structure. 
Assignment and internal marks are weak discriminators because most students max out; quiz and end-semester marks carry the real signal. Total marks computed by summation inherit this imbalance.
Is the lower stress of hostel residents caused by hostel life or by shorter travel?
￼
Figure 24. Mean stress by travel-time band and residence (n shown as day scholars | hostel residents).
Overall, hostel residents are less stressed (4.80 vs 5.20) and travel far less (8.4 vs 29.9 min median). Comparing like with like, within travel bands ≤10, 11–20, 21–40 and >40 minutes the gap almost disappears (differences of 0.03, 0.03, 0.08 and 0.00 points), while stress rises steadily with travel time from 4.7 to 5.6. 
Travel time, not residence itself, accounts for the hostel advantage – a classic confounder. (Only 108 hostel residents travel more than 40 minutes, so that band mostly reflects day scholars.)
Are invalid entries concentrated in particular states, departments or semesters?
￼
Figure 25. Invalid numeric cells per record by state, and their distribution.
15.7% of records contain at least one rejected numeric cell; the average is 0.18 cells per record and no record has more than 5. The rate does not depend on state (mean 0.174–0.185, Kruskal–Wallis p = 0.15), department (p = 0.92) or semester (0.17–0.18). The variance-to-mean ratio of invalid cells per record is 1.11, close to 1, which is what independent random errors (a Poisson process) would produce. 
Data corruption is spread uniformly and randomly; no state, department or semester is responsible, so no group needs to be excluded or corrected separately.
Do students from richer families receive higher placement packages?
￼
Figure 26. Mean package by family-income quintile among placed students.
No, among 19,809 placed students with known income and package, mean package is 9.95–9.98 LPA in all five income quintiles (Kruskal–Wallis p = 0.50); the Pearson correlation is 0.000 and Spearman -0.008. In raw data income appeared to correlate with package (0.07) only because of sentinel codes.
In this dataset, salary is unrelated to family background; the strongest correlate of package is internship duration (r = 0.19). Absence of evidence in a single, possibly simulated file is not proof that income never matters.
18. Visualization Challenge
The 14 charts listed below were chosen so that every analytical role is covered; together with the one-page dashboard they communicate the most important findings of the EDA.
￼
Figure 27. One-page dashboard: twelve panels summarising the main findings.
Table 33. The final set of visualisations
#
Visualisation
Type
Where
1
Missing-value bar chart
Univariate / data quality
Figure 2
2
Missingness map
Data quality
Figure 3
3
Numeric histograms with density
Distribution
Figure 5
4
Categorical bar charts
Categorical
Figure 6
5
Raw vs validated box plots
Outlier
Figure 9
6
Scatter panel with binned means
Relationship
Figure 10
7
Box plots by category
Relationship (numeric × categorical)
Figure 11
8
100%-stacked bars
Categorical × categorical
Figure 12
9
Correlation heat-map
Correlation
Figure 13
10
Scatter with regression by group
Multivariate
Figure 15
11
Pair plot by placement outcome
Multivariate
Figure 17
12
Monthly time series + seasonality
Time-based
Figure 19
13
Placement rate by semester
Categorical / insight
Figure 21
14
Before/after distributions
Data-quality impact
Figure 28

Critique of Three Visualisations-
Critique 1: Correlation Heat-Map (Figure 13)
Why Chosen- 23 variables give 253 pairs; a colour matrix is the only compact way to see all of them at once.
What it Communicates / Easy to See- Three blocks – an academic-engagement cluster (warm colours), a small wellbeing cluster, and a large blank (white) region of unrelated variables such as Age, Family income, Extracurricular hours.
Hard to See- Non-linear relations, ceilings (internal marks), sample size per pair and statistical uncertainty; one-decimal labels (0.4 vs 0.45) hide detail.
Better Alternative- A clustered heat-map or a network plot for structure, and scatter plots for the few key pairs (used in Figure 10).
Could it Mislead? Colour intensity suggests importance although r = 0.3 is only 9% of variance; Pearson r understates relationships of capped variables; derived variables must be excluded.
Improvements- Order variables by cluster, show two decimals only where |r| ≥ 0.3, grey-out |r| < 0.1, and add n and a note that correlation is not causation.
Critique 2: Box Plots by Category (Figure 11)
Why Chosen- Box plots show median, quartiles and outliers of a numeric variable per group, ideal for comparing distributions.
Easy to See- The clear drop in income for scholarship holders, the shift in CGPA for interns and the identical SGPA across departments.
Hard to See- Group sizes, the density shape, the mean, and multimodality (the travel-time mixture would be invisible). With 90,000 points the ‘outlier’ dots form dense black bands that over-state how many extremes exist.
Alternative- Violin plots or ECDF curves show shape, and a table of n, mean and median adds precision (as in Figure 18).
Could it Mislead? The logarithmic income axis makes the gap look smaller than it is in rupees; identical box heights across departments might hide different tails.
Improvements- Annotate n and medians, thin or hide fliers, use a log axis with labelled lakh/crore ticks, and keep the same y-range across panels where comparable.
Critique 3: Placement Rate by Semester (Figure 21)
Why Chosen- A bar chart of percentages is the simplest way to show how a yes/no outcome changes along an ordered variable.
Easy to See- The dramatic step at semester 6 and the flat plateaus either side.
Hard to See- The absolute numbers behind each bar (≈ 200 early placements vs ≈ 7,600 later), uncertainty, and the fact that each ‘student’ may contribute several rows.
Alternative- A line chart with confidence intervals or a table of counts conveys the same information with uncertainty; a stacked bar (Figure 12) shows both outcomes.
Could it Mislead? The step may look like students being placed at semester 6 when it is a property of how the data were recorded; the 1.7% ‘early’ bars are almost invisible, hiding the suspicious cases.
Improvements- Print counts above bars, add error bars, highlight the early-placement anomaly and state that the denominator is records with a known status.
19. Before-and-After Analysis
Issue Selected- Impossible and sentinel numeric values (17,941 cells, 0.7% of numeric cells) together with text-coded missing values. ‘Before’ means the numbers exactly as typed in the file (text tokens ignored, nothing else removed); ‘after’ means the validated data. The same statistics were computed on both.
￼
Figure 28. Distribution of study hours and family income before (orange) and after (blue) validation.
Table 34. Descriptive statistics before and after validation
Variable
Mean before
Mean after
Median before
Median after
SD before
SD after
Skew before
Skew after
Age
22.38
20.83
21.00
21.00
36.09
1.97
26.4
0.15
Attendance Percent
63.15
62.92
62.86
62.82
53.52
13.13
-1.4
-0.03
SGPA
4.22
5.30
5.30
5.30
34.74
0.72
-28.6
-0.34
CGPA
4.17
5.30
5.33
5.30
36.66
0.90
-27.1
-0.18
Study Hours Per Day
3.01
3.95
3.37
3.37
33.88
2.63
-29.0
1.18
Family Income
1,497,644
596,844
449,468
449,190
25,626,246
519,548
35.5
3.15
Travel Time Minutes
32.46
24.98
17.05
16.98
132.79
23.56
24.0
1.84
LMS Logins Per Week
10.20
8.18
7.68
7.66
39.70
5.91
21.7
0.61
Stress Level
5.14
5.03
5.03
5.03
3.72
1.59
19.9
0.05
Student Satisfaction
6.73
7.77
7.82
7.82
33.74
1.42
-29.4
-0.33
Placement Preparation Score
63.93
63.55
63.24
63.22
20.96
15.55
8.6
0.01
Placement Package LPA
2.67
2.52
0.00
0.00
43.32
4.37
-5.4
1.20

￼
Figure 29. Correlation coefficients before and after validation.
Table 35. Correlations before and after validation
Pair
Pearson before
Pearson after
Spearman before
Spearman after
Study Hours Per Day × SGPA
-0.001
0.569
0.552
0.573
Attendance Percent × SGPA
0.008
0.368
0.354
0.369
Stress Level × Student Satisfaction
-0.004
-0.340
-0.319
-0.330
Placement Preparation Score × CGPA
0.009
0.459
0.427
0.456
LMS Logins Per Week × Study Hours Per Day
0.008
0.628
0.548
0.567
SGPA × CGPA
-0.001
0.748
0.713
0.766
Family Income × Placement Package LPA
0.071
0.004
0.007
0.004

What Changed, What Did Not-
Means and Standard Deviations Changed Enormousl- Mean SGPA moved from 4.22 to 5.30, mean Study hours from 3.01 to 3.95 and mean Family_Income from ₹14.98 lakh to ₹5.97 lakh. Standard deviations collapsed (SGPA 34.7 - 0.72; Age 36.1 - 1.97).
Medians did not change (SGPA 5.30 - 5.30, Study hours 3.37 - 3.37, Family income ₹4.49 lakh both) – the robust statistic was immune to the sentinels, which is another reason to prefer it.
Distributions Changed Shape- Skewness of SGPA went from -28.6 to -0.34; Age from 26.4 to 0.15. The raw histograms look like a single spike with unreadable axes; the validated ones show the true bell and skew shapes.
Relationships Changed Completely- The Pearson correlation of Study hours with SGPA was -0.001 (no relationship!) before validation and 0.57 after; Attendance × SGPA went from 0.008 to 0.37; Stress × Satisfaction from -0.004 to -0.34; SGPA × CGPA from -0.001 to 0.75. Spearman correlations (rank-based) were less affected (Study × SGPA 0.55 before vs 0.57 after).
A Spurious Relationship Vanished- Income × Package read 0.07 with sentinels and 0.004 afterwards.
What Did Not Change- Variables without sentinels (Lab attendance, Library hours, Assignment submission) are unaffected, and the categorical findings (placement by semester, by internship) are unaffected because they do not use the rejected cells. Attendance × Lab attendance stays at 0.83.
Interpretation Changed- With raw data one would conclude that study hours, attendance and stress are unrelated to results and satisfaction; with validated data the engagement–performance link is clear. Data cleaning is therefore not cosmetic: it reversed substantive conclusions.
A Second, Smaller Before/After: Category Labels-
Before standardisation the Gender column has 17 distinct labels and Department 41; counting ‘Information Technology’ alone would report 13,778 records, whereas the cleaned count is 14,268 (+3.6%). Group comparisons on uncleaned labels would drop or fragment about 3–4% of records in each column, but, because misspellings are spread evenly, the group-level conclusions (no gender or department differences) do not change.
20. Major Findings
The raw file is unusable without cleaning: all 26 numeric columns contain text, 17,695 cells are disguised missing values and 17,941 are impossible numbers; 47 rows are blank.
Categories were fragmented by spelling (Gender 17 labels for 3 groups, State 32 for 8, City 87 for 25, Department 41 for 7, Programme 67 for 20).
Missing data are thin (≤ 10% per column) and statistically random; no variable was dropped and nothing was imputed.
Duplicates are limited to the blank rows, but Student_ID is repeated (4.6 rows per ID) with 25,052 repeated ID–semester pairs.
Typical student: CGPA 5.30, attendance 63%, 3.4 study hours a day, family income ₹4.49 lakh; income, travel time and study hours are right-skewed, marks are capped.
Study hours (r = 0.57 with SGPA), LMS logins, library hours, submission rate and attendance form one engagement–performance cluster; Attendance and Lab attendance are almost redundant (r = 0.83).
Placement happens in semesters 6–8 (64% placed) and almost never earlier (1.7%, 1,016 suspect records).
Among final-year students, interns are placed more often (79% vs 66% on average) in every CGPA band, but internship length beyond one to three months adds nothing.
Scholarship follows family income (median ₹2.7 lakh vs ₹5.7 lakh); stress follows travel time rather than hostel status.
Gender, department, state, academic year and family income show no relationship with CGPA or placement; package is unrelated to income.
No time trend or seasonality of substance: monthly counts are stable apart from one spike in May 2024.
Validation changed conclusions: correlations of –0.001 to 0.01 became 0.37–0.75 and a spurious income–package correlation vanished.
21. Limitations
Repeated and Ambiguous Records- The 21,729 Student_IDs appear 4.6 times on average, 25,052 rows repeat an ID + semester and attributes such as Programme, e-mail and phone differ within an ID. The unit of analysis is a student–term record, students with more rows weigh more, and observations are not independent, so standard errors and p-values are too optimistic.
Probably Simulated Data- Perfectly balanced categories, round-number sentinel spikes of nearly identical size, and many variables with zero correlation (age, income, extracurricular hours, department, gender) suggest a synthetic teaching dataset. Findings describe this file and should not be generalised to real institutions.
Association, Not Causation- The data are observational and cross-sectional; internship, study hours, hostel residence and the like may be confounded by unobserved factors (motivation, ability, location).
Missing and Rejected Values- Only 17% of rows are fully complete; 17,941 impossible values were set to missing without imputation. Pairwise sample sizes vary from about 82,000 to 95,000, and randomness of the gaps (MCAR) cannot be proven.
Judgement-Based Validity Rules- Thresholds (e.g. study ≤ 16 h, income < ₹1 crore, package ≤ 50 LPA, CGPA < 9.5) were chosen from domain reasoning and spikes in the data; different thresholds would reject slightly different cells. Zero spikes in attendance, study hours and marks were kept.
Ceiling and Floor Effects- 60% of internal marks and 90% of assignment marks sit at the maximum, and 74% of project scores at the floor of 20. These compress variance and weaken correlations, and Total_Marks (assumed to be out of 160) inherits the problem.
Undefined Units and Scales- The currency and period of Family_Income, the direction of Travel_Time, the maximum of project score, the measurement of Stress and Satisfaction and the meaning of Record_Date are not documented and were assumed.
Unreliable Dates- 33% of Record_Date values precede their academic year, 2,411 are in the future and 1,013 are invalid, so the time analysis reflects data entry, not student progress.
Contradictory Outcome Records- 1,016 placements in semesters 1–5, 234 non-placed students with a salary and 953 internship contradictions remain in the data; placement is also a single-time outcome, with no timestamp.
Statistical Significance with huge Samples- With n ≈ 90,000 almost every difference is ‘significant’; this report therefore relies on effect sizes, but even these can mislead when the observations are not independent.
Personal Data- Names, e-mails and phone numbers are present and were excluded; the file should be anonymised before wider sharing.


22. Final Data Story
What is this dataset about? 
100,000 records of Indian undergraduate students – their attendance, marks, grades, habits, background, internships and campus placement – captured between 2021 and 2026. It was delivered in a deliberately messy state.
Most Important Characteristics and Data-Quality Issues-
After removing 47 blank rows, the file holds 99,953 student–term records for 21,729 Student_IDs. Every numeric column was polluted: 17,695 disguised-missing text cells, 17,941 impossible or sentinel numbers (999, –999, 1000, 1e9), spelling variants that inflated Gender from 3 to 17 labels and Department from 7 to 41, mixed date and year formats, and logical contradictions (placements in semester 1, 33% of records dated before their academic year, internships with zero months). Missing values are random and thin (≤ 10% per column); duplicates are only blank rows, but IDs repeat.
Major Patterns-
Once cleaned, the data show a coherent academic story: study hours, engagement (LMS logins, library, assignment submission), attendance and marks move together, with SGPA 0.57 correlated with study hours and total marks 0.74 with study hours. Placement is a final-year event (64% of semester 6–8 records versus 1.7% earlier). Interns are placed more often (76% vs 59%), at every CGPA level; the gain exists for any internship but does not grow with its length.
Noteworthy Relationships-
Attendance and lab attendance are nearly the same variable (r = 0.83); scholarship status follows family income (median ₹2.7 vs ₹5.7 lakh); stress follows travel time (r = 0.23) and the apparent hostel advantage disappears once travel time is held fixed.
Unexpected Observations-
Family income, age, gender, department, state and extracurricular time have no relationship with academic results or placement; categories are almost perfectly balanced; assignment marks are at the maximum for 90% of students; there is a single May-2024 spike in records; and raw data had made income appear correlated with salary, which was entirely an artefact.
Findings Requiring Caution-
Associations are not causal (internship → placement, study → grades). Early placements, mismatched cities/states and programmes, and the dates are unreliable. Group differences with very large samples are statistically significant yet small (d ≈ 0.2–0.5). Because the same student appears several times, effective sample size is smaller than 100,000.
Useful Additional Information would be a unique student-term key, true term dates, definitions of units and scales, employer and internship details, and longitudinal follow-up.
Major limitations are the repeated records, thresholds chosen by judgement, ceiling effects in marks, the apparent simulated nature of the data and the unreliable date field (detailed in the Limitations section).

23. Conclusion
This exploratory analysis took a deliberately messy file of 100,000 student records and turned it into an analysable dataset of 99,953 validated student–term records, without imputing a single value and without deleting any plausible extreme observation. The investigation showed that the data quality problems – text-coded missing values, sentinel numbers, inconsistent spelling, impossible dates and contradictory outcomes – were large enough to hide the true relationships entirely: study hours and SGPA appeared uncorrelated before cleaning (r = –0.001) and clearly related afterwards (r = 0.57).
Once cleaned, the data describe students whose academic performance moves with their study effort and engagement, whose placement outcomes occur in the final three semesters and are more frequent among those who completed an internship, and whose stress depends more on commuting time than on where they live. Demographic and background factors, in contrast, are unrelated to results. The analysis also exposed structural weaknesses – repeated student IDs, ceiling effects in marks, unreliable record dates and an apparently simulated structure – and the findings should be read as associations in this particular dataset. The recommended next steps are a unique student–term key, documented units, validated dates and longitudinal follow-up before any causal or predictive modelling is attempted. 
