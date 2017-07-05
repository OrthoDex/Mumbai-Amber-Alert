## Inspiration
 
In India, a person goes missing every eight minutes, according to data from the National Crime Records Bureau. The statistics revealed that 2,667 people went missing (till March this year) in Mumbai alone, of which 1,675 have been found. 
 
The police in India, barring a few states, do not register first information reports – the first step to an investigation — for missing people. So no formal case is even filed. They only make an entry into the list of missing persons at the police station so in-depth investigations are rare. There is no investigation of a crime unless the person who reports someone missing asks the police to file a case of kidnapping. 
 
Also if a person is reported lost in one state but has been trafficked to another state, there is no mechanism to ensure that the person will be searched for countrywide. Clearly, there is a “lack of inter-state cooperation” and a need for an “integrated country-wide database” for missing people. 
 
Amber Alert Mumbai equips its users to take first-hand action by sending an alert for the person missing via Facebook messenger, providing a greater exposure and thereby increasing the chances of locating their dear ones.
 
 
## What it does
People who have subscribed to the Amber Alert Page on Facebook can easily report missing or found people using the page’s messenger bot. The bot offers every user two options of either report a missing person or report that they may have found one.
 
After a missing person is reported, every subscriber receives a broadcast about the person's details and the emergency contact numbers of the Police Helpline.
 
## How we built it
 
### Chatbot
 
We built the chatbot in Flask and used the Facebook Messenger API to send and receive messages. The chatbot uses common Messenger Send APIs like:
1. URL Button for linking to an FB Post or Missing Person Detail.
2. Call Button for calling the Police Helpline in case a missing person is found.
3. Get Started Button for onboarding users and subscribing them to Missing Person Alerts.
4. Quick Replies for continuously providing the user a clear direction/flow while interacting with the bot.
5. Webview: The webview is primarily used for filling a form to register a missing person. It's also used for previewing FB Posts and details of the missing person. The webview also auto-closes after filling the form using Messenger Extensions.
 
### Web App
The backbone of the whole system is the web app built in Ruby on Rails deployed on Heroku. The app stores Missing person information and uses Messenger Extensions to decide between closing a webview (in case of Messenger views) or showing "I found this person" buttons. The Messenger Extensions are also used to capture the user IDs which are later used to send replies back to the chatbot user through the Rails app directly.
 
The Rails app also asynchronously publishes an FB Post about the missing person, broadcasts this information as an alert to all subscribers and links to the post in that broadcast, all using the Graph API. The asynchronous processing is executed using Sidekiq workers. The subscriber list itself is stored in a Redis Datastore. A user is subscribed to alerts when the click the "Get Started" button.
 
The Rails app frontend uses Bootstrap to adjust between varying screen sizes. It was especially important to make the UI readable and clear since a majority of the views would occur through the Messenger Webview. 
 
We whitelisted the image CDN domain, the chatbot domain, and the web app domain to use Messenger Extensions and send images to Messenger.
 
## Challenges we ran into
What made making this project fun was overcoming these challenges and learning tons through them. :smile:
1. The most challenging part was deciding how to send the missing person alerts as a broadcast. We initially thought of somehow finding some way to retrieve all the users who 'liked' or 'followed' the page. Unfortunately, we didn't find any endpoint that did so. We even thought about scraping the Facebook Search results! (Simple thing like scraping the results of facebook.com/page_id/likers)
We finally used Redis to store the FB IDs of the users who clicked the "Get Started" button. The idea came after reading ways that businesses could use chatbots to market their products.
 
2. The permission of the Access Tokens had us confused initially. We allotted the Page post permission through the Graph API explorer. Suffice to say, the API explorer was constantly being used to find out what we could do with the Graph API.
 
3. User Interface: Providing a clean, minimal interface that was optimized for the Webview was extremely important and a large part of our time went in testing the UI on different screen sizes. Chrome Developer Tools helped *immensely* here.
 
4. Facebook Messenger Extensions: The Javascript SDK was amazingly useful, but we had to first verify that legitimacy of the user ID. We settled on querying the Graph API to see if the ID was not spoofed.
 
## Accomplishments that we're proud of
 
We solved a major issue that has not been looked into yet! The opportunity to use our skills as software engineers to do some social good is immensely satisfying.
 
On the technical side, we are particularly proud of building a whole Service Oriented Architecture that worked seamlessly together.
 
We are also proud of the Hack we used to send broadcasts to users by storing their IDs. :wink:
 
 
## What we learned
 
Facebook Messenger APIs, Graph APIs, SOA and how Python and Ruby can indeed work together! :wink: :tada:
 
## What's next for Mumbai-Amber-Alert
 
The app right now is hosted on Heroku, we may think of switching to AWS because Lambda functions would scale well.
 
Moreover, currently the app is susceptible to spam, so there's a need for content moderation. We aim to use SMS verifications, Captchas time limited missing alerts.
Currently, the FIR details are optional, but to make it safer and legitimate, we aim to put a time limit of 24 hrs on reports that do not have FIR details submitted.
 
In the future, we will be looking at Image Recognition using Machine Learning for mapping images in the database with camera photos taken by observers who think they may have found a missing person.
The camera photo can be analyzed to give a certain level of confidence that the chatbot user has found a missing person.
