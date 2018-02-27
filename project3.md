Project 3 - Service-Oriented Ticket Purchase Application
========================================================
*This project specification is incomplete and will change! Proceed at your own risk.*

### Code Due - TBD
### Deployment Due - TBD

For this project you will implement a service-oriented version of a ticket purchase service (i.e., your own EventBrite!). 

### Overview

The architecture of the web application will be as follows:

![architecture](https://docs.google.com/drawings/d/e/2PACX-1vTjBg_ZETz31hzGUrNL6Fh6GoSEUA9iWLSwyLnPdY0Ixg0YuHhVliwo4fJvfUhFp8mXIxz1dOHMZHw1/pub?w=960&h=720)

**Web Front End** - The web front end will implement an external web service API for the application and will support APIs for the following operations:
1. Get all events
2. Create a new event
3. Get details about a specific event
4. Purchase tickets for an event
5. Create a user
6. See a user's information, including *details* of all events for which the user has purchased tickets
7. Transfer tickets from one user to another

**Event Service** - The event service will manage the list of events and the number of tickets sold and available for each. When a ticket is purchased it is the responsibility of the Event Service to notify the User Service of the user's purchase.

**User Service** - The user service will manage the user account information, including the events for which a user has purchased tickets. 

### Logistics (read carefully!)

Every student must implement at least one service **individually**, however students may choose to work in teams on the portions described below.

A student who works individually must implement the Event Service and the Web Front End. We will provide you with a User Service implementation that you may test against. Your grade will be based only on the implementation of the Event Service and the Web Front End.

Students who choose to work in teams *may* work together on the implementation of the Web Front End. However, each student will be responsible for their own *individual* implementation of one service. You may not work as a team on the actual implementation of your service. If Student A implements the Event Service then Student B must implement the User Service. In this case, Student A's grade will be based on the Event Service and the Web Front End, and Student B's grade will be based on the User Service and the Web Front End.

### API

#### Front End Service

<details>
<summary>GET /events</summary>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Event Details<br/>
<pre>
[
	{
		"eventid": 0, 
		"eventname": "string", 
		"avail": 0, 
		"purchased": 0
	} 
]
	</pre></td></tr>
	<tr><td>400</td><td>No events found</td></tr>
</table>
</details>


<details>
<summary>POST /events/create </summary>
	
Body:

<pre>
{
	"userid": 0,
	"eventname": "string",
	"numtickets": 0
}
</pre>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Event created</tr>
	<tr><td>400</td><td>Event unsuccessfully created</td></tr>
</table>
</details>

<details>
<summary>GET /events/{eventid}</summary>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Event Details<br/>
<pre>
{
	"eventid": 0, 
	"eventname": "string", 
	"avail": 0, 
	"purchased": 0
}
</pre></td></tr>
	<tr><td>400</td><td>Event not found</td></tr>
</table>
</details>

<details>
<summary>GET /events/{eventid}/purchase/{userid}</summary>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Tickets purchased</td></tr>
	<tr><td>400</td><td>Tickets could not be purchased</td></tr>
</table>
</details>

<details>
<summary>POST /users/create</summary>

Body:

<pre>
{
	"username": "string",
}
</pre>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>User created<br/>
<pre>
{
	"userid": 0,
}	
</pre></td></tr>
	<tr><td>400</td><td>User could not be created</td></tr>
</table>
</details>

<details>
<summary>GET /users/{userid}</summary>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>User Details<br/>
<pre>
{
	"userid": 0,
	"username": "string",
	"tickets": [
		{
			"eventid": 0,
			"eventname": "string",
			"avail": 0,
			"purchased": 0
		}
	]	
}
</pre></td></tr>
	<tr><td>400</td><td>User not found</td></tr>
</table>
</details>

<details>
<summary>POST /users/{userid}/tickets/transfer</summary>

Body:
<pre>
{
	"eventid": "string",
	"tickets": 0,
	"targetuser": 0
}
</pre>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Event tickets transferred</td></tr>
	<tr><td>400</td><td>Tickets could not be transferred</td></tr>
</table>

</details>


#### Event Service

<details>
<summary>POST /create</summary>

Body:

<pre>
{
	"userid": 0,
	"eventname": "string",
	"numtickets": 0
}
</pre>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Event created</td></tr>
	<tr><td>400</td><td>Event unsuccessfully created</td></tr>

</table>
</details>

<details>
<summary>GET /list</summary>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>List of events <br/>
<pre>
[
	{
		"eventid": 0,
		"name": "string",
		"avail": 0,
		"purchased": 0
	}
]	
</pre>
	</td></tr>
</table>
</details>

<details>
<summary>GET /{eventid}</summary>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Event details<br/>
<pre>
{
	"eventid": 0,
	"name": "string",
	"avail": 0,
	"purchased": 0
}
</pre>
	</tr>
	<tr><td>400</td><td>Event not found</tr>
</table>
</details>

<details>
<summary>POST /purchase/{eventid}</summary>

Body:

<pre>
{
	"userid": 0,
	"eventid": "string",
	"tickets": 0
}
</pre>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Event tickets purchased</tr>
	<tr><td>400</td><td>Tickets could not be purchased</tr>
</table>

</details>


#### User Service

<details>
<summary>POST /create</summary>

Body:

<pre>
{
	"username": "string",
}
</pre>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>User created<br/>
<pre>
{
	"userid": 0
}	
</pre>
</tr>
<tr><td>400</td><td>User unsuccessfully created</tr>
</table>
</details>

<details>
<summary>GET /{userid}</summary>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>User details<br/>
<pre>
{
	"userid": 0,
	"username": "string",
	"tickets": [
		{
			"eventid": 0
		}
	]
}
</pre>
</tr>
	<tr><td>400</td><td>User not found</tr>
</table>
</details>

<details>
<summary>POST /{userid}/tickets/add</summary>

Body:

<pre>
{
	"eventid": "string",
	"tickets": 0
}
</pre>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Event tickets added</tr>
	<tr><td>400</td><td>Tickets could not be added</tr>

</table>
</details>

<details>
<summary>POST /{userid}/tickets/transfer</summary>

Body:

<pre>
{
	"eventid": "string",
	"tickets": 0,
	"targetuser": 0
}
</pre>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Event tickets transfered</tr>
	<tr><td>400</td><td>Tickets could not be transfered</tr>
</table>

</details>

### Requirements

1. If you wish to use a web framework other than Servlets/Jetty you must first seek approval from the instructor.
2. You will design a thread-safe data structure to store data maintained your service. It is *not* required that you make the data persistent.


### Submission Requirements

By **Friday, March 30** at 5PM you must submit all code to your github repository for this assignment. 

Use the following link to create your private github repository for this assignment: [Project 3]()

For full credit, make sure to follow all [Style Guidelines](https://github.com/CS682-S18/notes/blob/master/style.md). Points will be deducted for each violation.


### Grading Rubric

| Points | Criterion |
| ------ | -------- |  

Partial credit may be awarded for partial functionality and/or partially correct design or style elements.

### Academic Dishonesty

Any work you submit is expected to be your own original work. If you use any web resources in developing your code you are strongly advised to cite those resources. The only exception to this rule is code that is posted on the class website. The URL of the resource you used in a comment in your code is fine. If I google even a single line of uncited code and find it on the internet you may get a 0 on the assignment or an F in the class. You may also get a 0 on the assignment or an F in the class if your solution is at all similar to that of any other student.
