# Missing Function Level Access Control

When a user with lower level of access can manipulate the website to find ways to gain
higher level of access

Missing function level access control is a security vulnerability that occurs when an application does not properly restrict access to certain sensitive functions. This can allow attackers to gain unauthorized access to sensitive information or perform actions that they should not be able to. Function level access control is a fundamental security principle that ensures that only authorized users are able to access sensitive functions or data within an application. This vulnerability is part of the OWASP Top 10 A01:2021 Broken Access Control category and represents one of the most serious web application security risks

## Net Core

There are several ways to implement authorization in .NET Core using AuthorizeAttribute class and its parameters. Which one to use will depend on business requirements to implement function level access control.

### Simple authorization
Applying [Authorize] attribute to a controller or an individual action limits access to that component to authenticated users. Be aware that when combining [Authorize] and [AllowAnonymous], the [Authorize] attribute will be ignored.

In this way, access is limited to a specific action or all actions within a controller to only authenticated users.

### Role-based authorization
Role-based authorization allows the specification of which users are allowed to perform certain actions based on their assigned roles. For example, there may be 'Manager' role that is allowed to access certain resources or a 'User' role that is only allowed to perform certain actions. To implement role-based authorization in ASP.NET Core, the [Authorize(Roles = "Admin")] attribute can be used on specific controller actions or controller classes and specifies the roles that are allowed to access those actions or classes.

### Claims-based authorization
Claims-based authorization allows the specification of which users are allowed to perform certain actions based on their claims. Claims are pieces of information that are associated with a user, such as their name, email address, or other identifying information. To implement claims-based authorization in ASP.NET Core, the [Authorize(Policy = "EmployeeOnly")] attribute can be used to specify the claims that are required to access a particular action or resource.