
# Traffic Trend Analysis

## Analyzing Traffic Sources
Traffic source analysis is about understanding **where customers are coming from** and **which channels are driving the highest quality traffic.**

### **Applications of Traffic Source Analysis**
- Analyzing search data and shifting budget towards the engines, campaigns or keywords driving the strongest conversion rates
- Comparing user behavior patterns across traffic sources to inform creative and messaging strategy
- Identifying opportunities to eliminate wasted spend or scale high-converting traffic
### **Task**
### **1. Finding Top Traffic Sources **
**What is the TOP traffic source before 4 April 2012?*


**Query :**
```sql
SELECT 
	utm_source,
	utm_campaign,
	http_referer,
	COUNT(DISTINCT website_session_id) AS sessions
FROM website_sessions
WHERE created_at < '2012-04-12'
GROUP BY 1, 2, 3
ORDER BY 4 DESC
```

**Result :**
"https://user-images.githubusercontent.com/115857221/216097751-9cf81722-0923-48ce-87cb-67c4c43e1a15.png"

**INSIGHTS: The above result shows that gsearch nonbrand campaign received highest traffic*

  ### **2. Traffic Conversion Rate**
  **2) What is the conversion rate from order to session? IF present cvr of the company is 4%,what insights can be drawn based on analysis?*


**Query :**
```sql
SELECT 
COUNT(DISTINCT website_sessions.website_session_id) AS number_of_sessions,
COUNT(DISTINCT orders.website_session_id) AS number_of_orders,
COUNT(DISTINCT orders.website_session_id)/COUNT(DISTINCT website_sessions.website_session_id) AS conversion_rate

FROM website_sessions
     LEFT JOIN orders
         ON website_sessions.website_session_id = orders.website_session_id
WHERE website_sessions.created_at < '2012-04-12'
   AND utm_source = 'gsearch'
   AND utm_campaign = 'nonbrand'
```

**Result :**
"https://user-images.githubusercontent.com/115857221/216098251-06f32653-8c9c-478a-83f5-0d286efbdb70.png" 

**INSIGHTS: the above result shows that cvr is 2.9% which means company is overbidding.*

### **3. Traffic Source Trending**
**What is the weekly traffic source trend?*


**Query :**
```sql
SELECT
	MIN(DATE(created_at)) AS week_start_date,
	COUNT(DISTINCT website_session_id) AS sessions
FROM website_sessions 
WHERE created_at < '2012-05-10'
	AND utm_source = 'gsearch'
	AND utm_campaign = 'nonbrand'
GROUP BY YEAR(created_at), WEEK(created_at)
```

**Result :**
"https://user-images.githubusercontent.com/115857221/216099721-18749403-05ba-4a2c-b562-ed10076282e1.png"

### **4. Traffic Source Bid Optimization**
**What is the conversion rate from session to order,by device type?*


**Query :**
```sql
SELECT
	w.device_type,
	COUNT(DISTINCT w.website_session_id) AS sessions,
	COUNT(DISTINCT o.order_id) AS orders,
	COUNT(DISTINCT o.order_id)/COUNT(DISTINCT w.website_session_id) AS session_to_order_CVR
FROM website_sessions w
	LEFT JOIN orders o
		ON o.website_session_id = w.website_session_id
WHERE w.created_at < '2012-05-11'
	AND w.utm_source = 'gsearch'
	AND w.utm_campaign = 'nonbrand'
GROUP BY 1
ORDER BY 4 DESC
```

**Result :**
"https://user-images.githubusercontent.com/115857221/216100125-db73b60e-23b2-4491-9233-a72886b31827.png"

**INSIGHTS: The above results shows that desktop has high session volume than mobile*

### **5. Traffic Source Segment Trending**
**To analyse the impact on volume,Pull out the weekly trends for both mobile and desktop?*

**Query :**
```sql
SELECT
	MIN(DATE(created_at)) AS week_start_date,
	COUNT(DISTINCT CASE WHEN device_type = 'desktop' THEN website_session_id ELSE NULL END) AS dtop_session,
	COUNT(DISTINCT CASE WHEN device_type = 'mobile' THEN website_session_id ELSE NULL END) AS mob_session
FROM website_sessions
WHERE created_at < '2012-06-09'
	AND created_at > '2012-04-15'
	AND utm_source = 'gsearch'
	AND utm_campaign = 'nonbrand'
GROUP BY YEAR(created_at), WEEK(created_at)
```

**Result :**
"https://user-images.githubusercontent.com/115857221/216100559-cb7142a5-8022-4ad5-b183-ab005bc5212d.png"




  
