<h1> The problem </h1>

<p>You show up to work Tuesday morning, September 2, 2014. The head of the Product team walks over to your desk and asks you what you think about the latest activity on the user engagement dashboards. You fire them up, and something immediately jumps out:</p>

<img src = "https://github.com/kpourang/Investigating-a-Drop-in-User-Engagement/blob/main/Weekly%20Active%20users.png">

<p>The above chart shows the number of engaged users each week. Yammer defines engagement as having made some type of server call by interacting with the product (shown in the data as events of type "engagement"). Any point in this chart can be interpreted as "the number of users who logged at least one engagement event during the week starting on that date."</p>

<p>You are responsible for determining what caused the dip at the end of the chart shown above and, if appropriate, recommending solutions for the problem.</p>

<h1>Getting oriented</h1>

<p>Before you even touch the data, come up with a list of possible causes for the dip in retention shown in the chart above. Make a list and determine the order in which you will check them. Make sure to note how you will test each hypothesis. Think carefully about the criteria you use to order them and write down the criteria as well.</p>

<p>Also, make sure you understand what the above chart shows and does not show.</p>

<h1>Digging in</h1>

<p>Once you have an ordered list of possible problems, it's time to investigate.</p>

<p>For this problem, you will need to use four tables. The tables names and column definitions are listed below—click a table name to view information about that table. Note: this data is fake and was generated for the purpose of this case study. It is similar in structure to Yammer's actual data, but for privacy and security reasons it is not real.</p>

<h2>Table 1: Users</h2>
<p>This table includes one row per user, with descriptive information about that user's account.</p>
<img src = "https://github.com/kpourang/Investigating-a-Drop-in-User-Engagement/blob/main/Table1.JPG">

<h2>Table 2: Events</h2>
<p>This table includes one row per event, where an event is an action that a user has taken on Yammer. These events include login events, messaging events, search events, events logged as users progress through a signup funnel, events around received emails.</p>
<img src = "https://github.com/kpourang/Investigating-a-Drop-in-User-Engagement/blob/main/Table2.JPG">

<h2>Table 3: Email Events</h2>
<p>This table contains events specific to the sending of emails. It is similar in structure to the events table above. </p>
<img src = "https://github.com/kpourang/Investigating-a-Drop-in-User-Engagement/blob/main/Table3.JPG">

<h2>Table 4: Rollup Periods</h2>
<p>The final table is a lookup table that is used to create rolling time periods. Though you could use the INTERVAL() function, creating rolling time periods is often easiest with a table like this. You won't necessarily need to use this table in queries that you write, but the column descriptions are provided here so that you can understand the query that creates the chart shown above. </p>
<img src = "https://github.com/kpourang/Investigating-a-Drop-in-User-Engagement/blob/main/Table4.JPG">

<h1>Making a recommendation</h1>

<p>Start to work your way through your list of hypotheses in order to determine the source of the drop in engagement. As you explore, make sure to save your work. It may be helpful to start with the code that produces the above query, which you can find by clicking the link in the footer of the chart and navigating to the "query" tab.</p>

<p>Answer the following questions:</p>
<ul>
  <li>Do the answers to any of your original hypotheses lead you to further questions?</li>
  <li>If so, what are they and how will you test them?</li>
<li>If they are questions that you can't answer using data alone, how would you go about answering them (hypothetically, assuming you actually worked at this company)?</li>
  <li>What seems like the most likely cause of the engagement dip?</li>
  <li>What, if anything, should the company do in response?</li>
</ul>


<h1>Solution</h1>
<h1>Preparation and prioritizing</h1>

<p>Making hypotheses and evaluating them is often the most important part of this problem. If you do this well, you can save yourself a lot of time spent digging through data. It's impossible to provide an exhaustive list of possibilities for this kind of problem, but here are some things we came up with in our brainstorming session:</p>

<ul>
  <li><b>Holiday</b>: It's likely that people using a work application like Yammer might engage at a lower rate on holidays. If one country has much lower engagement than others, it's possible that this is the cause.</li>
  <li><b>Broken feature</b>: It is possible that something in the application is broken, and therefore impossible for people to use. This is a little harder to pinpoint because different parts of the application would show differently in the metrics. For example, if something in the signup flow broke, preventing new users from joining Yammer, growth would also be down. If a mobile app was unstable and crashed, engagement would be down for only that device type.</li>
  <li><b>Broken tracking code</b>: It's possible that the code that logs events is, itself, broken. If you see a drop to absolutely zero events of a certain type and you rule out a broken feature, then this is a possibility.</li>
  <li><b>Traffic anomalies from bots</b>: Most major website see a lot of activity from bots. A change in the product or infrastructure that might make it harder for bots to interact with the site could decrease engagement (assuming bots look like real users). This is tricky to determine because you have to identify bot-like behavior through patterns or specific events.</li>
  <li><b>Traffic shutdown to your site</b>: It is possible for internet service providers to block your site. This is pretty rare for professional applications, but nevertheless possible.</li>
  <li><b>Marketing event</b>: A Super Bowl ad, for example, might cause a massive spike in sign-ups for the product. But users who enter through one-time marketing blitzes often retain at lower rates than users who are referred by friends, for example. Because the chart uses a rolling 7-day period, this will register as high engagement for one week, then almost certainly look like a big drop in engagement the following week. Most often, the best way to determine this is to simply ask someone in the Marketing department if anything big happened recently.</li>
  <li><b>Bad data</b>: There are lots of ways to log bad data. For example, most large web apps separate their QA data from production data. One way or another, QA data can make its way into the production database. This is not likely to be the problem in this particular case, as it would likely show up as additional data logged from very few users.</li>
  <li><b>Search crawler changes</b>: For a website that receives a lot of traffic, changes in the way search engines index them could cause big swings in traffic.</li>
</ul>

<p>That's a lot of possibilities, so it's important to move through them in the most efficient order possible. Here are some suggestions for how to sort them so that you don't waste time:</p>

<ul>
  <li><b>Experience</b>: This isn't particularly relevant for those of you who have not worked in industry before, but once you have seen these problems a couple time, you will get a sense for the most frequent problems.</li>
  <li><b>Communication</b>: It's really easy to ask someone about marketing events, so there's very little reason not to do that. Unfortunately, this is also irrelevant for this example, but it's certainly worth mentioning.</li>
  <li><b>Speed</b>: Certain scenarios are easier to test than others, sometimes because the data is cleaner or easier to understand or query, sometimes because you've done something similar in the past. If two possibilities seem equally likely, test the faster one first.</li>
  <li><b>Dependency</b>: If a particular scenario will be easy to understand after testing a different scenario, then test them in the order that makes sense.</li>
</ul>

<h1>Solving the case</h1>

<p>The answers to the first three questions depend heavily on the individual's approach. It would be impossible to list answers for all possible hypotheses, but here's an example of how a solid thought process might look, all the way from the beginning to the solution.</p>

<p>1. One of the easiest things to check is growth, both because it's easy to measure and because most companies (Yammer included) track this closely already. In this case, you have to make it yourself, though. You'll notice that nothing has really changed about the growth rate—it continues to be high during the week, low on weekends:</p>

<pre>
SELECT DATE_TRUNC('day', created_at) AS day,
       COUNT(*) AS all_users,
       COUNT(CASE WHEN activated_at IS NOT NULL THEN u.user_id ELSE NULL END) AS activated_users
FROM tutorial.yammer_users u
WHERE created_at >= '2014-06-01' AND created_at < '2014-09-01'
GROUP BY 1
ORDER BY 1
</pre>

<img src = "https://github.com/kpourang/Investigating-a-Drop-in-User-Engagement/blob/main/Daily%20Signups.png">

<p>2. Since growth is normal, it's possible that the dip in engagement is coming from existing users as opposed to new ones. One of the most effective ways to look at this is to cohort users based on when they signed up for the product. This chart shows a decrease in engagement among users who signed up more than 10 weeks prior:</p>

<pre>
SELECT DATE_TRUNC('week', z.occurred_at) AS "week",
       AVG(z.age_at_event) AS "Average age during week",
       COUNT(DISTINCT CASE WHEN z.user_age > 70 THEN z.user_id ELSE NULL END) AS "10+ weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 70 AND z.user_age >= 63 THEN z.user_id ELSE NULL END) AS "9 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 63 AND z.user_age >= 56 THEN z.user_id ELSE NULL END) AS "8 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 56 AND z.user_age >= 49 THEN z.user_id ELSE NULL END) AS "7 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 49 AND z.user_age >= 42 THEN z.user_id ELSE NULL END) AS "6 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 42 AND z.user_age >= 35 THEN z.user_id ELSE NULL END) AS "5 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 35 AND z.user_age >= 28 THEN z.user_id ELSE NULL END) AS "4 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 28 AND z.user_age >= 21 THEN z.user_id ELSE NULL END) AS "3 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 21 AND z.user_age >= 14 THEN z.user_id ELSE NULL END) AS "2 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 14 AND z.user_age >= 7 THEN z.user_id ELSE NULL END) AS "1 weeks",
       COUNT(DISTINCT CASE WHEN z.user_age < 7 THEN z.user_id ELSE NULL END) AS "Less than a week"
FROM (
      SELECT e.occurred_at,
             u.user_id,
             DATE_TRUNC('week', u.activated_at) AS activation_week,
             EXTRACT('day' FROM e.occurred_at - u.activated_at) AS age_at_event,
             EXTRACT('day' FROM '2014-09-01'::TIMESTAMP - u.activated_at) AS user_age
      FROM tutorial.yammer_users u
      JOIN tutorial.yammer_events e
        ON e.user_id = u.user_id
        AND e.event_type = 'engagement'
        AND e.event_name = 'login'
        AND e.occurred_at >= '2014-05-01'
        AND e.occurred_at < '2014-09-01'
        WHERE u.activated_at IS NOT NULL
        ) z
GROUP BY 1
ORDER BY 1
LIMIT 100
</pre>

<img src = "https://github.com/kpourang/Investigating-a-Drop-in-User-Engagement/blob/main/Engagement%20by%20User%20Age%20Cohort.png">

<p>3. The understanding that the problem is localized to older users leads us to believe that the problem probably isn't related to a one-time spike from marketing traffic or something that is affecting new traffic to the site like being blocked or changing rank on search engines. Now let's take a look at various device types to see if the problem is localized to any particular product:</p>

<pre>
SELECT DATE_TRUNC('week', occurred_at) AS week,
       COUNT(DISTINCT e.user_id) AS weekly_active_users,
       COUNT(DISTINCT CASE WHEN e.device IN ('macbook pro','lenovo thinkpad','macbook air','dell inspiron notebook',
          'asus chromebook','dell inspiron desktop','acer aspire notebook','hp pavilion desktop','acer aspire desktop','mac mini')
          THEN e.user_id ELSE NULL END) AS computer,
        COUNT(DISTINCT CASE WHEN e.device IN ('iphone 5','samsung galaxy s4','nexus 5','iphone 5s','iphone 4s','nokia lumia 635',
       'htc one','samsung galaxy note','amazon fire phone') THEN e.user_id ELSE NULL END) AS phone,
      COUNT(DISTINCT CASE WHEN e.device IN ('ipad air','nexus 7','ipad mini','nexus 10','kindle fire','windows surface',
        'samsumg galaxy tablet') THEN e.user_id ELSE NULL END) AS tablet 
FROM tutorial.yammer_events e
WHERE e.event_type = 'engagement' AND e.event_name = 'login'
GROUP BY 1
ORDER BY 1
LIMIT 100
</pre>

<img src = "https://github.com/kpourang/Investigating-a-Drop-in-User-Engagement/blob/main/Weekly%20Engagament%20by%20Device%20Category.png">


<p>4. If you filter the above chart down to phones (double-click the dot next to "phone" in the legend), you will see that there's a pretty steep drop in phone engagement rates. So it's likely that there's a problem with the mobile app related to long-time user retention. At this point, you're in a good position to ask around and see if anything changed recently with the mobile app to try to figure out the problem. You might also think about what causes people to engage with the product. The purpose of the digest email mentioned above is to bring users back into the product. Since we know this problem relates to the retention of long-time users, it's worth checking out whether the email has something to do with it:</p>

<pre>
SELECT DATE_TRUNC('week', occurred_at) AS week,
       COUNT(CASE WHEN e.action = 'sent_weekly_digest' THEN e.user_id ELSE NULL END) AS weekly_emails,
       COUNT(CASE WHEN e.action = 'sent_reengagement_email' THEN e.user_id ELSE NULL END) AS reengagement_emails,
       COUNT(CASE WHEN e.action = 'email_open' THEN e.user_id ELSE NULL END) AS email_opens,
       COUNT(CASE WHEN e.action = 'email_clickthrough' THEN e.user_id ELSE NULL END) AS email_clickthroughs
  FROM tutorial.yammer_emails e
 GROUP BY 1
 ORDER BY 1
 </pre>
 
 <img src = "https://github.com/kpourang/Investigating-a-Drop-in-User-Engagement/blob/main/Email%20Actions.png">
 
<p>5. If you filter to clickthroughs (again, by clicking the dot in the legend), you'll see that clickthroughs are way down. This next chart shows in greater detail clickthrough and open rates of emails, indicating clearly that the problem has to do with digest emails in addition to mobile apps.</p>

<pre>
SELECT week,
       weekly_opens/CASE WHEN weekly_emails = 0 THEN 1 ELSE weekly_emails END::FLOAT AS weekly_open_rate,
       weekly_ctr/CASE WHEN weekly_opens = 0 THEN 1 ELSE weekly_opens END::FLOAT AS weekly_ctr,
       retain_opens/CASE WHEN retain_emails = 0 THEN 1 ELSE retain_emails END::FLOAT AS retain_open_rate,
       retain_ctr/CASE WHEN retain_opens = 0 THEN 1 ELSE retain_opens END::FLOAT AS retain_ctr
  FROM (
SELECT DATE_TRUNC('week',e1.occurred_at) AS week,
       COUNT(CASE WHEN e1.action = 'sent_weekly_digest' THEN e1.user_id ELSE NULL END) AS weekly_emails,
       COUNT(CASE WHEN e1.action = 'sent_weekly_digest' THEN e2.user_id ELSE NULL END) AS weekly_opens,
       COUNT(CASE WHEN e1.action = 'sent_weekly_digest' THEN e3.user_id ELSE NULL END) AS weekly_ctr,
       COUNT(CASE WHEN e1.action = 'sent_reengagement_email' THEN e1.user_id ELSE NULL END) AS retain_emails,
       COUNT(CASE WHEN e1.action = 'sent_reengagement_email' THEN e2.user_id ELSE NULL END) AS retain_opens,
       COUNT(CASE WHEN e1.action = 'sent_reengagement_email' THEN e3.user_id ELSE NULL END) AS retain_ctr
  FROM tutorial.yammer_emails e1
  LEFT JOIN tutorial.yammer_emails e2
    ON e2.occurred_at >= e1.occurred_at
   AND e2.occurred_at < e1.occurred_at + INTERVAL '5 MINUTE'
   AND e2.user_id = e1.user_id
   AND e2.action = 'email_open'
  LEFT JOIN tutorial.yammer_emails e3
    ON e3.occurred_at >= e2.occurred_at
   AND e3.occurred_at < e2.occurred_at + INTERVAL '5 MINUTE'
   AND e3.user_id = e2.user_id
   AND e3.action = 'email_clickthrough'
 WHERE e1.occurred_at >= '2014-06-01'
   AND e1.occurred_at < '2014-09-01'
   AND e1.action IN ('sent_weekly_digest','sent_reengagement_email')
 GROUP BY 1
       ) a
 ORDER BY 1
 </pre>
 
 <img src = "https://github.com/kpourang/Investigating-a-Drop-in-User-Engagement/blob/main/Open%20and%20CT%20Rates.png">
 
 <h1>Follow through</h1>

<p>After investigation, it appears that the problem has to do with mobile use and digest emails. The intended action here should be clear: notify the head of product (who approached you in the first place) that the problem is localized in these areas and that it's worth checking to make sure something isn't broken or poorly implemented. It's not clear from the data exactly what the problem is or how it should be solved, but the above work can save other teams a lot of time in figuring out where to look.</p>
