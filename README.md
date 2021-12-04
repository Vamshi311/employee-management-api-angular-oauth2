# Spring Boot Angular
1.	Create a Spring boot application using spring Initializer.
2.	Add REST services and expose them using controllers.
3.	Navigate to the root directory of spring boot application (directory with build.gradle file) in terminal and run “ng new <angular-app-name>”.
4.	Navigate to <angular-app-name> directory in terminal and run “ng add @angular/material” to install angular material modules to our angular application.
5.	Our spring boot angular application is ready. To make REST calls from angular to spring boot add proxy config as explained below.

### Make REST calls from Angular service to Spring Boot REST service
1.	Add proxy.config.json under root directory with below content

```
{
    "/server": {
      "target": "http://localhost:8080",
      "secure": false,
      "changeOrigin": true,
      "pathRewrite": {
        "^/server": ""
      },
      "timeout": 600000
    }
  }
 ```
  
2.	In package.json, edit the “start” command to     "start": "ng serve --proxy-config proxy.config.json" from “start”:”ng serve”.
3.	Run “npm start” from terminal to start angular app with those proxy settings.
4.	Then in angular service make rest calls like below:
this.http.get<Student[]>("/server/student/all");
all the calls that start with “/server” context will be diverted to http://localhost:8080. And the REST URI it calls is http://localhost:8080/student/all. 
“/server” will be replaced with “”. If you don’t want to replace any part of URI then remove “pathRewrite” property in proxy config. 
Then REST URI it calls is http://localhost:8080/server/student/all.

### How basic authentication is implemented?
We have created a new AuthService with three methods. authenticate(username, password), isAuthenticated() and logout().
authenticate(username, password) – validates user credentials and saves token to localStorage or sessionStorage if they are valid.
isAuthenticated() – checks if the token in localStorage or sessionStorage exists or is expired or not to validate user is authenticated or not.
logout() – removes token from localStorage or sessionStorage.
When user tries to access protected angular route, then canActivate implementation kicks in and checks if the user is authenticated or not (using AuthService). If authenticated, returns true 
and user will be navigated to protected angular route otherwise user will be navigated to login page. If user is at login page and enters valid credentials then user will be navigated to home 
page and token will be saved to localStorage or sessionStorage. When making REST calls, that token will be retrieved form localStorage or sessionStorage and sent in headers. Make use of interceptor
that intercepts HTTP calls and add authorization header before forwarding the REST call to backend.

### How OAuth2 is working in our sample angular application?
For secured angular routes, we set “canActivate” to an array with AuthGuard service which is an implementation of CanActivate interface from @angular/router.
AuthGuard will check if the user is authenticated or not. If not, it calls login functionality. Once the user is authenticated, auth token is saved to local storage 
and user will be redirected to callback component and then “/students” route. While making REST calls, that token is added to headers. When user tries to access another
protected angular route, AuthGuard again checks if the user is authenticated by checking if auth token exists and it is not expired.
When user logout, remove token from local storage.
Use “Router “ component from @angular/router to programmatically route the user to a route.
Example: router.navigate([“student”]);

### References:
* https://www.javainuse.com/spring/ang7-hello - high priority (covers basic auth and OAuth2 with angular)
* https://pavankjadda.medium.com/oauth2-authentication-with-spring-boot2-mysql-and-angular-7-488bdfe82cc1
* https://www.devglan.com/spring-security/spring-boot-oauth2-angular

* https://www.javainuse.com/spring/boot-jwt - spring boot OAuth2 example
also refer okta
* Building Your First App with Spring Boot and Angular – pluralsight tutorial

