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
![image](https://github.com/user-attachments/assets/474c288f-b122-4f51-b18a-6107fab3e9f5)

## Data Exploration

```python
print("DataFrame Shape:", df.shape)

print("\nDataFrame Columns:", df.columns)

print("\nDataFrame Data Types:\n", df.dtypes)

column_c_name = 'What are the top things that we should do to improve your rating for Allen? '
column_d_name = 'What are the things you like the most about studying with Allen?'
print(f"\nFirst few rows of '{column_c_name}' and '{column_d_name}':\n")
display(df[[column_c_name, column_d_name]].head())

print(f"\nMissing values in '{column_c_name}' and '{column_d_name}':\n",
      df[[column_c_name, column_d_name]].isnull().sum())
```
![image](https://github.com/user-attachments/assets/c9acab53-d9f5-4e45-a947-070ba8d98937)
![image](https://github.com/user-attachments/assets/1aa6f7c2-d65b-49c2-894a-ff9e3c7e4c1c)
![image](https://github.com/user-attachments/assets/0f56abc4-6b3d-4c22-94d1-9cbad6187aa1)

## Data wrangling
Splitting the strings in 'What are the top things that we should do to improve your rating for Allen? ' (Column C) into three separate columns, handling commas outside parentheses.

```python
import re

def split_string_by_commas(input_string):
    """Splits a string by commas, ignoring commas within parentheses.

    Args:
    input_string: The string to split.

    Returns:
    A list of three strings, padded with empty strings if necessary.
    """
    if not isinstance(input_string, str):
        return ['', '', '']

    # Split the string by commas outside parentheses
    parts = re.split(r',\s*(?![^()]*\))', input_string)

    # Truncate the list to ensure 3 elements
    parts = parts[:3]
    while len(parts) < 3:
        parts.append('')

    return parts

column_c_name = 'What are the top things that we should do to improve your rating for Allen? '
df[['C1', 'C2', 'C3']] = df.apply(lambda row: pd.Series(split_string_by_commas(row[column_c_name])), axis=1)
display(df.head())
```
![image](https://github.com/user-attachments/assets/6c79923e-329f-4933-897a-34b91555e0ee)

Splitting the strings in 'What are the things you like the most about studying with Allen?' (Column D) into three separate columns, handling commas while treating specific phrases as single entities.

```python
import re

def split_column_d(text):
    if not isinstance(text, str):
        return ['', '', '']

    patterns = [
        r"Features in the app like practice tests, homework, Improvement Book, etc.",
        r"Interactive features in class like chat, bring on stage, polls, etc."
    ]

    for i, pattern in enumerate(patterns):
        text = re.sub(pattern, f"__PLACEHOLDER_{i}__", text)

    parts = re.split(r',\s*', text)

    for i, pattern in enumerate(patterns):
        parts = [re.sub(f"__PLACEHOLDER_{i}__", pattern, part) for part in parts]

    parts = parts[:3]
    while len(parts) < 3:
        parts.append('')
    return parts

column_d_name = 'What are the things you like the most about studying with Allen?'
df[['D1', 'D2', 'D3']] = df.apply(lambda row: pd.Series(split_column_d(row[column_d_name])), axis=1)
display(df.head())
```
![image](https://github.com/user-attachments/assets/877a1bfe-3e1c-4d4b-b70c-8351a3dfd644)

Rename the newly created columns and concatenate them with the original DataFrame.

```python
df = df.rename(columns={'C1': 'Statement_C1', 'C2': 'Statement_C2', 'C3': 'Statement_C3'})
df = df.rename(columns={'D1': 'Statement_D1', 'D2': 'Statement_D2', 'D3': 'Statement_D3'})
display(df.head())
```
![image](https://github.com/user-attachments/assets/1b54f834-171a-4a80-832e-c6f94d4736e0)

## Model Evaluation

```python
import random

sample_size = 20
random_indices = random.sample(range(len(df)), sample_size)
sampled_df = df.iloc[random_indices]

columns_to_display = [
    'What are the top things that we should do to improve your rating for Allen? ',
    'What are the things you like the most about studying with Allen?',
    'Statement_C1', 'Statement_C2', 'Statement_C3',
    'Statement_D1', 'Statement_D2', 'Statement_D3'
]
display(sampled_df[columns_to_display])
```
![image](https://github.com/user-attachments/assets/638b5d67-97e3-4af7-85d9-86366bcbe8cb)

Save the modified DataFrame `df` to a new Excel file named "modified_nps_data.xlsx".

![image](https://github.com/user-attachments/assets/722a0f5a-c5bf-43f3-bf94-f957e4d97359)

Modified NPS dataset: https://github.com/Jyotiprakash01/Allen-Digital_NPS-Analysis/blob/main/modified_nps_data.xlsx

# Analysing the statements in columns - Statement_C1', 'Statement_C2', 'Statement_C3', 

## Improve Ratings of Allen – Priority 1
![image](https://github.com/user-attachments/assets/cd870d00-acf4-4f25-b29c-e5f68774f15a)

Key Observations
- Students expect quicker answers to doubts raised on the app. Delayed doubt resolution is impacting their learning flow.
- Learners are requesting more convenient or shorter class timings to better fit their schedules.
- Many students want support to revisit missed topics through revision sessions or extra classes.

## Improve Ratings of Allen – Priority 1 by each segment
![image](https://github.com/user-attachments/assets/dc0bc2fa-8e2f-486c-8c9b-ad94fb6fd721)

Top 3 Feedback by Categories 
- Even Allen’s happiest students want quicker resolution of doubts in app and support to revisit missed topics.
- Passive students are looking for better structure and support. Providing personal guidance and flexible schedules could turn them into Promoters.
- Detractors are most concerned with teaching quality and engagement. This indicates a need to upskill faculty and enhance classroom delivery to prevent chur

## Improve Ratings of Allen – Priority 2
![image](https://github.com/user-attachments/assets/01055e48-f4cf-4096-a2b5-9ad9ac0e40b1)

Key Observations
- Students want sessions that are more engaging, not just lectures.
- Delays in resolving doubts affect trust and learning continuity.
- Students want personalized tips to improve marks and exam strategy.

## Improve Ratings of Allen – Priority 2 by each segment
![image](https://github.com/user-attachments/assets/71978e4f-2017-45df-b433-77529862227f)

Key Observations
- Promoters expect a Faster doubt resolution and seamless tech experience
- Passive group seeks engagement and extra academic support.
- Detractors find teaching uninspiring or unclear. There's a demand for better-trained faculty and clearer concepts.

 ## Improve Ratings of Allen – Priority 3
![image](https://github.com/user-attachments/assets/e1a7538f-6275-4cf7-a760-40b4710476c6)

Top 3 Feedback by Categories 
- Even as a third priority, need for Interactive classes continues to rank highest — confirming it's a core concern across the board.
- Students across categories consistently express the need for one-on-one or structured academic mentoring.
- Even at third priority, many students still want catch-up classes, suggesting possible gaps in absorption or attendance.

## Key Findings

- Quick Doubt Resolution Is the Top Concern - Students feel delayed responses on the app are disrupting their learning flow. Doubt clarification is critical for concept retention and exam prep.
- Interactive Classes Are Consistently in High Demand - Ranked highest in Priority 2 and still top in Priority 3, showing it's not just a "nice-to-have" — it's a core learning need.
- The need for better engagement and teaching methods persists across all priority levels.
- This suggests a systemic gap in delivery style — even those not listing it as their first priority still feel it's important.

## Strategic Suggestions
1. Improve Doubt Resolution Turnaround Time
- Set SLAs for doubt resolution (e.g., within 6–12 hours)
- Introduce subject-wise doubt mentors or teaching assistants to share the load
- AI-based doubt suggestions (pre-fed answers to FAQs) to speed up response

2. Make Classes More Interactive
- Include polls, breakout rooms, quizzes, real-time Q&A
- Encourage student presentations, peer teaching, or case studies

3. Train Faculty on Engagement Techniques
- Conduct workshops on active learning, storytelling, and ed-tech tools
- Use a standard “Engagement Checklist” for every session
- Introduce post-class surveys to track perceived interactivity levels

4. Track Impact with Data
- After implementation, re-run the same survey to see shifts in:
- Number of Detractors vs Promoters
- Repeat concerns around doubts or interactivity
- Monitor app engagement analytics and class participation metrics

5.  Communicate Improvements
- Share updates like:
- “Doubt response time now down to 8 hours average!”
- “3 new ways we’re making your classes more engaging!”


# Analysing the statements in columns - 'Statement_D1', 'Statement_D2', 'Statement_D3'

## Most liked about Allen – Priority 1
![image](https://github.com/user-attachments/assets/8d98318a-7150-4ee8-aaf8-29b976426f50)

Key Observations
- Teachers are the biggest strength (189 responses)- The highest number of students (18.9%) highlighted that the teachers are really good and explain well, indicating that faculty quality is the most appreciated aspect of the learning experience.
- A close second is the study modules and practice questions, suggesting strong student satisfaction with the core academic content.
- Features like practice tests, homework, Improvement Book, etc. are valued by over 13% of students, showing that the digital content support is effective.

## Most liked about Allen – Priority 1 by each segment
![image](https://github.com/user-attachments/assets/0ff9a7a1-0467-437e-bca1-b61a34377673)

Key Observations
- Teacher Quality – With 70% promoter responses, this is your strongest differentiator. Even Detractors and Passives acknowledged teacher quality to some extent.
- Highest absolute count of mentions (187), but Promoter share is 44%, with 35% Passive and 20% Detractor. Indicates students find the material valuable but not always excellent.
- Doubt Resolution – Useful but Underutilized. Low mention count (46) despite decent promoter share (57%). Many students may not be using or aware of this feature.

## Most liked about Allen – Priority 2
![image](https://github.com/user-attachments/assets/b82d5507-81e7-4ab6-a5a0-9661ccc50f2c)

Key Observations
- Study modules and practice questions are even more appreciated here (17.6%), showing that structured, high-quality content is critical for learners.
- Once again, teachers being good and explaining well is the most appreciated feature, cited by 15.4% of students. Faculty quality continues to be the biggest strength.
- Practice tests, homework, and the Improvement Book are recognized by 14.8% of students, reaffirming the strength of app-based learning support.

## Most liked about Allen – Priority 2 by each segment
![image](https://github.com/user-attachments/assets/bce25a81-348e-4897-9f10-395a74e18ff4)
Key Observations
- Again, Teacher’s Quality Most liked feature. 58% Promoters and only 14% Detractors. Confirms that teacher quality = top driver of satisfaction..
- Study Modules & Practice Questions Highest total mentions (176), but only 45% Promoters. 21% Detractors – possible inconsistency in perception.
- App & Feature Usage. 148 total mentions, but just 49% Promoters. 25% Detractors shows room for UX or discovery improvement.

## Most liked about Allen – Priority 3
![image](https://github.com/user-attachments/assets/99d78523-33a3-4645-8b65-e78d71c6a73b)
Key Observations
- The most appreciated feature by far is the study modules and practice questions, cited by 20.6% of students – indicating that the quality and usefulness of academic material is resonating more than ever.
- 13.8% of students value the teachers’ clarity and teaching style, maintaining faculty quality as a consistent strength across all priorities.

## Most liked about Allen – Priority 3 by each segment
![image](https://github.com/user-attachments/assets/6d7294ee-b523-482e-b0cd-2feddb9401ba)

Key Observations
- Study Modules & Practice Questions. Most mentioned (206). High satisfaction (54% Promoters), though 20% Detractors suggest some students may feel overwhelmed or confused.
- Teacher Quality. 60% Promoters, only 12% Detractors. Continues to be Allen’s strongest driver of delight.

## Key Findings - What’s Working Well
Study Content is the Strongest Asset
- Highest-rated consistently across all priorities, with nearly 1 in 5 students selecting it.
- Indicates students deeply appreciate structured, relevant, and high-quality academic material.
Faculty Quality Remains a Core Strength
- Teachers are consistently appreciated for clarity and quality of explanation.
- Critical for brand trust and retention.
Digital Tools and Platform Usability are Strong
- App-based features and platform ease of use rank in the top 5 across all priorities.
- Indicates a positive digital learning experience.

## Suggestions for Improvement
1. Boost Doubt Resolution Mechanisms
- Add structured doubt-clearing slots post-class.
- Introduce live doubt-clearing hours or 1:1 support chats.
- Use a ticket-based doubt system to ensure nothing is missed.

2. Make Mentorship Visible & Accessible
- Assign each student a mentor teacher and promote it clearly in onboarding.
- Run regular mentorship webinars or 1:1 career guidance sessions.
- Showcase mentor success stories and testimonials to improve visibility.

3. Enhance Classroom Engagement
- Train faculty to actively use chat, polls, stage-invite features.
- Introduce gamified quizzes and peer interaction elements.
- Use analytics to track participation and follow up with low-engagement students.

4. Refine Curriculum Design
- Collect regular feedback on pace & difficulty.
- Consider adding optional remedial sessions or advanced content to suit different learner needs.

# Analyzing ALLEN’s Customer Helpline Feedback

![image](https://github.com/user-attachments/assets/43998aed-465b-4fdd-be79-7ff87ba176c0)

Key Findings:
- Immediate Support is a Major Driver of Satisfaction. This shows quick resolution is directly tied to positive brand sentiment.
- Delayed Support Still Retains Goodwill — but Not Always. Suggesting some tolerance for slower support, as long as issues get resolved. However, Passive responses (90) are high here – indicating a risk zone for future dissatisfaction.
- Not very satisfied with customer support” has the highest Detractor count. This is a red flag, showing that when support fails, it deeply impacts perception.

Recommendations for ALLEN’s Customer Helpline

1. Prioritize Fast Resolution 
- Set a 24-48 hours response benchmark for queries.
- Highlight “Fast Support Guarantee” in communications and app banners.
- Use automation + escalation workflows to avoid pile-ups.
2. Enhance Communication for Slower Cases
- For complex or delayed queries, send updates like:
- “We’re working on it. Expect resolution in 2 days. Thanks for your patience!”
- Offer temporary resources or suggestions in the meantime (FAQs, video help, peer support).
3. Fix the Dissatisfaction Zone
- Identify and audit past cases marked as “Not very satisfied.”
- Assign a Quality Review Taskforce to assess how those were handled.
- Empower support staff with better training, scripts, and decision-making tools.
4. Make Support More Visible and Approachable
- Add a prominent “Ask for Help” button in the app/classroom dashboard.
- Run a quick in-app poll: “Have you used our helpline? Was it helpful?”
- Share positive support stories/testimonials from students who've had issues resolved well.
5. Convert ‘Non-Users’ into Advocates
- For the 171 students who never contacted support, send:
- “Here’s how to get instant help when stuck — meet your Support Team.”
- Conduct a Support Awareness Campaign with bite-sized help tips weekly.

# Analyzing "How many wish to continue next year?" column
![image](https://github.com/user-attachments/assets/97e0ed54-dfe0-44ff-acb7-cb82e2f0cb34)

Key Findings :
- Strong Loyalty from Promoters- A massive 373 out of 521 Promoters (72%) said “Yes, 100%” — showing clear satisfaction and loyalty.
- Passives Show Decent Retention Intent — But Room for Persuasion. 59% said yes but 32% are at risk.
- Only 50 Detractors (25%) are certain to continue. 78 (38%) said “Mostly no” and another 75 are undecided – a clear indicator of dissatisfaction and dropout risk.

Recommendations for Allen:
1. Double Down on Promoter Retention
- Send “Thank you for your trust” renewal messages to Promoters.
- Offer early-bird renewal discounts, loyalty badges, or certificates.
- Encourage Promoters to share testimonials or refer friends.
2. Target the Undecided with Personalized Nudges
- Run a “Still thinking? Here's why Allen is your best bet” campaign.
- Share: Success stories, Improvements planned for next year, Faculty line-up & curriculum teasers
- Consider a limited-time offer to nudge decision-making (discounts, bonuses, etc.)
3. Conduct Exit Interviews with Detractors
- For those saying “Mostly No,” use follow-up calls or surveys to ask:
- “What would make you stay?”
- Offer them a chance to try new features or improved offerings with a free trial or mentoring session.
4. Improve Experience for Passives
- Identify what’s lacking (e.g., app usability, slow support, study intensity).
- Focus on academic experience and emotional connect — two levers that help turn Passives into Promoters.
5. Build Retention Communication into App Journey
- Add nudges like:
- “Enjoying your learning? Lock in next year now!”
- “Here’s what’s coming next year – be the first to benefit.”
- Use push notifications, SMS, and WhatsApp for gentle but regular reminders.

# Analyzing "What are the main reasons you do not wish to continue learning with ALLEN next year?" Open Ended Feedback column
![image](https://github.com/user-attachments/assets/9bf00e00-1bc5-4b1e-8ce4-7c5757bc12db)

Key Findings from Word Cloud image:
- Words like "teachers," "teaching," "Faculty," "explaining," and "quality" highlight concerns about the effectiveness of instruction.
- Specific complaints include "poor," "worst," and "unable to understand," suggesting dissatisfaction with faculty knowledge or delivery.
- Terms such as "interactive," "interaction," and "mentorship" indicate a demand for more engaging and personalized learning experiences.
- "Doubt," "resolved," "questions," and "helpline" appear prominently, pointing to issues with timely or effective doubt-solving mechanisms.
- Words like "app," "tech issue," "live class," and "recordings" reflect frustrations with the digital platform’s reliability (e.g., buffering, functionality). "Timings" and "manage" suggest logistical challenges with scheduling or class duration.
- Terms like "marks," "neet," "exams," and "qualify" reveal anxiety about academic performance and coNegative sentiments ("disappointed," "won’t recommend") imply some students may not continue due to unmet expectations.
- Words like "expensive," "fees," and "paid" indicate financial concerns, with some students questioning the ROI of the program

Recommendations:
- Faculty Training: Focus on pedagogical skills and clarity in explanations.
- Tech Upgrades: Optimize app performance, especially for doubt-solving and live classes.
- Student Engagement: Introduce interactive tools (e.g., Q&A sessions, mentorship programs).
- Feedback Loop: Regularly collect and act on student feedback to address specific pain points (e.g., timings, fees).


# Final Takeaways
- Majority are happy, but time to resolution is a concern → focus on speed and efficiency
- Detractors need immediate attention → treat them like an early warning system
- Unengaged users are a missed opportunity → nurture them with proactive support visibility
- Survey strategy needs to evolve → deeper insights come from context, segmentation, and tracking change over time
- Improve Speed Without Compromising Quality
    Set internal SLAs (e.g., <24 hrs resolution for 80% of cases)
    Introduce AI chatbots and automated FAQs for faster replies
- Detractor-Centric Interventions
    Identify and re-engage students marked as Detractors | Offer personalized follow-up or service recovery gestures | Use feedback loops to analyze why they rated low
- Proactive Support for Non-Contacted Students
    17.1% never used customer support | Run a pulse check campaign: “We’re here for you. Have you ever faced an issue but didn’t know whom to ask?” | Create onboarding videos or WhatsApp nudges to promote support usage
- Enhance Promoter Loyalty
    Thank and recognize Promoters – offer referral programs or exclusive content | Use their stories as testimonials
- Internal Quality Audits
    Monitor how issues were handled (especially among Detractors and Passives) Identify tone gaps, delays, or missed follow-ups

# Recommendations to Improve the Survey & Insights

- Include questions on price perception/value for money.
- Add segment-specific tags (course enrolled, city, time spent on platform) for deeper segmentation.
- Include faculty-specific feedback (for targeted improvement).
- Replace Yes/No-type answers with sentiment scales:
    😠 Very Dissatisfied → 😐 Neutral → 😃 Very Satisfied
    This gives a more nuanced understanding and better charts



















