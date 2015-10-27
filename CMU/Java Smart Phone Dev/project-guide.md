# LGM-Spotagram Memo

+ Team Name: LGM
+ Application Name: Spotagram
+ Description:
	+ Nowadays, social network Apps are so popular all over the world, some of which are particularly known for almost everyone of us, such as Facebook and Twitter. And two of the important reasons for their popularity are: it satisfies people’s curiosity by conveying interesting information and people can communicate and share things with their friends or even make some new friends. Based on these features, we have the framework of our new App. It is can also be defined as a social App, where people can share their words, pictures and even videos. But we try to achieve more than that. We integrate a Map into our App, so that it will locate the user each time he logs on. So the first function of our map is that some information announced by other users nearby will be presented on the map, which the user can browse the message and comment on them. But he can also search for a position he like to receive more information in that position. The second function is that user can share his words, voice or even video at his location or the location he chose. Additionally, information will be classified into different classes based on the tags the announcers chose, such as ‘Transportation News’, ’Restaurant Introduction’ or ’Videos’. Since all the messages will be more location-oriented, our new App can play more roles in our life.
+ Team Member:
	+ Lei Yu(leiyu)
	+ Miaojun Li(miaojunl)
	+ Da Wang(dawang)
+ Inspiration:

## Database Doc V0.1

Basically there will be 3 tables in the database: `User`, `Post` and `Comment`. Here is the detail description of these three tables.

### User Table

Name | Type | Comment
:--: | :--: | :--:
userid | INT | `AUTO_INCREMENT` `PRIMARY_KEY`
username | VARCHAR(30) | no longer than 30 chars
password | VARCHAR(30) | no longer than 30 chars
gender | BIT | male / female
avatar | VARCHAR(128) | url address
info | TEXT | json description content

The content store in the `info` part includes:

+ age: int
+ birthday: date
+ what's up: string
+ region: place

### Post Table

Name | Type | Comment
:--: | :--: | :--:
postid | INT | `AUTO_INCREMENT` `PRIMARY_KEY`
location | VARCHAR(256) | json location information
date | DATE | date of the post
content | VARCHAR(256) | content of the post
type | SMALLINT | different type of post
tag | VARCHAR(256) | json tag list
userid | INT | `FOREIGN_KEY`
info | TEXT | json description content

The content store in the `infor` part includes:

+ image:
+ sound:
+ video:

(this part is for extenstion)

### Comment Table

Name | Type | Comment
:--: | :--: | :--:
commentid | INT | `AUTO_INCREMENT` `PRIMARY_KEY`
date | DATE | date of the post
content | VARCHAR(256) | content of the post
userid | INT | `FOREIGN_KEY` (this is the user who posts the comment)
postid | INT | `FOREIGN_KEY`


