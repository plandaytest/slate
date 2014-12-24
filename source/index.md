---
title: Planday REST API

language_tabs:
- ruby: http
- python: swagger
- shell: .net

toc_footers:
- <a href='http://www.planday.com'>Planday Website</a>
- <a href='mailto:contact@planday.com'>Contact</a>

search: true
---


# Introduction

Planday API is a fully hosted solution to manage employee scheduling and payroll support. We provide a REST API built on pragmatic RESTful design principles.

Our API uses resource-oriented URLs that leverage built in features of HTTP, like authentication, verbs and response codes. All request and response bodies are JSON encoded, including error responses. Any off-the-shelf HTTP client can be used to communicate with the API.

We believe an API is a user interface for a developer - accordingly, we've made sure our API can be easily explored from the browser!


# Getting started guides (simple guides on how to get started)

>Please use the followings link for access Planday API

```ruby
http://api.planday.net/
```

```python
http://api.planday.net/swagger/
```

```shell
APIService.cs - Define the constant URL to API service

namespace Planday.Website.Utils
{
  public class APIService
  {
    public static readonly string API_ROOT_HOST = "http://api.planday.com/v1";
    private static readonly string API_AUTHENTICATION_LOGIN = "/authentication/login";
    private static readonly string API_EMPLOYEE_GET_ALL = "/hr/employees";
    public static readonly string URL_AUTHENTICATION_LOGIN = API_ROOT_HOST + API_AUTHENTICATION_LOGIN;
    public static readonly string URL_EMPLOYEE_GET_ALL = API_ROOT_HOST + API_EMPLOYEE_GET_ALL;
    public static readonly string URL_EMPLOYEE_GET_SINGLE = API_ROOT_HOST + API_EMPLOYEE_GET_ALL + "/{0}";
  }
}

ServiceRequest.cs - Util class for calling to the REST service

using System;
using System.IO;
using System.Net;
using System.Text;
using System.Web;
namespace Planday.Website.Utils
{
  public enum HttpRequestMethod
  {
    Get,
    Post,
    Put,
    Delete
  }
  public class ServiceRequest
  {
    private string address;
    private object postContent;
    private string queryString;
    private HttpRequestMethod method;
    private HttpSessionStateBase session;
    public ServiceRequest( HttpSessionStateBase _session, string _address, string _queryString, HttpRequestMethod _method, object _postContent = null )
    {
      session = _session;
      address = _address;
      queryString = _queryString;
      method = _method;
      postContent = _postContent;
    }
    public object GetResponse()
    {
      if ( session[ Constants.TOKEN ] != null )
      {
        address = string.Format( "{0}?api_key={1}", address, session[ Constants.TOKEN ] );
      }
      if ( !string.IsNullOrEmpty( queryString ) )
      {
        address = string.Format( "{0}&{1}", address, queryString );
      }
      var http = (HttpWebRequest)WebRequest.Create( new Uri( address ) );
      http.Accept = "application/json";
      http.ContentType = "application/json";
      http.Method = method.ToString();
      if ( postContent != null )
      {
        string parsedContent = postContent.ToString();
        ASCIIEncoding encoding = new ASCIIEncoding();
        Byte[] bytes = encoding.GetBytes( parsedContent );
        Stream newStream = http.GetRequestStream();
        newStream.Write( bytes, 0, bytes.Length );
        newStream.Close();
      }
      var response = http.GetResponse();
      var stream = response.GetResponseStream();
      var sr = new StreamReader( stream );
      var content = sr.ReadToEnd();
      return content;
    }
  }
}

```

Planday API allows you to call to modules of Planday service that supports RESTful with JSON style.


Addresses:


* API: [http://api.planday.net/](http://api.planday.net/). This is main URL of Planday API for current version, if you want to work with another version, go to [http://api.planday.net/v{x}/](http://api.planday.net/v{x}/) with x is the working version, for example [http://api.planday.net/v1/](http://api.planday.net/v1/)  

* API document: [http://document.api.planday.net/](http://document.api.planday.net/)

* Swagger UI: [http://api.planday.net/swagger/](http://api.planday.net/swagger/)


# Changes

This is a versionless API. Advance notification of breaking changes will be available in this document and will be sent by email to our developer mailing list.

# Authentication

```ruby
POST http://api.planday.net/v1/authentication/login/

**Request Body**

{
  "portalAlias": "jmi.planday.dk",
  "username": "100",
  "password": "2wsx"
}

Successful login
**Response Body**

{
  "token": "zZFZKkyU3oFjZAmdY8GUGxZqbaex1eiz",
  "_links": [
    {
      "rel": "self",
      "href": "~/authentication/login",
      "title": null,
      "isTemplated": false
    },
    {
      "rel": "employees_in_portal",
      "href": "~/hr/employees{?page}",
      "title": null,
      "isTemplated": true
    },
    {
      "rel": "timesheet",
      "href": "~/schedule/timesheets{?page}",
      "title": "Get timesheets",
      "isTemplated": true
    }
  ],
  "_embedded": null
}

Unsuccessful login
**Response Body**

{
  "error": {
    "errors": [],
    "code": 400,
    "message": "Incorrect information"
  }
}

```
```python
http://api.planday.net/swagger/ui/index.html#!/Authentication/Authentication_Login

**Login Info**

{
  "portalAlias": "jmi.planday.dk",
  "username": "100",
  "password": "2wsx"
}

Successful login
**Response Body**

{
  "token": "zZFZKkyU3oFjZAmdY8GUGxZqbaex1eiz",
  "_links": [
    {
      "rel": "self",
      "href": "~/authentication/login",
      "title": null,
      "isTemplated": false
    },
    {
      "rel": "employees_in_portal",
      "href": "~/hr/employees{?page}",
      "title": null,
      "isTemplated": true
    },
    {
      "rel": "timesheet",
      "href": "~/schedule/timesheets{?page}",
      "title": "Get timesheets",
      "isTemplated": true
    }
  ],
  "_embedded": null
}

Unsuccessful login
**Response Body**

{
  "error": {
    "errors": [],
    "code": 400,
    "message": "Incorrect information"
  }
}

```


```shell

Login.cs - Login model

namespace Planday.Website.Models.Authentication
{
  public class Login
  {
    public string PortalAlias { get; set; }
    public string Username { get; set; }
    public string Password { get; set; }
  }
}

LoginResult.cs - LoginResult model

using System.Collections.Generic;
namespace Planday.Website.Models.Authentication
{
  public class Link
  {
    public string rel { get; set; }
    public string href { get; set; }
    public string title { get; set; }
    public bool isTemplated { get; set; }
  }
  public class LoginResult
  {
    public string token { get; set; }
    public List<Link> _links { get; set; }
    public object _embedded { get; set; }
  }
}

Call to Authentication API to login and get token

Call to login service and get token

public ActionResult Login( Login login )
{
  try
  {
    var json = new JavaScriptSerializer().Serialize( login );
    ServiceRequest request = new ServiceRequest( Session, APIService.URL_AUTHENTICATION_LOGIN, string.Empty, HttpRequestMethod.Post, json );
    var content = request.GetResponse();
    DataContractJsonSerializer serializer = new DataContractJsonSerializer( typeof( LoginResult ) );
    LoginResult loginResult = null;
    using ( var ms = new MemoryStream( Encoding.Unicode.GetBytes( content.ToString() ) ) )
    {
      loginResult = (LoginResult)serializer.ReadObject( ms );
    }
    Session.Add( Constants.TOKEN, loginResult.token );
    ViewBag.Message = "Logged in successfully!";
    return View();
  }
  catch ( Exception ex )
  {
    ViewBag.Message = "Logged in not successfully!" + ex.Message;
    return View();
  }
}

```

Virtually all API calls require authentication for access with appropriate authorisation. So you must call to Authentication/Login API before calling any API with the following information:


On success the API will return the login information as well as token that allows you to call other APIs next time. This API may return HTTP code 400 if incorrect information was provided.


**URL**
`http://api.planday.net/v1/authentication/login/`

**Method**
`POST`

**Query Parameters**

Parameter | Description
--------- | -----------
portal_alias | Planday username
user_name | Planday password
password | The alias of portal of log-in account


**Response**

**Successful login**

<aside class="success">
HTTP code 200
</aside>

**Unsuccessful login**

<aside class="warning">
HTTP code 400
</aside>


# Requests
```ruby
Example Requests:

GET http://api.planday.net/v1/employees//0 returns a list of employee belong to current portal.

GET http://api.planday.net/v1/employees/100/ returns an employee information with Id = 100.

GET http://api.planday.net/v1/employees/100/departments/ returns the department list that an employee with Id = 100 belongs to.

POST http://api.planday.net/v1/employees/ creates an employee.

PUT http://api.planday.net/v1/employees/100/ updates the information of employee who has 100 as Id

PUT http://api.planday.net/v1/employees/100/departments/ put employee with Id = 100 to the departments that are in request body.

DELETE http://api.planday.net/v1/employees/100/ deletes an employee information who has 100 as Id

DELETE http://api.planday.net/v1/employees/100/departments remove employee with Id = 100 from the departments that are in request body.

```

```python
Example Requests:

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_Get

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_Get returns a list of employee belong to current portal.

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_Get_0/ returns an employee information with input Id specified in swagger UI.

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_GetDepartments returns the department list that an employee with input Id belongs to.

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_New creates an employee.

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_Update  updates the information of employee who has Id specified in swagger UI input option

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_AddEmployeeToDepartments put employee to the departments that are in request body.

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_Delete deletes an employee information

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_RemoveEmployeeFromDepartments remove employee from the departments that are in request body.

```


Planday API allows you to call the service that supports RESTful in JSON format. The APIs are categorised by the modules:
Authentication:

* Login: [http://api.planday.net/v1/authentication/login/](http://api.planday.net/v1/authentication/login/)
* Logout: [http://api.planday.net/v1/authentication/logout/](http://api.planday.net/v1/authentication/logout/)
* HR: Provides the APIs for operating with HR data includes departments, groups and employees..
* Schedule: Provide the APIs for operating with schedule data includes day schedule, timesheet, puch-clock..

Each API is organised by the format [http://api.planday.net/v1/{modules}/entity/{id-or-property}](http://api.planday.net/v1/{modules}/entity/{id-or-property}) and used HTTP method as the verb of operation as following:



HTTP method| Operation
---------- | -------
GET	|Get an entity, entity list or entity's properties / relations.
POST	|Create an entity, property or relation.
PUT	|Update an entity, property or relation.
DELETE	|Delete an employee, property or relation.

Please note that we use these methods for the operations as standard and will inform you in special case.

# Responses (including errors)

```ruby
Example Call

GET http://localhost:2888/api/hr/employees/432432/groups/

Call to get departments of an employee that is not found. Method return validation error.

**Response Body**
{
  "error": {
    "errors": [
      {
        "propertyName": "employeeId",
        "propertyValue": 432432,
        "errorMessage": "Employee does not exist"
      }
    ],
    "code": 400,
    "message": "Employee does not exist"
  }
}
```

```python
Example Call

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_GetDepartments

Swagger UI input: Id = 432432

Call to get departments of an employee that is not found. Method return validation error.

**Response Body**
{
  "error": {
    "errors": [
      {
        "propertyName": "employeeId",
        "propertyValue": 432432,
        "errorMessage": "Employee does not exist"
      }
    ],
    "code": 400,
    "message": "Employee does not exist"
  }
}
```




Every request to Planday API will return one of the following response codes, include the HTTP status code. On a successful request, the data will be return in JSON format with HTPP code 200. On an unsuccessful operation, another HTTP code will be return, and the message with our standard code to describe more about the error.


HTTP code| API code | Meaning
---------- | ------- | -------
200	|200	|The call is successfully made, data will be returned in JSON format.
401	|401	|Unathorized. Invalid token or the user not logged in yet.
404	|404	|Not found. The requested entity is not found.
409	|409-xx	|Conflicted. For example, duplicated username. (xx is a number)
400	|400-010	|Bad request. For example, call to get an employee with Id is in invalid format.
400	|400-020	|Validation error. For example, call to get departments of an employee that is not found.
500	|500	|Internal error.

# Rate Limiting (exclude for now)
# Field Filtering (partial rep)
# Embedding (extension)
# Pagination
# Sorting
# Caching & Concurrency Control

# [*Ressources and Models]
## Web Hook
## Employee
This module contains the APIs to operate with employee entity

### Get employee list

**Title**
Get the employee list of current portal

```ruby
Get employee list using jquery

$.ajax({
  url: "http://api.planday.net/v1/employees/?api_key=qadbXS1tEQdtPlPPFAz1ZAc4KCWJ2kbbh&%24skip=25&%24top=10&%",
  dataType: "json",
  type : "GET",
  success : function(r) {
    console.log(r);
  }
  });


```

```python
Get employee list using swagger ui

http://api.planday.net/swagger/ui/index.html#!/Employees/Employees_Get

swagger option
api_key=qadbXS1tEQdtPlPPFAz1ZAc4KCWJ2kbbh
$skip=25
$top=10

```


```shell

Get employee list of current portal

Define Employee and EmployeeListResult model

Employee.cs - Employee model

using System;
using System.Collections.Generic;
namespace Planday.Website.Models.HR
{
  public class Credentials
  {
  }
  public class Department
  {
    public int id { get; set; }
  }
  public class EmployeeGroup
  {
    public int id { get; set; }
  }
  public class Link
  {
    public string rel { get; set; }
    public string href { get; set; }
    public object title { get; set; }
    public bool isTemplated { get; set; }
  }
  public class Employee
  {
    public int id { get; set; }
    public string firstName { get; set; }
    public object middleName { get; set; }
    public string lastName { get; set; }
    public Credentials credentials { get; set; }
    public string birthday { get; set; }
    public List<Department> departments { get; set; }
    public List<EmployeeGroup> employeeGroups { get; set; }
    public string initials { get; set; }
    public string nickName { get; set; }
    public string ssn { get; set; }
    public string zip { get; set; }
    public string city { get; set; }
    public Nullable<long> countryId { get; set; }
    public Nullable<long> regionId { get; set; }
    public string cellPhone { get; set; }
    public int? cellPhoneCountryId { get; set; }
    public string phone { get; set; }
    public Nullable<long> phoneCountryId { get; set; }
    public string hiredDate { get; set; }
    public string bankRegNumber { get; set; }
    public string bankAccount { get; set; }
    public string description { get; set; }
    public string workHours { get; set; }
    public string salaryIdentifier { get; set; }
    public bool isMale { get; set; }
    public bool isPublic { get; set; }
    public bool isEmailNotify { get; set; }
    public object profileImage { get; set; }
    public object profileImageThumb { get; set; }
    public double lieuOverTimeBalance { get; set; }
    public double employeeBuyBalance { get; set; }
    public Nullable<long> languageId { get; set; }
    public int employeeTypeId { get; set; }
    public string email { get; set; }
    public object image { get; set; }
    public Nullable<long> timeZoneId { get; set; }
    public object employeeProperties { get; set; }
    public List<Link> _links { get; set; }
    public object _embedded { get; set; }
  }
}


EmployeeListResult.cs - Employee list model

using System.Collections.Generic;
namespace Planday.Website.Models.HR
{
  public class EmployeeListResult
  {
    public int totalResults { get; set; }
    public int totalPages { get; set; }
    public int page { get; set; }
    public List<Employee> resourceList { get; set; }
    public List<Link> _links { get; set; }
    public object _embedded { get; set; }
  }
}

Call to API to get employee list

public ActionResult Index()
{
  try
  {
    ServiceRequest request = new ServiceRequest( Session, APIService.URL_EMPLOYEE_GET_ALL, string.Empty, HttpRequestMethod.Get );
    var content = request.GetResponse();
    DataContractJsonSerializer serializer = new DataContractJsonSerializer( typeof( EmployeeListResult ) );
    EmployeeListResult employeeListResult = null;
    using ( var ms = new MemoryStream( Encoding.Unicode.GetBytes( content.ToString() ) ) )
    {
      employeeListResult = (EmployeeListResult)serializer.ReadObject( ms );
    }
    return View( employeeListResult.resourceList );
  }
  catch ( Exception ex )
  {
    return View();
  }
}

You can get the full sample project here.

```




**URL**
`http://api.planday.net/v1/employees/`

**Method**
`GET`

**Query Parameters**

Parameter | Description
--------- | -----------
api_key | Access token
$skip | Number of first items should be skipped. Example: $skip=25
$top | Number of first items to select. It's the number of items in the page as well. Example: $top=10
$orderby | Order operation. Example: $orderby=FirstName,LastName
$filter | Filter operation. Example: $filter=FirstName eq 'Christian'


**Response**

**Successful Response**

```ruby
Code: 200 Success Response
Employee list with pagination

**Response Body**
{
  "totalResults": 202,
  "totalPages": 101,
  "page": 51,
  "resourceList": [
    {
    ...
    }
}

```

```python
Code: 200 Success Response
Employee list with pagination

**Response Body**
{
  "totalResults": 202,
  "totalPages": 101,
  "page": 51,
  "resourceList": [
  {
  ...
  }
}

```



<aside class="success">
Code: 200

Response: Employee list with pagination
</aside>

**Error Response**

```ruby
Code: 400 BAD_REQUEST
Example

**Response Body**
{
  "error": {
    "errors": [],
    "code": 400,
    "message": "Could not find a property named 'FirstNam' on type 'Employee'."
  }
}

Code: 401 UNAUTHORIZED
Example

**Response Body**
{
  "error": {
    "errors": [],
    "code": 401,
    "message": "Your token is expired or invalid."
  }
}

```

```python

Code: 400 BAD_REQUEST
Example

**Response Body**
{
"error": {
"errors": [],
"code": 400,
"message": "Could not find a property named 'FirstNam' on type 'Employee'."
}
}

Code: 401 UNAUTHORIZED
Example

**Response Body**
{
"error": {
"errors": [],
"code": 401,
"message": "Your token is expired or invalid."
}
}


```

<aside class="warning">
Code: 400

Response: Bad Request
</aside>

<aside class="warning">
Code: 401

Response: Unauthorized
</aside>


**Note**
We use OData as the query building, please go to [OData](http://odata.org) website for reference.


### Get Single Employee

**Title**
Get the employee of current portal by Id

```ruby
Get employee data using jquery

$.ajax({
  url: "http://api.planday.net/v1/employees/100",
  dataType: "json",
  type : "GET",
  success : function(r) {
    console.log(r);
  }
  });


```


```shell


```

**URL**
  `http://api.planday.net/v1/employees/{id}`

  **Method**
  `GET`

  **Query Parameters**

  Parameter | Description
  --------- | -----------
  api_key | Access token
  Id | Id of employee


  **Response**

  **Successful Response**

  ```ruby



  ```

  <aside class="success">
  Code: 200

  Response: Employee data
  </aside>


  **Unsuccessful Response**

  ```ruby
  Code: 404 NOT_FOUND
  Example
  **Response Body**
  {
    "error": {
      "errors": [],
      "code": 404,
      "message": "Employee does not exist"
    }
  }

  Code: 401 UNAUTHORIZED
  Example
  **Response Body**
  {
    "error": {
      "errors": [],
      "code": 401,
      "message": "Your token is expired or invalid."
    }
  }


  ```

  ```python
  Code: 404 NOT_FOUND
  Example
  **Response Body**
  {
    "error": {
      "errors": [],
      "code": 404,
      "message": "Employee does not exist"
    }
  }

  Code: 401 UNAUTHORIZED
  Example
  **Response Body**
  {
    "error": {
      "errors": [],
      "code": 401,
      "message": "Your token is expired or invalid."
    }
  }


  ```

  <aside class="warning">
  Code: 400

  Response: Bad Request
  </aside>

  <aside class="warning">
  Code: 401

  Response: Unauthorized
  </aside>


## Employee Property
### Get Custom Property List
### Get A Single Custom Property
## Department
### Get Department List
### Get Single Department
