# heroin
Addictive recommendation algorithm for UGC

the following list a general approach to recommending user generated content to your userbase, you can also merge this algorithm to make it more refine:
This list is actively being modified and corrected
The below are a puesoduo variables used in the algorithms

- SAMPLER_NUM: an integer that represent the initial numbers of users to recommend a sample content to. Defaults to 7.
- TARGET_LIST: an array that contains a list of final user to recommend content to
- THIS_USER: a string that represent uid of the user that posted the content
- ACTIVE_ENGAGERS: users that subscribes but has above 30% of engagement to UGC of a particular user
- DEAD_ENGAGERS: users that subscribes but has below 30% of engagement to UGC of a particular user
- RANDOM_CHANCE: a function that returns a random string which may be any of 'CHANCE_YES' | 'CHANCE_OK' | 'CHANCE_AVERAGE' | 'CHANCE_LUCKY' | 'NOT_A_CHANCE'. where this function has an higher probability of returning 'CHANCE_YES' to 'NOT_A_CHANCE'.
- ENGAGE_THRESHOLD: this is the thresold a user engagement must reach in other to consider it as an interesting content

Terms used in some of the algorithms
- ERC: this is also known as exponential recommendable content. This is added to a recommended content to ensure the content with enagement above a certain threshold is exponentially distributed across user that may be potentially interested in it. For instance, if we initially recommend an ERC video content of 60 seconds to 10 users. Then 3 of those 10 users watch duration greater than 40 seconds of the video. For every user that pass the enagement threshold, we exponentially recommend the content to other user fetched via the MBI method, then we repeat until it dies off. The exponent can be increased by either multiplication, addition or however you choose to make the content go viral.
Note: all user that are suggested an ERC content must have been active within a certain period (less than a week or month)
- SC: this is also known as safe criteria. This is the measure of content nudity, profanity or any graphics that not suitable for young minds. the value maybe any of 'abnormal' | 'neutral' | 'normal'.
- RC: also known as relevant content. measure in realtime by how most user interacts with the contents.
- TC: also known as trending content.
- SP: also known as stage profile. This is a flag 
- CE: this means `content engagement`. it must be a fix range value, usually 0 - 1 or 0 - 100

# Stepping Stone
this algorithm is basically useful for gaining out-reach and followers for a user profile that has an adequate enagement on the content they posted.
## Approach
when a user posted a content, we do the followings:
- Fetch users for this content using the MBI method
- Filter out subscribed users and limit to SAMPLER_NUM
- Flag the result as an ERC.

# Random Selection
this is recommending content randomly to user that are not subscribed and have not engaged with the content

# Local Random Selection
this is recommending content randomly to user nearby that are not subscribed and have not engaged with the content

# User Content Staging (UCS)
if a particular user has pass the ENGAGE_THRESHOLD for more than 7 (or any threshold you choose to set) contents from a content creator, then we proceed to stage the entire contents from the content creator for this user to engage in by cursoring from lastest to the earliest bit-by-bit and side-by-side.

# Phone Contact Link
this type of recommendation is based on user contact. It basically involves recommending content to users that knows the user that created the content.

# MBI method (may be interested)
this method helps in determining who is likely to be interested in a content. Whenever a content is posted, we utilize A.I to know what was posted by the user. Depending on the content type. 
if it is a video/audio, we analyse the audio to know what is spoken.
if it is a video/image, we analyse the graphics to know what is visible.
if it is a pdf or any other encoded doc, we decode the buffer to know what was written.

All this metadata and info gotten are then classify into several fixed and dynamic topics. This topics are linked to the content itself and the user that posted it and then stored in a database. The interest a user has on a topic is calculated base on "engagement/views_in_topic".
We later make a query based on either the user or content to figure out which users maybe interested in a topics or to get similar content from a topic.

# User Language Filtering
we can determine if a user understand a language by "engagement/content_views_in_a_language" which can help us filter out language the doesn't understand

# PG Rating Filtering
we can determine PG rating a user prefers by "engagement/content_views_in_a_specific_pg" which can help us filter out PG rating that is not suitable for the user

# Combined Algorithms
this algorithm are refined combinations of the above algorithms
## Approach

### New Content
when a user newly posted a content, do the followings:
- Fetch subscribers/followers of THIS_USER, filter in ACTIVE_ENGAGERS || RANDOM_CHANCE() === 'NOT_A_CHANCE'. Add a relevant flag. Append the result to TARGET_LIST.
- Fetch users for this content using the MBI method then filter out subscribed users and limit to SAMPLER_NUM. Flag the result as an ERC. Append the result to TARGET_LIST.
- if content passes SC then fetch 10 random users 7km aways that are not subscribe to THIS_USER. Append the result to TARGET_LIST.
- if content passes SC then fetch 10 random users that are not subscribe to THIS_USER. Append the result to TARGET_LIST.
- Fetch all users that knows THIS_USER via phone contact. Append the result to TARGET_LIST.
- Filter out duplicate
- Filter out user that don't want to see this user again
return final data and store it;

### New User
when a user newly created an account, such user will not initially have data in their recommendation listing. we know nothing about this user, we will have to suggest the user with high quality and relevant content. this is done by a staging their engagement so as to deep dive into the ocean of what they love to see and watch,
do the followings:
for determining user interest
- Fetch 7 RC each from the top 30 content creators in the new user locality then append to result_list
- Fetch 7 RC each from the top 30 content creators in the world then append to result_list
- Fetch 7 RC each from 10 random content creators in the new user locality then append to result_list
- Fetch 7 RC each from 10 random content creators in the world then append to result_list

for determining user fan
- Fetch 7 RC each from the top 30 topics in the new user locality then append to result_list
- Fetch 7 RC each from the top 30 topics in the world then append to result_list
- Fetch 7 RC each from 10 random topics in the new user locality then append to result_list
- Fetch 7 RC each from 10 random topics in the world then append to result_list

for more user enjoyment
- Fetch 9 random TC from last 6 months.
- Fetch 7 nearby random TC from last 6 months. 

for language test case
- Fetch 7 content each for every language in the user locality to test.

if the new user also likes the UGC suggested by the topics, then we take note that the user likes this topics to continue recommending this topic.

# Other Events

## User Engagement
This event should be triggered for every user engagements
- update the profile engagement for engaging user by "EC/views_on_specific_user"
- update the language ratio for engaging user by "EC/views_on_specific_language"
- update PG rating for engaging user by "EC/views_on_specific_pg_rating"
- update fixed topic for engaging user by "EC/views_on_specific_fixed_topic"
- update dynamic topic for engaging user by "EC/views_on_specific_dynamic_topic"

## Adding More Recommendations
<!-- TODO: -->

## UGC indexing
when a user create a new content, we index the following to make it searchable:
- the text
- the audio/video transcript
- the graphics displayed on each frame of a video
- the graphics displayed on an image

## Trending Content Index
a content will be flagged as trending when it passes a certain views/(current_time - creation_time)

## Recommendation scarcity
This event should be triggered for the recommendation listing of a user
Recommendation scarcity might occur when there is not enough content been posted that matches the user interest on the platform or the algorithm doesn't performs well for a specific user. In cases like this, you may want to sacrifice user interest with other contents user has not seen yet.

# Data Structure
In general cases, your database structure will appear like this:

users_recommendation -> `{ uid, cursor, date, seen }`
                       |-> fixed -> `{ topic, uid, updatedOn, rating }`
users_topics_rating -> |
                       |-> dynamic -> `{ topic, uid, updatedOn, rating }`
users_language_rating -> `{ uid, language, rating, updatedOn }`
users_pg_rating -> `{ uid, language, rating, updatedOn }`
users_profile_rating -> `{ uid, profile, rating, updatedOn }`