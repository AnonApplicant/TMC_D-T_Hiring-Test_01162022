# TMC D&T Hiring Test
Completed Jan 25, 2022

[View on GitHub](https://github.com/AnonApplicant/TMC_D-T_Hiring-Test_01162022/edit/main/README.md)

---

## Task 1: SQL

### Tools Used
- Postgres
- pgAdmin


#### Q1. What are the last five locations Clinton visited during the previous campaign?

`SELECT location, date 
FROM tmc.clinton 
ORDER BY date DESC 
LIMIT 5;`

<img width="255" alt="tmc_q1_query" src="https://user-images.githubusercontent.com/55712982/151003111-bc937a23-7b8b-4672-b78e-995a3562e02d.png">

<img width="238" alt="tmc_q1_result" src="https://user-images.githubusercontent.com/55712982/151003654-3710e7a2-1950-4586-9b5e-1796fbc87ef3.png">

Ans. New York City, NY; Philadelphia, PA; Raleigh, NC; Oakland, PA; Allendale, MI

#### Q2: Which locations did Clinton visit the most during the campaign?

`SELECT 
	count(location) AS number_of_visits,
	location
FROM analysis.clinton_campaign_stops  
GROUP BY 2
ORDER BY 1 DESC;`

<img width="398" alt="tmc_q2_query" src="https://user-images.githubusercontent.com/55712982/151004433-9997be9b-9292-4b21-8e26-b7ac1f599102.png">

<img width="298" alt="tmc_q2_result" src="https://user-images.githubusercontent.com/55712982/151004442-110d267e-53a3-4694-9475-0a0bda0cd2e1.png">

Ans. New York City, NY with 7 visits and Charlotte, NC with four visits

#### Q3: How many events did Clinton do in October? How many distinct locations?

`SELECT 
    count(*) AS total_events,
    count(distinct location) AS distinct_locations
FROM analysis.tmc_clinton  
WHERE (date >= '20161001' AND date <= '20161031');`

<img width="484" alt="Screen Shot 2022-01-25 at 9 04 29 AM" src="https://user-images.githubusercontent.com/55712982/151004748-211ebc3c-6b11-4158-b55a-2bf7f5eabae0.png">

<img width="288" alt="tmc_q3_result" src="https://user-images.githubusercontent.com/55712982/151004769-07ff11ed-31fc-4221-a062-185353e52845.png">

Ans. 32 total events and 29 distinct locations

#### Q4: How many states did Clinton visit: swing states vs. red states vs. blue states (define as you wish)?

`SELECT 
    CASE 
        WHEN state in ('CA', 'IL', 'NY', 'CO') THEN 'blue state'
        WHEN state in ('PA', 'NH', 'NV', 'OH', 'MI', 'IA', 'FL', 'NC', 'AZ') THEN 'swing state'
        ELSE 'red state'
    END AS partisan_status,
    count(*) AS clinton_visits
FROM analysis.tmc_clinton
GROUP by 1
ORDER BY 2 DESC;`

<img width="867" alt="Screen Shot 2022-01-25 at 9 05 16 AM" src="https://user-images.githubusercontent.com/55712982/151004980-f57e1c57-a277-4970-af04-5979ad19057c.png">

<img width="279" alt="tmc_q4_result" src="https://user-images.githubusercontent.com/55712982/151005000-981c70f1-5d33-485f-b95a-2971ff9e5b1b.png">

Ans. 53 visits to a swing state, 2 visits to a red state, and 12 visits to a blue state

#### Q5: What locations had the most combined visits between Trump and Clinton? (Use a join, if you can!)

`with cte_trump AS
(
	SELECT location, lat, lng, COUNT(1) total_count
	FROM tmc.trump
	GROUP BY location, lat, lng
	ORDER BY total_count DESC
),
cte_clinton AS
(
	SELECT location, lat, lng, COUNT(1) total_count
	FROM tmc.clinton
	GROUP BY location, lat, lng
	ORDER BY total_count DESC
)
SELECT t.location, c.total_count + t.total_count AS total_count
FROM cte_trump t 
JOIN cte_clinton c on t.lat = c.lat and t.lng = c.lng
ORDER BY total_count DESC;`

<img width="610" alt="Screen Shot 2022-01-25 at 1 53 24 PM" src="https://user-images.githubusercontent.com/55712982/151049522-39d18678-4374-4f5c-925d-5c576a9e610c.png">

<img width="287" alt="Screen Shot 2022-01-25 at 1 53 43 PM" src="https://user-images.githubusercontent.com/55712982/151049508-c14960c9-d6ff-44c5-9d49-e77945347575.png">

Ans. New York, NY with 11 visits.

#### Q6: CHALLENGE: Use a window function to show the running count of events in a state by date.

`SELECT state, date,
       COUNT(state) OVER
         (PARTITION BY state ORDER BY date ASC) AS running_count
  FROM analysis.tmc_clinton;`

<img width="1017" alt="Screen Shot 2022-01-25 at 5 48 23 PM" src="https://user-images.githubusercontent.com/55712982/151078991-96e948c5-896e-4f56-9135-ac70a273e5fd.png">

<img width="456" alt="Screen Shot 2022-01-25 at 5 48 49 PM" src="https://user-images.githubusercontent.com/55712982/151078962-a7fff62a-6c84-4df3-b598-8f67f097fa7f.png">

##### To Fix Errors within Dataset

`UPDATE analysis.task_one_trump 
SET state = 'WA'
WHERE state = 'Washington';
UPDATE analysis.task_one_trump 
SET state = 'DC'
WHERE location LIKE 'Washington DC,%';
UPDATE analysis.task_one_trump 
SET location = 'Prescott Valley, AZ',
	state = 'AZ',
	city = 'Prescott Valley';`

<img width="385" alt="Screen Shot 2022-01-26 at 12 19 10 PM" src="https://user-images.githubusercontent.com/88506979/151222960-9f2ae256-f301-417c-a4e0-baa9bf855ae6.png">

---

## Task 2: Explanatory Email

#### Prompt: A new member does not understand why syncing the data from their various tools (i.e., CRM, Email Tool, Peer to Peer Texting) to a cloud-based database (we use Amazon Redshift) is beneficial. Explain some of the possibilities that come with syncing data centrally to a database.

Hi [NAME], thank you for your inquiry. I???m glad you asked! 

Syncing your organization???s various tools to a cloud database is a TMC recommended best practice. ???Syncing data??? refers to sharing information and in this case, automatically cross data sources. Benefits include better-informed decision-making and a more efficient allocation of resources.

Having an up-to-date centralized source of truth for data helps your organization work more efficiently saving time and money. Each tool your organization uses (i.e. MiniVAN, Email Tool, Peer to Peer Texting) contains slightly different, uniquely informative data that when centralized, can better inform decision making and the allocation of resources. 

For example, might you be confirming RSVPs for an event through multiple tactics, including text, email, and phone call, and receive a confirmation via text, eliminating that person from email and call audiences maximizes efficiency. Further, generally, contact tools charge by contact attempt such as a text or dial.

In an alternative instance, might your organization want to send a donation solicitation to subscribers who haven???t donated recently, having a centralized database with information from your email CRM and payment processing system will enable you to exclude people who have donated within the past two weeks.

In summary, syncing your organization???s tools to a cloud-based database helps you save time and money.

I???m happy to schedule some time with you to discuss the next steps towards [ORGANIZATION NAME] adopting this best practice.

Please let me know your interest and availability.


Thank you,

[MY NAME]
