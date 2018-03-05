# course-provider-api

The Course Provider API lets consumers query courses from Student Administration System (SAS).
 
This document describes the services provided by this API, their brief usage followed by
instructions to install.

Service Description:

The Course Provider API provides three services.

1st Services: Get All Courses
```
This service provides all the available courses on SAS system.
The service allows to paginate through the result by using offset and limit per page
as optional input parameters. 

The default values of offset is "0" and limit is "10".

Service URI: http://host:port/api/courses
```
Example:
```
URI: http://127.0.0.1:8081/api/courses?offset=0&limit=10

Response:
{
    "nextOffSet": 2,
    "currentLimit": "2",
    "totalCourses": 292,
    "remainingCourses": 290,
    "courses": [
        {
            "courseId": 1,
            "code": "COMP1",
            "name": "COMP1",
            "field": "ENGINEERING",
            "level": "MS",
            "mode": "ONLINE",
            "begins": "2016-12-12T00:00:00",
            "duration": 1,
            "fees": 300,
            "listed_on": "2017-07-01T00:00:00"
        },
        {
            "courseId": 2,
            "code": "COMP2",
            "name": "COMP2",
            "field": "ENGINEERING",
            "level": "MS",
            "mode": "ONLINE",
            "begins": "2017-01-12T00:00:00",
            "duration": 1,
            "fees": 300,
            "listed_on": "2017-07-02T00:00:00"
        }
    ],
    "success": true,
    "status": 200
}

```
2nd Service: Get All Latest Courses
```
This service provides all the latest available courses on SAS system.
The service also allows to paginate through the result by using offset and limit per page
as optional input parameters. It also allows to pass optional duration (in months) to 
focus on courses only available during that duration.

The default values of offset is "0", limit is "10" and duration is "6" months.

Service URI: http://host:port/api/courses/latest
```
Example:
```
URI: http://127.0.0.1:8081/api/courses/latest?offset=0&limit=10&duration=6

Response:
{
    "nextOffSet": 2,
    "currentLimit": "2",
    "totalCourses": 292,
    "remainingCourses": 290,
    "courses": [
        {
            "courseId": 1,
            "code": "COMP1",
            "name": "COMP1",
            "field": "ENGINEERING",
            "level": "MS",
            "mode": "ONLINE",
            "begins": "2016-12-12T00:00:00",
            "duration": 1,
            "fees": 300,
            "listed_on": "2017-07-01T00:00:00"
        },
        {
            "courseId": 2,
            "code": "COMP2",
            "name": "COMP2",
            "field": "ENGINEERING",
            "level": "MS",
            "mode": "ONLINE",
            "begins": "2017-01-12T00:00:00",
            "duration": 1,
            "fees": 300,
            "listed_on": "2017-07-02T00:00:00"
        }
    ],
    "success": true,
    "status": 200
}

```
3rd Service: Get A Specific Course Details
```
This service provides a specific course details from SAS system by using the Course ID

Service URI: http://host:port/api/courses/{courseId}
```
Example:
```
URI: http://127.0.0.1:8081/api/courses/4

{
	"course": [
		{
			"courseId": "1",
			"code": "ENGCSDS0001",
			"name": "Data Science",
			"field": "Engineering",
			"fees": "45000",
			"mode": "Online",
			"level": "Masters",
			"begins": "01-MAY-2018"
		}
	],
	"success": true,
	"status": 200
}

```
The API's can also be accessed through the MULE API KIT Console:
http://127.0.0.1:8081/console/
The Endpoint will list all the API's and its usage, it can also be used to test the Application.

System Requirement:
```
	- Mule Runtime Environment 3.8.3 EC/EE or more
	- H2 Database in Server Mode
```
Installation Instructions:
```
1. Install H2 Database and run it in Server mode

Note: If the database user doesn't have a password, then please add a password using the below SQL statement:
ALTER USER <username> SET PASSWORD <password>;

2. Download the Mule Project and import it to Anypoint Studio
3. Use the SQL script named "DBTableCreateAndPopulate.sql" present inside "src/main/resources/" to create the
required table and populate it with test data
4. Configure the h2DBUrl, h2DBUserName and h2DBPassword under "# DB Connection Property" 
in "course-provider-api.properties" present at "src/main/resources" for Database connection
5. Configure the httpHost, httpPort and httpBaseUri under "# HTTP Connection Property" in
"course-provider-api.properties" present at "src/main/resources" for HTTP connection
6. Check the required "h2-1.4.196.jar" for Database connection is present in the build path
if not then add "src/main/resources/h2-1.4.196.jar" as an external jar to the build path. 
7. If running the project with Maven support, then please copy the "h2-1.4.196.jar" from "src/main/resources/"
to Maven repository path like ".m2\repository\org\h2\h2\1.4.196" because some how downloaded jar by Maven is not working.
8. Now the project is configured and can be deployed or run using Anypoint Studio or mule standalone.

Note: The H2 Database need to run in server mode before the project is being tested to avoid database connection issue.
```
Challenges Faced:
```
1. Being new to Mule had to face many challenges however it was a very good learning exercise
2. Even after Mavenizing the project the h2 database connection was not working, so had to copy the "h2-1.4.196.jar"
shipped with h2 database installation to the Maven repository as mentioned in installation step above
3. Implementing caching and pagination was challenging had to do iterative testing to make sure it is working
```
Design and Development Considerations:
```
1. Modularisation at the interface (RAML) as well as coding level. This reduces the complexity of the system 
and makes both the interface as well as code more readable.
2. Tried to implement Reusability in the RAML as well as in the code by choosing small independent functionalities
as modules
3. Autonomy or coherency, the modules of functionality chosen as sub-flows or components are independent and has be
reused in the main flows
4. Readability, the above considerations such as Modularisation, Reusability and Coherency also increases readability
and reduce the complexity at the same time
5. Globalization of properties and configuration parameters instead of hard coding, to make the software independent
of the environment
6. Uniform messaging format, the format of all responses generated out are kept same, this reduces complexity on 
consuming systems as they can easily predict and interpret the responses
7. Uniform and centralized error handling, all the error handling is tried to be kept at one place for reusability
8. Limited tracing, only for Debug purposes for key functionalities
9. Global id based communication, a unique courseId is considered apart from course code, this makes the system 
loosely coupled and keeps the domain information of the system intact as per domain driven design principles
10. Considered pagination to make the system to be used by a mobile device tomorrow, the mobile device can specify
the provided to control browsing through the content easily. This also allows efficient use of network, by transferring
small volume of data each time
11. Considered caching to efficiently use the network as well DB resources, so that the number database calls can be
limited for frequent queries plus the improves turn-around time for a request
```
Alternate considerations:
```
1. At one point of time considering splitting the query of "courses" based on sub-resources such as "field", 
"level" and "mode" to reduce volume of data being queried and communicated. But chosen to implement pagination
instead. The reason is the former would have increased complexity at the consumer side as they have to data 
aggregation logics and run multiple queries where in-case of paged information only a filter logic will sufficient
and plus the same data can be used for purposes
```
Improvements:
```
If got more time then would like to investigate or implement for below improvements
1. Solution to h2 database jar file issue
2. To run the h2 database in embedded mode
3. Decouple the database calls to a separate module and re-use it from main flows
4. Implement fault configuration and security principles from FCAPS including authorization
5. Added Munit cases for sub-flows as well
```
At the end I would like to thank for giving me an opportunity to explore Mule. I enjoyed doing this exercise
and learned a lot about Mule and use Anypoint Studio for development, Munit for unit testing and Mule Designer
to build the RAML.


