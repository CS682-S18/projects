Project 3 - Service-Oriented Ticket Purchase Application
========================================================
*This project specification is incomplete and will change! Proceed at your own risk.*


### Code Due - TBD
### Deployment Due - TBD

For this project you will implement a service-oriented version of a ticket purchase service (i.e., your own EventBrite!). 

### Overview

The architecture of the web application will be as follows:

![architecture](https://docs.google.com/drawings/d/e/2PACX-1vTjBg_ZETz31hzGUrNL6Fh6GoSEUA9iWLSwyLnPdY0Ixg0YuHhVliwo4fJvfUhFp8mXIxz1dOHMZHw1/pub?w=960&h=720)

**Web Front End** - The web front end will implement an external web service API for the application and will support operations including creating users, viewing events, and purchasing tickets.

**Event Service** - The event service will manage the list of events and the number of tickets sold and available for each.

**User Service** - The user service will manage the user account information, including the events for which a user has purchased tickets.

### Logistics (read carefully!)

Every student must implement at least one service **individually**, however students may choose to work in teams on the portions described below.

A student who works individually must implement the Event Service and the Web Front End. We will provide you with a User Service implementation that you may test against. Your grade will be based only on the implementation of the Event Service and the Web Front End.

Students who choose to work in teams *may* work together on the implementation of the Web Front End. However, each student will be responsible for their own *individual* implementation of one service. You may not work as a team on the actual implementation of your service. If Student A implements the Event Service then Student B must implement the User Service. In this case, Student A's grade will be based on the Event Service and the Web Front End, and Student B's grade will be based on the User Service and the Web Front End.

### API

#### Front End Service

<details>
<summary>`GET /events`</summary>


Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Event Details<br/>
	<pre>
{
  "events": [
	{
	 	"eventid": 0, 
	 	"eventname": 	"string", 
	 	"avail": 0, 
	 	"purchased": 0
	},
		{
	 	"eventid": 2, 
	 	"eventname": 	"string", 
	 	"avail": 0, 
	 	"purchased": 0
	}
 
  ]
}
	</pre></td></tr>
	<tr><td>400</td><td>No events found</td></tr>

</table>

</details>

<details>
<summary>`POST /events/create`</summary>
Body:

```json
{
  "userid": 0,
  "eventname": "string",
  "numtickets": 0
}
```

Responses:

| Code | Description |
| ----| -----|
| 200 | Event created |
| 400 | Event unsuccessfully created |
</details>

<details>
	<summary>`GET /events/{eventid}`</summary>

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
	<summary>`GET /events/{eventid}/purchase`</summary>

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>Tickets purchased</td></tr>
	<tr><td>400</td><td>Tickets could not be purchased</td></tr>

</table>

</details>

<details>
	<summary>`POST /users/create`</summary>
Body:

```json
{
  "username": "string",
}
```

Responses:

<table>
	<tr><td>Code</td><td>Description</td></tr>
	<tr><td>200</td><td>User created</td></tr>
	<tr><td>400</td><td>User could not be created</td></tr>

</table>

</details>

<details>
	<summary>`GET /users/{userid}`</summary>

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
      "eventid": 0
    }
  ]
}
	</pre></td></tr>
	<tr><td>400</td><td>User not found</td></tr>

</table>

</detail>



### Requirements

1. If you wish to use a web framework other than Servlets/Jetty you must first seek approval from the instructor.
2. 



### Submission Requirements

There will be two deadlines for this assignment.

By **Thursday, Feb 22** at the *beginning* of class you must be prepared to demonstrate your working solution on the CS microcloud. When testing or running during demonstration please use only your [assigned microcloud node(s) and port(s)](https://github.com/CS682-S18/notes/blob/master/microcloud.md). For full demonstration credit your solution must be completely interoperable with the correct solution and the solution of any other student who also has a correct solution.

By **Friday, Feb 23** at 5PM you must submit all code and a jar file to your github repository for this assignment. Your jar file *must* be in a `lib` directory under your top-level repo directory and it *must* be named `project2.jar`. 

Use the following link to create your private github repository for this assignment: [Project 3]()

For full credit, make sure to follow all [Style Guidelines](https://github.com/CS682-S18/notes/blob/master/style.md). Points will be deducted for each violation.


### Grading Rubric

| Points | Criterion |
| ------ | -------- |  

Partial credit may be awarded for partial functionality and/or partially correct design or style elements.

### Academic Dishonesty

Any work you submit is expected to be your own original work. If you use any web resources in developing your code you are strongly advised to cite those resources. The only exception to this rule is code that is posted on the class website. The URL of the resource you used in a comment in your code is fine. If I google even a single line of uncited code and find it on the internet you may get a 0 on the assignment or an F in the class. You may also get a 0 on the assignment or an F in the class if your solution is at all similar to that of any other student.
