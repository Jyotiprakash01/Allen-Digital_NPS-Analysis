# Allen Students - NPS Survey Analysis

## Objective: 
Analyze NPS survey responses to understand student satisfaction and dissatisfaction drivers.

## Data Overview: 
~1000 student responses collected.

## Purpose: 
Identify key improvement areas and strengths of the Allen Learning App.

DataSet :https://github.com/Jyotiprakash01/Allen-Digital_NPS-Analysis/blob/main/NPS%20Dec-Jan%20Raw%20data.xlsx

## Tools Used:
Excel, Power BI and Python

## NPS Sentiment Distribution 
- A healthy 52% of students are strong advocates.
- 20% are dissatisfied or had challenges in their experience.
- The remaining 28% are neutral and can be converted to promoters.

# NPS Survey Data Summary
- Total Responses: 1000
- Promoters (Score 9–10): 521 (52.1%)
- Passives (Score 7–8): 276 (27.6%)
- Detractors (Score 0–6): 203 (20.3%)
- Net Promoter Score (NPS): 31.8

![image](https://github.com/user-attachments/assets/719f8507-dbb3-4dbe-875f-c3d465f6338b)

## Summary of Ratings for each feature by NPS Segment
![image](https://github.com/user-attachments/assets/1b2e1820-1e79-4c35-bb89-56578aa32104)

1. Promoters (NPS 9-10) – 521 Students
- Gave highest scores across every parameter.
- Particularly strong appreciation for:
    Live Class (4.58) – Highest rated feature.
    App’s Overall Look & Feel (4.56)
    Test and its insights (4.50)
    Ease of finding info (4.46)

Takeaway: These are your strongest areas and key reasons for high satisfaction. Highlight them in marketing and double down on improving even further.


2. Passives (NPS 7-8) – 276 Students
- Rated most features between 3.4 – 4.1.
- Top features:
    Live Class (4.13)
    Look & Feel (4.11)
    Test and Insights (4.07)
    Lowest among passives:
    Break sessions (3.00)
    Mentorship (3.57)
    Homework & Custom Practice (3.45)

Takeaway: These users are on the fence. Improving their weaker rated experiences may help convert them to Promoters.

3. Detractors (NPS 0-6) – 203 Students
- Gave consistently low scores (mostly below 3.5).
- Poorest ratings in:
    Break Sessions (2.36) – Lowest of all.
    Homework (2.91)
    Improvement Book & Custom Practice (2.90)
    Mentorship (2.71) – Huge dissatisfaction gap vs promoters (4.10)

Takeaway: These are the main pain points causing dissatisfaction. Focus efforts here to reduce churn and improve experience.

## Recommendations for features

For Product/Operations Teams:
- Redesign or improve:
- Mind & body sessions
- Mentorship structure
- Homework & Custom Practice tracking/usability

For Marketing Teams:
- Highlight top-rated features loved by Promoters in your campaigns.
- Consider using quotes from “Test & Insights” and “Live Class” experiences.

For Support/Onboarding:
- Provide better guidance/tutorials for Detractors to find features and use Homework/Practice modules.

## Data Preparation for Qualitative Responses

- Split multiple statements in a single cell based on delimiter (commas) in Column C: "Top improvements suggested" and Column D: "Positive aspects liked"
- To execute this, I've uploaded the excel into Google Colab. Ensured each suggestion/feedback point was treated separately.

```python
import pandas as pd
df = pd.read_excel("[Assignment] NPS Dec-Jan (1) (2)(1).xlsx")
display(df.head())
```









