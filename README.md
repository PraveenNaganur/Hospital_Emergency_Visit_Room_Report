# Hospital_Emergency_Visit_Room_Report


% NonAdministrative Schedule =
DIVIDE(
    COUNTROWS(FILTER('Hospital ER', 'Hospital ER'[patient_admin_flag] = FALSE())),
    [Total Patients]
)


Patient Ratings & Wait Times

Preview
Raw
Project Title
Patients Emergency Room Visit Report
Dashboard Link : https://app.powerbi.com/groups/me/reports/247cf6d3-4bdc-487d-b29d-df3bcaf95613/b1e6575002ae91b0c17d?experience=power-bi
Problem Statement
Emergency departments are critical touchpoints in healthcare systems, often managing a high influx of diverse patients under time-sensitive conditions. However, hospitals frequently face challenges in understanding trends and patterns related to ER visits, such as peak hours, patient demographics, referral sources, and satisfaction levels. Without clear visibility into these factors, it becomes difficult to allocate resources efficiently, reduce wait times, and improve patient satisfaction.

This Power BI dashboard addresses these issues by offering a comprehensive visualization of emergency room visits. It includes insights on total patient volumes, appointment types, average wait times, patient satisfaction, gender and age group distributions, department referrals, and time-based visit patterns. The report aims to equip healthcare decision-makers with actionable data to streamline ER operations, enhance service quality, and support data-driven strategic planning.

Steps followed
Step 1: Load Data

Import the CSV file into Power BI Desktop with all relevant columns.

Step 2: Open Power Query Editor

Enable Column Quality, Distribution, and Profile from the View tab.

Ensure proper data types (e.g., Date as Date, patient_id as text).

Detect and address null values in satisfaction score and wait time.

Step 3: Profile Entire Dataset

Switch profiling mode from default (first 1,000 rows) to the full dataset.

Step 4: Null Value Handling

Use <> BLANK() in DAX to exclude nulls from calculations.

Use visual-level filters to remove blanks from charts.

Key DAX Measures

Total and Categorized Counts

Total Patients = COUNTROWS('Hospital ER')

% Administrative Schedule = DIVIDE( COUNTROWS(FILTER('Hospital ER', 'Hospital ER'[patient_admin_flag] = TRUE())), [Total Patients] )

% NonAdministrative Schedule = DIVIDE( COUNTROWS(FILTER('Hospital ER', 'Hospital ER'[patient_admin_flag] = FALSE())), [Total Patients] )

Patient Ratings & Wait Times

Average Satisfication Score = CALCULATE( AVERAGE('Hospital ER'[patient_sat_score]), 'Hospital ER'[patient_sat_score] <> BLANK() )

% No Rating = VAR _NoRatings = CALCULATE([Total Patients], 'Hospital ER'[patient_sat_score] = BLANK()) RETURN DIVIDE(_NoRatings, [Total Patients])

Average Wait Time = AVERAGE('Hospital ER'[patient_waittime])

Referral Metrics

% Referred Patients = VAR _FilterPatients = CALCULATE([Total Patients], 'Hospital ER'[department_referral] <> "none") RETURN DIVIDE(_FilterPatients, [Total Patients])

% Un Referred Patients = VAR _FilterPatients = CALCULATE([Total Patients], 'Hospital ER'[department_referral] = "none") RETURN DIVIDE(_FilterPatients, [Total Patients])

Chart Point Flags

CF Max Point (Month) = VAR _PatientTable = CALCULATETABLE( ADDCOLUMNS(SUMMARIZE('Date','Date'[Month]), "@Total_Patients",[Total Patients]), ALLSELECTED() ) VAR _MinValue = MINX(_PatientTable,[@Total_Patients]) VAR _MaxValue = MAXX(_PatientTable,[@Total_Patients]) VAR _TotalPatients = [Total Patients] RETURN SWITCH(TRUE(), _TotalPatients = _MinValue,"0", _TotalPatients = _MaxValue,"1")

Gender-Based Metrics

% Female Visit = DIVIDE(CALCULATE([Total Patients],'Hospital ER'[patient_gender]="F"), [Total Patients]) % Male Visit = DIVIDE(CALCULATE([Total Patients],'Hospital ER'[patient_gender]="M"), [Total Patients]) % Unknown = DIVIDE(CALCULATE([Total Patients],'Hospital ER'[patient_gender]="NC"), [Total Patients])

Age Grouping Columns

Age Buckets = SWITCH(TRUE(), 'Hospital ER'[patient_age] <= 10, "0-10", 'Hospital ER'[patient_age] <= 20, "11-20", 'Hospital ER'[patient_age] <= 30, "21-30", 'Hospital ER'[patient_age] <= 40, "31-40", 'Hospital ER'[patient_age] <= 50, "41-50", 'Hospital ER'[patient_age] <= 60, "51-60", 'Hospital ER'[patient_age] <= 70, "61-70", "70+" )

Age Group = VAR _PatientAge = 'Hospital ER'[patient_age] RETURN IF( _PatientAge <= 2, "Infancy", IF(_PatientAge <= 6, "Early Childhood", IF(_PatientAge <= 12, "Middle Childhood", IF(_PatientAge <= 18, "Teenager", "Adult"))))

Parameter Table for Dynamic Measure

Parameter = { ("Avg.Satisfication", NAMEOF('Calculations'[Average Satisfication Score]), 0), ("Avg. Wait Time", NAMEOF('Calculations'[Average Wait Time]), 1) }
