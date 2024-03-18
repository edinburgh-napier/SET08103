# Lab 08: Deployment

In this lab we are going to complete two tasks:

- Automate the release of our software to GitHub Releases using GitHub Actions.

- Generate our reports as markdown files and copy these back to a new branch on GitHub

## Deploying to GitHub Releases

We have already seen how to generate a release on GitHub manually. GitHub actions can automate this process as part of our [Continuous Delivery](../../lectures/lecture16) workflow to produce a JAR file in our repository .

### Updating the GitHub Actions Script

We are going to modify our existing *build* stage in our GitHub Actions script so it pushes the built JAR file to GitHub Releases.  We could add this as a separate stage but as the build stage already creates a jar file we will append our deployment action at the end of the stage.

```yml
build:
    name: Build Run in Docker and Deploy Release
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          submodules: recursive
      - name: Set up JDK 11
        uses: actions/setup-java@v2
        with:
          java-version: '11'
          distribution: 'adopt'
      - name: Package and Run docker compose
        run: |
          mvn package -DskipTests
          docker-compose up --abort-on-container-exit
      - uses: "marvinpinto/action-automatic-releases@latest"
        with:
          repo_token: "${{ secrets.GITHUB_TOKEN }}"
          prerelease: false
          automatic_release_tag: "latest"
          files: |
            ./target/*.jar

```

Let us consider the steps GitHub Actions will go through:

1. It will checkout the repo as normal and setup the GitHub Actions Java environment.
2. It will package the code to a jar with dependencies called `seMethods` skipping the maven test stage.
3. The script uses the `"marvinpinto/action-automatic-releases@latest"` action to perform the following tasks:
    - Set the repository using the global variable `${{ secrets.GITHUB_TOKEN }}` This is automatically created by GitHub Actions
    - Set the release tag to `latest`
    
    - Copy any jar files from our target directory to GitHub Releases

For reference, the complete `main.yml` file is below:

```yml
name: A workflow for my Hello World App
on:
  push:
    branches:
      - master
jobs:
  UnitTests:
    name: Unit Tests
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          submodules: recursive
      - name: Set up JDK 11
        uses: actions/setup-java@v2
        with:
          java-version: '11'
          distribution: 'adopt'
      - name: Unit Tests
        run: mvn -Dtest=com.napier.sem.AppTest test
      - name: CodeCov
        uses: codecov/codecov-action@v2
        with:
          token: ${{ secrets.CODECOV_TOKEN }} # now required for public repos 
          directory: ./target/site/jacoco
          flags: Unit Tests # optional
          verbose: true # optional (default = false)

  IntegrationTests:
    name: Integration Tests
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          submodules: recursive
      - name: Set up JDK 11
        uses: actions/setup-java@v2
        with:
          java-version: '11'
          distribution: 'adopt'
      - name: Integration Tests and CodeCov
        run: |
          docker build -t database ./db 
          docker run --name employees -dp 33060:3306 database
          mvn -Dtest=com.napier.sem.AppIntegrationTest test          
          docker stop employees
          docker rm employees
          docker image rm database                    
      - name: CodeCov
        uses: codecov/codecov-action@v2
        with:
          token: ${{ secrets.CODECOV_TOKEN }} # now required for public repos
          directory: ./target/site/jacoco
          flags: Integration Tests # optional
          verbose: true # optional (default = false)
  build:
    name: Build Run in Docker and Deploy Release
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          submodules: recursive
      - name: Set up JDK 11
        uses: actions/setup-java@v2
        with:
          java-version: '11'
          distribution: 'adopt'
      - name: Package and Run docker compose
        run: |
          mvn package -DskipTests
          docker-compose up --abort-on-container-exit
      - uses: "marvinpinto/action-automatic-releases@latest"
        with:
          repo_token: "${{ secrets.GITHUB_TOKEN }}"
          prerelease: false
          automatic_release_tag: "latest"
          files: |
            ./target/*.jar

```

> Note that GitHub Actions will only run when we push to the **master** branch. **Develop** was removed.

I have also added to the Maven `pom.xml` so that only the jar with dependencies is built during the Maven Package stage.

The complete Maven file is shown below for reference.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.napier.sem</groupId>
    <artifactId>sem_employees</artifactId>
    <version>0.1.0.3</version>

    <properties>
        <maven.compiler.source>10</maven.compiler.source>
        <maven.compiler.target>10</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.1.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.2.0</version>
                <executions>
                    <execution>
                        <id>default-jar</id>
                        <!-- skip building the default-jar-->
                        <phase>none</phase>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-assembly-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <finalName>seMethods</finalName>
                    <archive>
                        <manifest>
                            <mainClass>com.napier.sem.App</mainClass>
                        </manifest>
                    </archive>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                    <appendAssemblyId>false</appendAssemblyId>
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id>
                        <phase>package</phase>
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.19.1</version>
                <dependencies>
                    <dependency>
                        <groupId>org.junit.platform</groupId>
                        <artifactId>junit-platform-surefire-provider</artifactId>
                        <version>1.1.0</version>
                    </dependency>
                    <dependency>
                        <groupId>org.junit.jupiter</groupId>
                        <artifactId>junit-jupiter-engine</artifactId>
                        <version>5.1.0</version>
                    </dependency>
                </dependencies>
            </plugin>
            <plugin>
                <groupId>org.jacoco</groupId>
                <artifactId>jacoco-maven-plugin</artifactId>
                <version>0.8.2</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>prepare-agent</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>report</id>
                        <phase>test</phase>
                        <goals>
                            <goal>report</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

Once happy with your changes merge them into master and push your changes to GitHub. Once GitHub Actions is complete if you look up your Releases you will see the new release there.  Note that `seMethods.jar` has been added.

![1](img/release.png)


> Publishing the build jar file as a release on GitHub is one way to provide an automated release. Ideally we would deploy our code to a cloud environment such as Google Cloud or Microsoft Azure. This is something that was originally included on the module but has been removed due to potential financial costs to students. If you are interested there are plenty of GitHub Actions resources available to do this.  For example https://github.com/google-github-actions/deploy-cloud-functions
>



# Optional Extras

The following sections of this lab are provided as additional resources that you can use to make interacting with the Application easier. For the coursework you only need to provide evidence that the reports have been generated, such as screenshots of the console (see [lab 12](../lab12/README.md)). The following sections provide more intuitive ways to display the reports to the end user.

## Output the Reports to a GitHub Branch

Currently we are outputting our reports to the console. In this section we are going to generate one of the reports to a markdown file within the docker container then get GitHub Actions to copy this report to a new branch in our repository. 

The main method of the App is now:
```java
       // Create new Application and connect to database
        App app = new App();

        if (args.length < 1) {
            app.connect("localhost:33060", 0);
        } else {
            app.connect(args[0], Integer.parseInt(args[1]));
        }

        ArrayList<Employee> employees = app.getSalariesByRole("Manager");
        app.outputEmployees(employees, "ManagerSalaries.md");

        // Disconnect from database
        app.disconnect();
```
We use the following sql in the getSalariesByRole method replacing the 'Manager' with the role parameter passed to the method

```sql
SELECT employees.emp_no, employees.first_name, employees.last_name,
titles.title, salaries.salary, departments.dept_name, dept_manager.emp_no
FROM employees, salaries, titles, departments, dept_emp, dept_manager
WHERE employees.emp_no = salaries.emp_no
  AND salaries.to_date = '9999-01-01'
  AND titles.emp_no = employees.emp_no
  AND titles.to_date = '9999-01-01'
  AND dept_emp.emp_no = employees.emp_no
  AND dept_emp.to_date = '9999-01-01'
  AND departments.dept_no = dept_emp.dept_no
  AND dept_manager.dept_no = dept_emp.dept_no
  AND dept_manager.to_date = '9999-01-01'
  AND titles.title = 'Manager'
```

The method returns an `ArrayList<Employee>` which is output to file using the method `app.outputEmployees(employees, "ManagerSalaries.md"); which is listed below`

```java
    /**
     * Outputs to Markdown
     *
     * @param employees
     */
    public void outputEmployees(ArrayList<Employee> employees, String filename) {
        // Check employees is not null
        if (employees == null) {
            System.out.println("No employees");
            return;
        }

        StringBuilder sb = new StringBuilder();
        // Print header
        sb.append("| Emp No | First Name | Last Name | Title | Salary | Department |                    Manager |\r\n");
        sb.append("| --- | --- | --- | --- | --- | --- | --- |\r\n");
        // Loop over all employees in the list
        for (Employee emp : employees) {
            if (emp == null) continue;
            sb.append("| " + emp.emp_no + " | " +
                    emp.first_name + " | " + emp.last_name + " | " +
                    emp.title + " | " + emp.salary + " | "
                    + emp.dept_name + " | " + emp.manager + " |\r\n");
        }
        try {
            new File("./reports/").mkdir();
            BufferedWriter writer = new BufferedWriter(new FileWriter(new                                 File("./reports/" + filename)));            
            writer.write(sb.toString());
            writer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

This will produce a directory called reports with a file called `ManagerSalaries.md` 

To copy the report from inside the docker container on GitHub Actions we need to add an action to our GitHub Actions yml file

Add the following to the end of your yml file within the build and deploy stage. Note you will need to change the name of your container on the second line. For a project named `sem_employees` that uses docker_compose to build a container called `app`, GitHub Actions will name the container `sem_employees_app_1`
```yml
      - name: Copy Output
        run: docker container cp sem_employees_app_1:./tmp/reports ./
      - name: Deploy
        uses: JamesIves/github-pages-deploy-action@v4.2.5
        with:
          branch: reports # The branch the action should deploy to.
          folder: reports # The folder we are copying
```

If successful then we should have a new branch in our repository called reports containing the markdown file which should look like the following.

| Emp No | First Name | Last Name | Title | Salary | Department | Manager |
| --- | --- | --- | --- | --- | --- | --- |
| 110039 | Vishwani | Minakawa | Manager | 106491 | Marketing | 110039 |
| 110114 | Isamu | Legleitner | Manager | 83457 | Finance | 110114 |
| 110228 | Karsten | Sigstam | Manager | 65400 | Human Resources | 110228 |
| 110420 | Oscar | Ghazalie | Manager | 56654 | Production | 110420 |
| 110567 | Leon | DasSarma | Manager | 74510 | Development | 110567 |
| 110854 | Dung | Pesch | Manager | 72876 | Quality Management | 110854 |
| 111133 | Hauke | Zhang | Manager | 101987 | Sales | 111133 |
| 111534 | Hilary | Kambil | Manager | 79393 | Research | 111534 |
| 111939 | Yuchang | Weedman | Manager | 58745 | Customer Service | 111939 |


## Converting to a Web App

Another option is to convert our application to act as a Web Application. This will take a bit of time and patience and is not required for  the coursework. 

To do this we will create a REST service.  REST is just a form of application where we access resources via a URL.  REST behaviour can be added to our Java application via the Java Spring Framework.  This just requires a few modifications to our Maven `pom.xml` file.

### Adding Spring

First, we need Maven to treat our project as a child of a standard Spring project.  **Add the following** after the existing `<groupID>` section:

```xml
# Existing code
<groupId>com.napier.sem</groupId>
<artifactId>seMethods</artifactId>
<version>0.1.0.8</version>

# New code
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.5.RELEASE</version>
</parent>
```

Now add the following to our `<dependencies>` section:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

We also need to change how Maven packages our application.  Change the `<artifactId>maven-assembly-plugin</artifactId>` section in the `<plugins>` section to:

```xml
<plugin>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-maven-plugin</artifactId>
  <configuration>
      <finalName>seMethods</finalName>
      <mainClass>com.napier.sem.App</mainClass>
  </configuration>
  <executions>
      <execution>
          <id>make-assembly</id>
          <phase>package</phase>
          <goals>
              <goal>repackage</goal>
          </goals>
      </execution>
  </executions>
</plugin>
```

And that is Spring setup.  Now we will test the setup.

### Testing RESTful Service

**Create a new package called `hello`**.  We need three files - `Greeting.java`:

```java
package hello;

public class Greeting
{

    private final long id;
    private final String content;

    public Greeting(long id, String content)
    {
        this.id = id;
        this.content = content;
    }

    public long getId() {
        return id;
    }

    public String getContent() {
        return content;
    }
}
```

`GreetingController.java`:

```java
package hello;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController
{
    private long counter = 0;
    private static final String template = "Hello, %s!";

    @RequestMapping("/greeting")
    public Greeting greeting(@RequestParam(value="name", defaultValue="World") String name)
    {
        return new Greeting(counter++, String.format(template, name));
    }
}
```

This file specifies how our REST application will listen for requests.  In the `greeting` part of the URL (e.g., http://www.napier.ac.uk/greeting) a Greeting message will be returned with the message *Hello, <something>*.  If a `name` parameter is passed via the URL (e.g., /greeting?name=Kevin) the *<something>* will be that name.  Otherwise, *World* will be used.

Finally, `Application.java`:

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application
{
    public static void main(String[] args)
    {
        SpringApplication.run(Application.class, args);
    }
}
```

**Run the Application** and then go to the following URL: http://localhost:8080/greeting.  You will be returned the following JSON code:

```json
{"id":0,"content":"Hello, World!"}
```

Going to http://localhost:8080/greeting?name=Kevin will return the following:

```json
{"id":1,"content":"Hello, Kevin!"}
```

**Stop the application** otherwise it will continue running in the background servicing the requests.

### Converting the HR System to a RESTful App

Now that we have Spring setup we can convert our existing HR System to be restful.  This is quite an involved process, requiring you to update much across our existing code.

It might be useful to start up a database container to connect to now.  This is so we can test our application.

#### Updating `App`

First, we need to modify the declaration of `App` to include the necessary imports and to state that the application is a Spring one.  Modify the start of `App.java` to the following:

```java
package com.napier.sem;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.sql.*;
import java.util.ArrayList;

@SpringBootApplication
@RestController
public class App
```

We also have to change the declarations managing our connection to be `static`:

```java
    /**
     * Connection to MySQL database.
     */
    private static Connection con = null;

    /**
     * Connect to the MySQL database.
     */
    public static void connect(String location)
    {
        // Code as before.
    }

    /**
     * Disconnect from the MySQL database.
     */
    public static void disconnect()
    {
        // Code as before.
    }
```

These need to be `static` as we will no longer create an `App` object.  Next we update `main` to initialise the Spring application:

```java
    public static void main(String[] args)
    {
        // Connect to database
        if (args.length < 1)
        {
            connect("localhost:33060");
        }
        else
        {
            connect(args[0]);
        }

        SpringApplication.run(App.class, args);
    }
```

Notice we no longer call any methods.  This will be done via the URLs.

#### Updating Methods

Each method has to be updated to support a URL entry point.  For example, `getEmployee` we will convert to this:

```java
    /**
     * Get a single employee record.
     * @param ID emp_no of the employee record to get.
     * @return The record of the employee with emp_no or null if no employee exists.
     */
    @RequestMapping("employee")
    public Employee getEmployee(@RequestParam(value = "id") String ID)
```

We set the URL to `/employee`.  We set the name of the parameter to `id`.  We also change the type of the parameter to `String` as a URL only provides string data.  **You will need to modify any integration test that calls `getEmployee` to use a String also**.  You should be able to run the App and go to the following URL: http://localhost:8080/employee?id=10002.  This will return the following:

```json
{"emp_no":10002,"first_name":"Bezalel","last_name":"Simmel","title":null,"salary":0,"dept":null,"manager":null}
```

##### Exercise

Your task is to update all the methods to support REST interaction.  For information, you will need:

- `getAllSalaries` mapped to `salaries`.
- `getSalariesByTitle` mapped to `salaries_title` with `title` parameter.
- `getDepartment` mapped to `department` with `dept` parameter.
- `getSalariesByDepartment` mapped to `salaries_department` with `dept` parameter.

**Remember all parameters have to be `String`.** You will have to decide how to manage this.  My advice is to test each one as you implement them.

## Putting in a Web Front-end

Exposing our application to the entire Internet is not a good idea.  A better strategy is to put a web server in front of our application which will redirect calls.  We will do this via an Nginx web server (this was our first Docker container remember).  We will do this in two steps:

1. Simply forwarding on requests to our application.
2. Using some JavaScript to generate a table from our generated JSON.

### Nginx Web Server Set-up

First, **create a new folder/directory in your IntelliJ project called `web`**.  Then add the following `Dockerfile` to the `web` folder:

```dockerfile
FROM nginx
COPY content /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf
```

Now **add a new file to the folder called `nginx.conf`**:

```conf
events {
}

http {
    root /usr/share/nginx/html;
    server {
        listen 80;
        location /app/ {
            proxy_pass http://app:8080/;
        }
    }
}
```

This configuration tells Nginx the following:

- Our main website files will be in the folder `/usr/share/nginx/html`.  Note this is the same folder we will copy to in the Dockerfile.
- Nginx will listen on port 80 (the standard HTTP port).
- Any request coming into `/app/` (e.g., http://www.napier.ac.uk/app) will be forwarded to the address `http://app:8080`.  

Now add a new directory called `content` to the `web` directory, and add the following `index.html` file to it:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">

    <title>Title</title>

    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>

</head>
<body>
<div class="mypanel"></div>
</body>

<script>
    $.getJSON('http://time.jsontest.com', function(data) {

        var text = `Date: ${data.date}<br>
                    Time: ${data.time}<br>
                    Unix time: ${data.milliseconds_since_epoch}`


        $(".mypanel").html(text);
    });
</script>

</html>
```

Don't worry about the HTML.  We will add to it later, but you don't need to understand it.

### Some *Magic* JavaScript

The code here only works for arrays of JSON data (e.g., methods that return an `ArrayList`).  It will not work on a single element like an `Employee`.  You will have to write different code for that.

Now it is time for the last piece of the puzzle - turning the raw JSON data into a table.  To do this, create a new file `salaries_title.html` in the `content` folder for our web image.  The file contents are below:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Salaries by Title</title>

    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>

    <style>
        th, td, p, input
        {
            font:14px Verdana;
        }
        table, th, td
        {
            border: solid 1px #DDD;
            border-collapse: collapse;
            padding: 2px 3px;
            text-align: center;
        }
        th
        {
            font-weight: bold;
        }
    </style>
</head>
<body>
<div class="showData">Waiting</div>
</body>
<script>
    var urlParams = new URLSearchParams(window.location.search);
    var title = urlParams.get('title');

    var URL = "http://" + window.location.hostname + "/app/salaries_title?title=" + title;

    $.getJSON(URL, function(data) {
        // EXTRACT VALUE FOR HTML HEADER.
        var col = [];
        for (var i = 0; i < data.length; i++) {
            for (var key in data[i]) {
                if (col.indexOf(key) === -1) {
                    col.push(key);
                }
            }
        }

        // CREATE DYNAMIC TABLE.
        var table = document.createElement("table");

        // CREATE HTML TABLE HEADER ROW USING THE EXTRACTED HEADERS ABOVE.
        var tr = table.insertRow(-1);                   // TABLE ROW.
        for (var i = 0; i < col.length; i++) {
            var th = document.createElement("th");      // TABLE HEADER.
            th.innerHTML = col[i];
            tr.appendChild(th);
        }

        // ADD JSON DATA TO THE TABLE AS ROWS.
        for (var i = 0; i < data.length; i++) {
            tr = table.insertRow(-1);
            for (var j = 0; j < col.length; j++) {
                var tabCell = tr.insertCell(-1);
                tabCell.innerHTML = data[i][col[j]];
            }
        }

        // FINALLY ADD THE NEWLY CREATED TABLE WITH JSON DATA TO A CONTAINER.
        $(".showData").html(table);
    });
</script>
</html>
```

The key part is the `<script>` tag at the bottom:

- `var urlParams = new URLSearchParams(window.location.search);` gets any parameters (parts after the question mark) in the URL.  For example, `www.napier.ac.uk/salaries_title.html?title=Engineer&name=Kevin` would provide the `title=Engineer&name=Kevin` part.
- `var title = urlParams.get('title');` gets the `title` parameter from the URL.  In the example above, this is `Engineer`.
- `var URL = "http://" + window.location.hostname + "/app/salaries_title?title=" + title;` builds a URL from the current location `window.location.hostname`.  It then adds `/app/salaries_title?title=<title>` to the URL.  Because we are going to the `/app` we will get the JSON data.
- `$.getJSON(URL, ...` gets the JSON data from the URL we specified.  The rest of the code is the function called on completion of this call, which just builds a table.

**Run The Application.**  You should be able to access the following URL: `http://localhost/salaries_title.html?title=Engineer`.  It will produce a web page as follows:

![Web Table Output](img/web-table-output.PNG)


