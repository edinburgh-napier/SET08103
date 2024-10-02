# Lab 02: Continuous Integration Setup


In this lab we will automate our build process using [GitHub Actions] (https://github.com/features/actions). CI is [Continuous Integration](https://en.wikipedia.org/wiki/Continuous_integration), a software engineering method where we ensure our local development software versions are merged into the mainline code several times a day. There are several CI approaches available, but GitHub actions is easy to plug into our software production pipeline.

### Behavioural Objectives

After this lab you will be able to:

-   \[ \] **Pull your project** to *return to your previous development state.*

-   \[ \] **Add** *continuous integration to your project.*

-   \[ \] **Integrate** *Docker into CI build step.*

-   \[ \] **Define** the *Gitflow Workflow.*

-   \[ \] **Link** *Docker containers.*

-   \[ \] **Package** an *application JAR with IntelliJ.*

-   \[ \] **Create** a *release on GitHub.*

### Pulling Back Your Project

At the end of the last lab we had a working application that we could deploy to Docker. Everything was done using three files:

-   A **pom.xml** Maven build file, which we have not explored further yet.
    
-   An **App.java** code file that contains our current code which is just a *Hello World* example.
    
-   A **Dockerfile** that specifies how to run our application in a separate Docker container.

We have three other files in our repository:

-   A **.gitignore** file to tell Git which files and folders to ignore for versioning.
    
-   A **README.md** file for our project.

-   A **LICENSE** file defining the licensing terms for our project.

Everything is in our GitHub repository. We can pull this back in IntelliJ to start from where we left off. If your code is still on the machine you are using you can ignore this step.

### Starting IntelliJ

If you are using your own machine IntelliJ will open the last project that was opened. If not then you will need to clone the project from your GitHub Repository

![Graphical user interface, application, Teams Description automatically
generated](./img/image1.png)

IntelliJ Start Window

The button to click on is **Get from VCS**, then select **Git**:

![Graphical user interface, text, application Description automatically
generated](./img/image2.png)

IntelliJ Import from Git

If you have not saved login credentials you may be asked to provide these The simplest method is to click on **Login with GitHub** and add your details:

Note that GitHub now prefers a token for authentication rather than a password.

### Creating a GitHub token

Go to your GitHub account. From the menu at the top right select settings

![Graphical user interface, application Description automatically
generated](./img/image3.png)

On the left hand side select Developer Settings

![Graphical user interface, application Description automatically
generated](./img/image4.png)

On the next screen select Personal access tokens then Generate New Token

![Graphical user interface, text, application Description automatically
generated](./img/image5.png)

Tick the workflow option which will allow us access using our CI environment and at the bottom of the page select generate token

![Graphical user interface, text, application, email Description
automatically generated](./img/image6.png)

The Token that is generated is used instead of your password. Make sure you take a note of this as once you navigate away from the page you will no longer be able to retrieve it.

Next time you try to push code to your repository a GitHub login window will appear. Use the token instead of your password. Sometimes I find that this does not work the first time and I am prompted again from the IntelliJ terminal window. Entering the details again seems to fix the issue.

Now we need to check that everything works correctly. Perform the following steps:

1.  Build the project (**Build** then **Build Project**).

2.  Run the project locally (open **App.java** and click the **green triangle** next to **public class App** and select **Run App.main()**).
    
3.  Install Docker and the IntelliJ Docker plugin if needed (see [the last lab](../lab01/)).
    
4.  Run the project via Docker (open **Dockerfile** and click the **green triangles** at the top of the file and select **Run on Docker**).

Hopefully everything has worked and we are back to the point we left off at last week. **Remember these steps**. You will need to repeat them every time you pull back your project to a new local system.

Adding CI to Your Repository
----------------------------

We can now set-up GitHub Actions. On your GitHub repository select the actions tab at the top then select *set up workflow yourself*

![Graphical user interface, text, application, email Description
automatically generated](./img/image7.png)

This will create a file in your repository named 

`.github/workflows/main.yml`

Replace the default text with the following

```yml
name: A workflow for my Hello World App
on: push

jobs:
  build:
    name: Hello world action
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Set up JDK 11
        uses: actions/setup-java@v2
        with:
          java-version: '11'
          distribution: 'adopt'
      - name: Compile with Maven
        run: mvn compile
      - name: Build Docker Image
        run: docker build -t semimage .
      - name: Run image
        run: docker run --name semcontainer -d semimage
      - name: view logs
        run: docker logs semcontainer
```

To sync our local version do a pull from IntelliJ 

![1642967004914](img/1642967004914.png)

To check if our CI workflow is working make some changes and push them to GitHub

1. Add some text to your `Readme.md` file.
2. Add the updates to the commit.
3. Create a commit.  Use a sensible message.
4. Push the commit.

Now we can go to GitHub see if our build was successful.  

![1642967431174](img/1642967431174.png)

You can click on the **action** to show more details of the build.

![2022-01-23 19_52_58-Window](img/action1.png)

The stages shown above duplicate the stages in our workflow file that we defined above main.yml

### Adding Build Badge

You might have seen build status badges on GitHub before like this one:

![Build Status](img/build_passing.png)

You can add the badge for your build status to your `README.md` file as well.  To do this, add the following text to your `Readme.md` file:

```
![GitHub Workflow Status (branch)](https://img.shields.io/github/actions/workflow/status/<repository owner>/<repository>/<action file name>.yml?branch=<branch name>)
```

You will need to replace the parameter `<repository owner>`, `<repository>`, `<action file name>` and `<branch name>` with the correct values. 

 - replace `<repository owner>` with the GitHub username of the repository you are working on.
For these lab exercises this will be your own GitHub username, for the coursework, it will be the GitHub username of the group member owning your group's repository.
 - replace `<respository>` with the repository's name.
 - replace `<action file name>` with the name of the GitHub actions file that contains the build job you want to get the batch for.
 - replace `<branch name>` with the name of the branch you want to build batch for.

The batch for the `master` branch for the repository `sem` on my account `asteyven` will be as follows. 
```
![GitHub Workflow Status (branch)](https://img.shields.io/github/actions/workflow/status/asteyven/sem/main.yml?branch=master)
```


Now go through our Git update steps:

1. Add files to commit.
2. Create commit.
3. Push to GitHub.

Now if you go to your GitHub page you should see the following:

![2022-01-23 20_04_29-Window](img/commit1.png) 


And now we have our project automatically building on pushes to GitHub, and the current build status

### Other Badges

You can add various badges to your project.  [Shields.io](https://shields.io/) is one such site that provides badges.  We are going to add two to our `README.md`: one for our license and one for our release.  The license badge takes the URL:

`[![LICENSE](https://img.shields.io/github/license/<github-username>/<repository>.svg?style=flat-square)](https://github.com/<github-username>/<repository>/blob/master/LICENSE)`

Just replace `<github-username>` with your GitHub username.
Replace `<repository>` with your repository name.
The release badge is:

`[![Releases](https://img.shields.io/github/release/<github-username>/<repository>/all.svg?style=flat-square)](https://github.com/<github-username>/<repository>/releases)`

And then update your GitHub repository:

1. Add files to commit.
2. Create commit.
3. Push to GitHub.

If you go to your repository's dashboard in GitHub you should see your new badges.

![badges](img/badges.png)



## Setting up Gitflow Workflow

Our next step is to set-up our workflow.  This is our approach to managing separate features and collaborators in our project.  [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) is one such workflow that works well with Git.  Gitflow is examine further in [Lecture 02](../../lectures/lecture02).  To work with Gitflow we manage several branches:

- **master** which is the main Git branch.  This is created automatically when a Git repository is created.  Only main releases are tracked in this branch.
- **develop** is the integration branch.  Features are merged into this branch as they are completed. It is a *feature integration* branch.
- **feature** branches are where new features are worked on before integration with with `develop`.
- **release** is where releases are made.  A release is normally a collection of features, or a set point in time.  Note that a release must be a working version.  The `release` branch comes from a version of `develop`.  `master` is a version of `release`.
- **hotfix** branches are *maintenance* ones based on `master`.  We are fixing a production version of the code, so rather than working from `develop` we work from `master`.

### Develop Branch

The first step in setting up Gitflow is the creation of a `develop` branch in our project.  We can do this in IntelliJ.  Select **VCS**, **Git** then **Branches...** to open the branches window:

![IntelliJ Git Branches](img/intellij-git-branch.png)

Select **New Branch** and call the branch **develop**.  Make sure the **Checkout branch** checkbox is ticked.

The `develop` branch only exists on the local system.  To add it to GitHub we have to perform a push.  Do this now.  From IntelliJ, **VCS**, **Git** then **Push**. Click **Push** and the branch will be added to GitHub. You can confirm this on GitHub by opening the branches drop-down, refreshing the page if you are currently on it:

![GitHub Branches](img/github-branches.png)

#### Adding Develop Build Status to GitHub

GitHub will have automatically added this branch to its build.  As `develop` is a key branch of our project we will add this build status to the `README.md` file.  

This time I will use Shields.io to create a build badge. Go to [https://shields.io/category/build](https://shields.io/category/build) and select GitHub Workflow Status (branch). Fill in the UserName, RepositoryName, Workflow name (taken from the main.yml file)  and branch name

Update the `README.md` as below (keep the other badges):

```markdown
# Software Engineering Methods
![GitHub Workflow Status (branch)](https://img.shields.io/github/actions/workflow/status/<username>/<repository>/<action file name>.yml?branch=<branch>)
```

And add this to GitHub:

1. Add files to commit.
2. Create commit.
3. Push commit to GitHub.

And if you go to the dashboard for the repository on GitHub you will see that nothing has changed.  That is because we have pushed to our `develop` branch, not the `master` branch.  You can see the updates by switching to the `develop` branch on GitHub using the branches drop-down from earlier:

![Develop Branch Status on GitHub](img/github-develop-status.png)

The formatting of the above screenshot is achieved with the following markdown:

```markdown
# Software Engineering Methods
* Master Build Status ![GitHub Workflow Status (branch)](https://img.shields.io/github/actions/workflow/status/<username>/<repository>/<action file name>.yml?branch=<master branch>)
* Develop Branch Status ![GitHub Workflow Status (branch)](https://img.shields.io/github/actions/workflow/status/<username>/<repository>/<action file name>.yml?branch=<develop branch>)
* License [![LICENSE](https://img.shields.io/github/license/<username>/<repository>.svg?style=flat-square)](https://github.com/<username>/<repository>/blob/master/LICENSE)
* Release [![Releases](https://img.shields.io/github/release/<username>/<repository>/all.svg?style=flat-square)](https://github.com/<username>/<repository>/releases)
```
## Updating our Example Application

To end this lab we will add a new feature to our application - database support via [MongoDB](https://docs.mongodb.com/).  We will perform the following steps:

1. Start a MongoDB server via Docker.
2. Start a new feature in our project.
3. Add MongoDB support to our application.
4. Link our container with the MongoDB container.
5. Test that everything works.
6. Merge the feature into our `develop` branch.
7. Create a release.
8. Add a version.

This may seem like a lot of steps, but individually they are simple.  What could be seen as the hardest part - setting up a database and connecting to it - is simple in our build pipeline.

### Running a MongoDB Docker Image

Our first step is to start a new MongoDB container.  Let us do this via IntelliJ rather than the command line.

Open the Services panel at the bottom of IntelliJ and make sure **Images** is highlighted. The button on the top-left allows us to pull images for Docker.  Click this button to open the **Images Console** window:

![IntelliJ Docker Panel](img/intellij-docker-panel.png)

Type **mongo** press ctrl + Enter to start.  The latest version of MongoDB will now be pulled as an image. It will appear in the Docker panel of IntelliJ:

With `mongo:latest` selected, click the **plus sign** to **Create Container**.  This will open the following window:

Add the run options from the Modify Options Link

![IntelliJ Create Container](img/createMongo.png)

MongoDB is a server application which listens on port 27017.  We could just open that port, but just in case MongoDB is already running locally we will switch ports.  We looked at this in the last lab.  In the **Run Options** text box add **-p 27000:27017** as shown in the image.  Then click **Run**.  IntelliJ will start the container and it will be waiting for you to use.

### Starting a Feature Branch

We are going to add a new feature to our application.  To do this, we need to create a new branch as we did for `develop`.  The steps you need to undertake are:

1. Create a new branch called `feature/mongo-intergration` (**Git** then **New Branch**
2. Push the branch to GitHub.

That is it.  We are now working on a feature branch, which we created from our `develop` branch since it was the one we had checked out.

### Adding MongoDB Support to Our Application

We will use Maven to manage the import of MongoDB functionality into our application.  This is done by adding **dependencies** to our `pom.xml` file.  IntelliJ will recognise these dependencies and pull in the relevant libraries and functionality.  The new code we want is:

```xml
    <dependencies>
        <dependency>
            <groupId>org.mongodb</groupId>
            <artifactId>mongodb-driver</artifactId>
            <version>3.6.4</version>
        </dependency>
    </dependencies>
```

We add this to the `pom.xml` file as follows: 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.napier.sem</groupId>
    <artifactId>seMethods</artifactId>
    <version>0.1.0.1</version>
    
     <dependencies>
        <dependency>
            <groupId>org.mongodb</groupId>
            <artifactId>mongodb-driver</artifactId>
            <version>3.6.4</version>
        </dependency>
    </dependencies>

</project>
```

If IntelliJ does not automatically download the dependencies click on the refresh button on the maven panel:

![Refresh Maven](img/maven_refresh.png)


IntelliJ will manage everything for us.  Initially the test `org.mongodb` and `mongodb-driver` will be red, but once the import is complete it will turn black.  Let us to a commit.

1. Add files to the commit.
2. Create the commit.
3. Push the commit to GitHub.

Now we can test that we can talk to the MongoDB server.  We will update `App.java` to the following:

```java
package com.napier.sem;

import com.mongodb.MongoClient;
import com.mongodb.client.MongoDatabase;
import com.mongodb.client.MongoCollection;
import org.bson.Document;

public class App
{
    public static void main(String[] args)
    {
        // Connect to MongoDB on local system - we're using port 27000
        MongoClient mongoClient = new MongoClient("localhost", 27000);
        // Get a database - will create when we use it
        MongoDatabase database = mongoClient.getDatabase("mydb");
        // Get a collection from the database
        MongoCollection<Document> collection = database.getCollection("test");
        // Create a document to store
        Document doc = new Document("name", "Kevin Sim")
                .append("class", "Software Engineering Methods")
                .append("year", "2021")
                .append("result", new Document("CW", 95).append("EX", 85));
        // Add document to collection
        collection.insertOne(doc);

        // Check document in collection
        Document myDoc = collection.find().first();
        System.out.println(myDoc.toJson());
    }
}
```

Now all we have to do is run the application normally (i.e. not as a Docker container).  Select **Run** then **Run** and select **App** as the configuration.  Your application should launch, connect to the MongoDB server running in the Docker container and perform some basic operations as shown.  The console output will look something like the following:

```shell
"C:\Program Files\Java\jdk1.8.0_181\bin\java.exe" "-javaagent:C:\Program Files\JetBrains\IntelliJ IDEA 2020.3.2\lib\idea_rt.jar=51700:C:\Program Files\JetBrains\IntelliJ IDEA 2020.3.2\bin" -Dfile.encoding=UTF-8 -classpath "C:\Program Files\Java\jdk1.8.0_181\jre\lib\charsets.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\deploy.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\access-bridge-64.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\cldrdata.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\dnsns.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\jaccess.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\jfxrt.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\localedata.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\nashorn.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\sunec.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\sunjce_provider.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\sunmscapi.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\sunpkcs11.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\ext\zipfs.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\javaws.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\jce.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\jfr.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\jfxswt.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\jsse.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\management-agent.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\plugin.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\resources.jar;C:\Program Files\Java\jdk1.8.0_181\jre\lib\rt.jar;C:\Users\KevL\Desktop\SEM_Demo\target\classes;C:\Users\KevL\.m2\repository\org\mongodb\mongodb-driver\3.6.4\mongodb-driver-3.6.4.jar;C:\Users\KevL\.m2\repository\org\mongodb\bson\3.6.4\bson-3.6.4.jar;C:\Users\KevL\.m2\repository\org\mongodb\mongodb-driver-core\3.6.4\mongodb-driver-core-3.6.4.jar" com.napier.sem.App
Feb 02, 2021 11:41:28 AM com.mongodb.diagnostics.logging.JULLogger log
INFO: Cluster created with settings {hosts=[localhost:27000], mode=SINGLE, requiredClusterType=UNKNOWN, serverSelectionTimeout='30000 ms', maxWaitQueueSize=500}
Feb 02, 2021 11:41:28 AM com.mongodb.diagnostics.logging.JULLogger log
INFO: Cluster description not yet available. Waiting for 30000 ms before timing out
Feb 02, 2021 11:41:28 AM com.mongodb.diagnostics.logging.JULLogger log
INFO: Opened connection [connectionId{localValue:1, serverValue:1}] to localhost:27000
Feb 02, 2021 11:41:28 AM com.mongodb.diagnostics.logging.JULLogger log
INFO: Monitor thread successfully connected to server with description ServerDescription{address=localhost:27000, type=STANDALONE, state=CONNECTED, ok=true, version=ServerVersion{versionList=[4, 4, 3]}, minWireVersion=0, maxWireVersion=9, maxDocumentSize=16777216, logicalSessionTimeoutMinutes=30, roundTripTimeNanos=4243600}
Feb 02, 2021 11:41:28 AM com.mongodb.diagnostics.logging.JULLogger log
INFO: Opened connection [connectionId{localValue:2, serverValue:2}] to localhost:27000
{ "_id" : { "$oid" : "60193a68517ac5004c914c07" }, "name" : "Kevin Sim", "class" : "Software Engineering Methods", "year" : "2021", "result" : { "CW" : 95, "EX" : 85 } }

Process finished with exit code 0

```

If you look at the logs of the MongoDB container in IntelliJ you will see the something similar to the following partial log:

```shell

{"t":{"$date":"2021-02-02T11:41:28.619+00:00"},"s":"I",  "c":"NETWORK",  "id":22943,   "ctx":"listener","msg":"Connection accepted","attr":{"remote":"172.17.0.1:58692","connectionId":2,"connectionCount":2}}
{"t":{"$date":"2021-02-02T11:41:28.619+00:00"},"s":"I",  "c":"NETWORK",  "id":51800,   "ctx":"conn2","msg":"client metadata","attr":{"remote":"172.17.0.1:58692","client":"conn2","doc":{"driver":{"name":"mongo-java-driver","version":"3.6.4"},"os":{"type":"Windows","name":"Windows 10","architecture":"amd64","version":"10.0"},"platform":"Java/Oracle Corporation/1.8.0_181-b13"}}}
{"t":{"$date":"2021-02-02T11:41:28.639+00:00"},"s":"I",  "c":"STORAGE",  "id":20320,   "ctx":"conn2","msg":"createCollection","attr":{"namespace":"mydb.test","uuidDisposition":"generated","uuid":{"uuid":{"$uuid":"b862f932-c8aa-4b30-904b-0c3a1e6a1dd2"}},"options":{}}}
{"t":{"$date":"2021-02-02T11:41:28.658+00:00"},"s":"I",  "c":"INDEX",    "id":20345,   "ctx":"conn2","msg":"Index build: done building","attr":{"buildUUID":null,"namespace":"mydb.test","index":"_id_","commitTimestamp":{"$timestamp":{"t":0,"i":0}}}}
{"t":{"$date":"2021-02-02T11:41:29.035+00:00"},"s":"I",  "c":"NETWORK",  "id":22944,   "ctx":"conn2","msg":"Connection ended","attr":{"remote":"172.17.0.1:58692","connectionId":2,"connectionCount":0}}
{"t":{"$date":"2021-02-02T11:41:29.035+00:00"},"s":"I",  "c":"NETWORK",  "id":22944,   "ctx":"conn1","msg":"Connection ended","attr":{"remote":"172.17.0.1:58688","connectionId":1,"connectionCount":1}}

```

A good time to push this update to GitHub.

1. Add files to commit.
2. Create commit.
3. Push to GitHub.

Now we need to modify our application so that it runs in our Docker containers.

### Linking Containers

Linking containers requires container discovery.  There are a few ways to do this, but we will use the simplest.  Docker networking and container discovery is an entire subject in itself, and outside the scope of this module.

We are going to undertake the following steps:

1. Create a self-contained JAR for our project - this will include any external libraries.
2. Add a network bridge to docker.
3. Update our code files, Dockerfile, and MongoDB instance.
4. Update GitHub Actions build file.

#### Creating a Self-contained JAR

So far we have not been doing good practice.  For Java, JAR (Java ARchive) files should be deployed and not individual code files as we have been doing.  The advantage of a JAR file is it can contain library dependencies, such as the MongoDB one we have added.  Maven can build this for us automatically.

First we must update our `pom.xml` file.  Add the following below the `dependencies` section:

```xml
    <properties>
        <maven.compiler.source>10</maven.compiler.source>
        <maven.compiler.target>10</maven.compiler.target>
    </properties>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-assembly-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>com.napier.sem.App</mainClass>
                        </manifest>
                    </archive>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
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
        </plugins>
    </build>
```

We have added two new sections:

1. `properties` - here we are telling Maven to produce Java 8 code (1.8).
2. `build` - there is quite a bit going on here.  You can happily reuse the code though:
    - We are defining how Maven assembles the JAR file.
    - We are telling Maven which class to run when the JAR is executed (`mainClass`).
    - We are telling Maven to build the `jar-with-dependencies` - in other words pull in the MongoDB code.

First rebuild your project so that everything is up to date: **Build** then **Build Project**. We can now ask Maven to package up our application.  In IntelliJ open the **Maven Panel** on the right hand side:

![IntelliJ with Maven Panel Open](img/intellij-maven-panel.png)

Open the **Lifecycle** collapsed menu, and select **package** and click the **green triangle in the Maven panel** to start the package process.  This will take a few seconds as your code and the external JAR libraries are combined into a single JAR.  You will see this in the **target** folder in the **Project Structure** as `seMethods-0.1.0.1-jar-with-dependencies.jar`.  So we have successfully built our project into a single JAR for deployment.  Time to push to GitHub.

1. Add files to commit.
2. Create commit.
3. Push to GitHub.

#### Creating a Network Bridge in Docker

In Docker, containers can discover each other by name if they are on the same Docker network bridge which is not the default one.  Therefore, we need to create a new bridge for our applications to talk on.  This is actually quite easy.  Run the following command from the command line:

```shell
docker network create --driver bridge se-methods
```

This will have created a new network called `se-methods`.  We can use this network to connect our main application to our MongoDB server.  Let us do this now.

#### Updating Our System

First we need to stop our current MongoDB server.  In IntelliJ you should be able to see this in the Services panel under **Containers**.  To stop it, select the container and **click** the **red stop button** on the left:

![IntelliJ Docker Container List](img/containers.png)

Once stopped, **right-click** on the container, and select **Delete Container** and then **Yes** in the prompt.

We need to create a new MongoDB server that uses our network infrastructure, and we also want to define the name of the server.  We do this by creating a new container from `mongo:latest` in IntelliJ using the following parameters:

![MongoDB Container Settings](img/dockerconfig.png)

Click **Run** and the container will start.  Next we need to update our main application so it can talk to this MongoDB server.  The only line that needs updating is the `MongoClient` creation one:

```java
// Connect to MongoDB
MongoClient mongoClient = new MongoClient("mongo-dbserver");
```

We are now explicitly connecting to the server called `mongo-dbserver`, which is the name we gave to our MongoDB container.  To test this, we need to update our Dockerfile:

```docker
FROM openjdk:latest
COPY ./target/seMethods-0.1.0.1-jar-with-dependencies.jar /tmp
WORKDIR /tmp
ENTRYPOINT ["java", "-jar", "seMethods-0.1.0.1-jar-with-dependencies.jar"]
```

We have changed what we are copying to the JAR file that has been created.  We are also changing our entry point to execute this JAR.  We need to first update our jar file, rebuild the docker image and restart the container.

- Delete the target directory
- Run Maven Package to recreate the jar file
- Build the Docker Image (You can do this by clicking the **green triangles** in the Dockerfile and selecting **Build Image for Dockerfile**.  )
- The image will be created and added to the list of images in IntelliJ's Docker Panel, near the bottom with an `sha256` name.  If you have more than one of these because of previous builds, delete all the `sha256` images and rebuild to have only one.

To test our new image, select **Create Container** with it selected and use the following properties:

![Application Docker Settings](img/dockerconfig1.png)

Click **Run** and our container will start our application which will connect to the MongoDB server and exit.  If successful you will see output on the container logs

![Container Logs](img/containerlogs.png)


Time for an update to GitHub. 

1. Add files to commit.
2. Create commit.
3. Push to GitHub.

#### Updating GitHub Actions

Now to put this into our GitHub Actions file:

```yml
name: A workflow for my Hello World App
on: push

jobs:
  build:
    name: Hello world action
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Set up JDK 11
        uses: actions/setup-java@v2
        with:
          java-version: '11'
          distribution: 'adopt'
      - name: Setup network
        run: |
          docker network create --driver bridge se-methods
          docker pull mongo
          docker run -d --name mongo-dbserver --network se-methods mongo
      - name: Build with Maven
        run: mvn package
      - name: Build
        run: docker build -t se_methods .
      - name: Run image
        run: docker run --network se-methods --name semcontainer se_methods
      - name: view logs
        run: docker logs semcontainer

```

We have a few more Docker commands but these are just the ones we added via IntelliJ or otherwise.  Now push this to GitHub:

1. Add files to commit.
2. Create commit.
3. Push to GitHub.

Check with GitHub Actions and ensure that not only is the project building but that it successfully runs in Docker.  You will need to open some of the code folds to verify.

### Merging Feature

It's time to merge our feature back into the `develop` branch.  Before doing this, we need to check that no changes have occurred in `develop`.  Although we know there hasn't been, we need to get into the habit of managing the workflow.

To `merge` any changes in the `develop` branch onto our feature branch, select **Git**, then **Branches...**.  This will open up the **Branches Popup**:

![IntelliJ Branches Popup](img/intellij-merge-branch.png)

Select the `orign/develop` branch (this is the one in GitHub) and then select **Merge into Current**.  You might spot a little pop-up at the bottom stating **Already Up-to-date** which means we can proceed and merge our feature back into `develop`.

Now we just need to switch back to the `develop` branch.  Open the **Branches Popup** again, and select the `develop` branch in **Local Branches** and select **Checkout**:

![IntelliJ Switch Branch](img/intellij-switch-branch.png)

And now `merge` the `feature/mongo-integration` branch in the **Local Branches** into the current branch as before.  This will complete the feature.  We just need to `push` this change into GitHub.  It is just a `push` as all the feature changes have been added to `develop`.

### Creating a Release

Now we need to create a release.  First, **create a new release branch**.  Follow the instructions as before.

We are going to call this release `0.1.0.2` (`0.1-alpha-2`).  You will need to change the following files to reflect this:

- `pom.xml` - the `version` tag.
- `Dockerfile` - the `COPY` and `ENTRYPOINT` values need updated with the new JAR file name.

Once you've done that, test that everything still works locally.  This involves:

1. Rebuilding the project.
2. Telling Maven to package the project.
3. Building the Docker image.
4. Running the Docker image.

If everything goes well, push the changes to GitHub:

1. Add files to commit.
2. Create commit.
3. Push to GitHub.

Now we need to `merge` this release back into `master`.  The steps you need to take are:

1. **Checkout** `master`.
2. **Merge** `release` onto `master`.
3. **Push** to GitHub.

#### Version Tags

Git commits can also be tagged.  To do this in IntelliJ, select **Git** then **New Tag** to open the **Tag** window:

![intelliJ Create Tag](img/intellij-git-tag.png)

Use the name provided, and click **Create Tag**.  Now we just need to `push` the tag to GitHub.  Select push, but this time ensure the **Push Tags** checkbox is ticked as indicated:

![IntelliJ Push Tag](img/intellij-push-tag.png)

Click **Push** and your tag will be added to GitHub.

#### GitHub Release

Now to create a release on GitHub.  Go to the GitHub page for your project and select the **Releases** tab to open the following window:

![GitHub Releases](img/new_release1.png)

Click **Create new release** to start entering the release details:

The details we want are below:

![GitHub First Release](img/github-first-release.png)

Also make sure the checkbox **This is a pre-release** is ticked.  Then click **Publish release**.  Your release details will then be presented:

![GitHub Release Created](img/github-release-created.png)

And if you go back to the main GitHub repository page you will find that your badges have been updated:

![GitHub Updated Badges](img/github-badges.png)

All you need to do now is merge the `release` branch back into `develop`:

1. Checkout `develop`.
2. Merge `release` into `develop`.
3. Push `develop`.

And we are done.  We have done a lot, but still not much code.  We have built our development pipeline, defined our workflow, and integrated a database along the way.  Not bad work.

### Clearing Up

Before stepping away from your machine there are some things you should do:

1. Stop any running Docker containers.
2. Delete any unneeded containers.
3. Delete any unneeded images.
4. Ensure any changes have been pushed.

With that done, you can happily walk away from the machine.

### Our Current Process

This is our current workflow.  This is an important set of steps so document them:

1. Pull the latest `develop` branch.
2. Start a new feature branch.
3. Once feature is finished, create JAR file.
4. Update and test Docker configuration with GitHub Actions.
5. Update feature branch with `develop` to ensure feature is up-to-date.
6. Check feature branch still works.
7. Merge feature branch into `develop`.
8. Repeat 2-7 until release is ready.
9. Merge `develop` branch into `release` and create release.
10. Merge `release` into `master` and `develop`.
