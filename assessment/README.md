# Coursework Assessment Details

## Coursework Proforma

| | |
| --- | --- |
| Module number | SET08103 |
| Module title | Software Engineering Methods |
| Module leader | Andreas Steyven |
| Tutor with responsibility for this Assessment. Student's first point of contact. | As above. |
| Assessment | Project |
| Weighting | 60% of module assessment |
| Size and/or time limits for assessment | See description below. |
| Deadline of submission | Your attention is drawn to the penalties for late submissions.  See details below. |
| Arrangements for submission | Coursework to be submitted via Moodle and GitHub. |
| Assessment Regulations | All assessments are subject to the University Regulations |
| The requirements for the assessment | See below. |
| Special instructions | N/A |
| Return of work and feedback | Face-to-face via code reviews and via Moodle. |
| Assessment criteria | See below. |

## Coursework Specification

You will work on the project as a Scrum team.  Details on Scrum are provided in [Unit 01 (b)](../units/unit01/unit01b.md), including an FAQ on how to apply Scrum in the module.

You work for an organisation that requires reporting on population information.  You have been tasked with designing and implementing a new system to allow easy access to this population information.  The organisation has provided you with an SQL database to work from available [here](https://downloads.mysql.com/docs/world-db.zip).

The organisation has asked for the following reports to be generated:

- All the countries in the world organised by largest population to smallest.
- All the countries in a continent organised by largest population to smallest.
- All the countries in a region organised by largest population to smallest.
- The top `N` populated countries in the world where `N` is provided by the user.
- The top `N` populated countries in a continent where `N` is provided by the user.
- The top `N` populated countries in a region where `N` is provided by the user.
- All the cities in the world organised by largest population to smallest.
- All the cities in a continent organised by largest population to smallest.
- All the cities in a region organised by largest population to smallest.
- All the cities in a country organised by largest population to smallest.
- All the cities in a district organised by largest population to smallest.
- The top `N` populated cities in the world where `N` is provided by the user.
- The top `N` populated cities in a continent where `N` is provided by the user.
- The top `N` populated cities in a region where `N` is provided by the user.
- The top `N` populated cities in a country where `N` is provided by the user.
- The top `N` populated cities in a district where `N` is provided by the user.
- All the capital cities in the world organised by largest population to smallest.
- All the capital cities in a continent organised by largest population to smallest.
- All the capital cities in a region organised by largest to smallest.
- The top `N` populated capital cities in the world  where `N` is provided by the user.
- The top `N` populated capital cities in a continent where `N` is provided by the user.
- The top `N` populated capital cities in a region where `N` is provided by the user.
- The population of people, people living in cities, and people not living in cities in each continent.
- The population of people, people living in cities, and people not living in cities in each region.
- The population of people, people living in cities, and people not living in cities in each country.

Additionally, the following information should be accessible to the organisation:

- The population of the world.
- The population of a continent.
- The population of a region.
- The population of a country.
- The population of a district.
- The population of a city.

Finally, the organisation has asked if it is possible to provide the number of people who speak the following the following languages from greatest number to smallest, including the percentage of the world population:

- Chinese.
- English.
- Hindi.
- Spanish.
- Arabic.

### Country Report

A country report requires the following columns:

- Code.
- Name.
- Continent.
- Region.
- Population.
- Capital.

### City Report

A city report requires the following columns:

- Name.
- Country.
- District.
- Population.

### Capital City Report

A capital city report requires the following columns:

- Name.
- Country.
- Population.

### Population Report

For the population reports, the following information is requested:

- The name of the continent/region/country.
- The total population of the continent/region/country.
- The total population of the continent/region/country living in cities (including a %).
- The total population of the continent/region/country not living in cities (including a %).

## Group Submission

The coursework **must** be delivered by a group.  The aim of the module is to assess your ability to work as a team to deliver a product.  Therefore, the majority of your coursework grade will be based on your team's ability to work together using the methods defined in the module.

The submission is monitored during lab stand-up meetings, and formally via the 4 assessment points.  Your submissions are delivered via your GitHub repository. The master branch of your GitHub repository should also be submitted to Moodle at each assessment point along with a spreadsheet in Excel format (csv, xls or xlsx) detailing Individual team members' contributions.

## Individual Assessment

Individual contributions to the team will be assessed by your peers and the module teaching team based on attendance at the various meetings and individual contributions towards each code review, and via the metrics gathered from tools such as GitHub.  **Individual contributions will lead to a scaling of the overall coursework grade if the module team have evidence that illustrates a lack of contribution to the team deliverable.**

#### Groups must maintain a spreadsheet detailing individual team members contribution at each of the 4 assessment points

We wish to determine the individual contribution to the team project.  To do this, the team have to submit a single spreadsheet to Moodle defining the agreed contribution of each team member to the individual delivery points.  This should be submitted in percentages with the total sum of individual contributions adding up to 100% at each point of assessment.  For example:

| Matriculation Number | Code Review 1 | Code Review 2 | Code Review 3 | Code Review 4 |
|----------------------|---------------|---------------|-------------|-------|
| 4000xxxx             | 25            | 50            | 0           | 25    |
| 4000xxxx             | 25            | 50            | 50          | 25    |
| 4000xxxx             | 25            | 0             | 50          | 25    |
| 4000xxxx             | 25            | 0             | 0           | 25    |
| **Total**            | **100**       | **100**       | **100**     | **100** |
The team need to agree these scores.  **If the team cannot agree, or a team member believes the spreadsheet submitted does not represent the actual contributions, then contact a member of the teaching team.**  In these circumstances, the metrics and other information provided on GitHub will be used.

The data supplied in this spreadsheet will be used to weight each team members final mark for each assessment point.

For example, if the group received 18 out of 20 for code review one then all 4 members would get the full 18 marks as all had contributed equally. For code review 2 Members 1 & 2 would get the group mark but Members 3 & 4 would receive zero

## Disciplinary Procedures

The coursework **must** be delivered as part of a team. **If anyone is dismissed from their team this means they cannot deliver the coursework and will fail.**  Dismissal from a team involved the following process:

- An individual is evidenced as breaching the code of conduct as set-out by the student team.
- Evidence is presented at the next available meeting with a member of the module delivery team.
- The individual evidenced will have the opportunity to evidence mitigating circumstances either to the student team or privately to a member of the module delivery team.
- The module delivery team retains the right to the final decision of whether the dismissal is warranted.

Any dismissed team member has a week to appeal the decision to the module team with suitable evidence provided.


## Code Review Meetings

Each group will undertake **four** graded code reviews:

Note that Teaching starts in Week 2. Week Commencing 16th of February

1. Week 5 (commencing 07/10/2024) Code Review 1 (10% of CW mark).
2. Week 9 (commencing 04/11/2024) Code Review 2 (30% of CW mark).
3. Week 11 (commencing 18/11/2024) Code Review 3 (30% of CW mark). 
4. Week 13 (commencing 02/12/2024) Code Review 4 (30% of CW mark).

The code reviews will take place during your usual Lab sessions.  Each group will be given **10 minutes maximum** for the code review.  Your group will be **allocated a time for the code review**.  The details of the individual review points are below.  These meetings **must be attended** at the **stated time**.  Guidelines for grading the group:

- **Being late** for the meeting or **not being ready** when the meeting starts will result in the grade for that review being capped at 40%.
- **Not attending** the meeting will mean the code review will be marked at 0%.

**All team members** should attend the code review, however commitments and other considerations will be taken into account.  **Individuals attendance at reviews will be monitored** to ensure the team is contributing collectively to the project.

**Being ready** means that you are ready to present the points for the code review.  This means that you have a computer with the various tools logged into (e.g., GitHub, etc.) and a building version of the application in IntelliJ.

### Code Review 1

The aim of this code review meeting is to check that the project workflow is set up for the team.  You may choose to meet some of the feature requirements during this review point, but it is not as necessary.

#### Checklist Submission 1 (8% of CW mark)

The following must be in place:

-  GitHub project for coursework set-up.
-  Product Backlog created.
-  Project builds to self-contained JAR with Maven.
-  Dockerfile for project set-up and works.
-  GitHub Actions for project set-up and build is working using JAR, and Docker on GitHub Actions.
-  Correct branches for GitFlow workflow created - includes `master`, `develop`, and `release` branches.
-  First release created on GitHub.
-  Code of Conduct defined.

#### Graded Criteria Submission 1 (2% of CW mark)

The following criteria will be assessed for overall quality:

- Metrics from GitHub.  Also used to assess individual contribution.
- Code quality including comments.

### Code Review 2

The aim of this code review is to check that task management is set-up and that the initial requirements gathering has taken place via user stories and use cases.  You should have completed at least 33% of the work for the project at this point based on your own estimates.

#### Checklist Submission 2 (18% of CW mark)

The following must be in place:

-  Issues being used on GitHub.
-  Tasks defined as user stories.
-  Project integrated with Zube.io.
-  Kanban/Project Board being used.
-  Sprint Boards being used.
-  Full use cases defined.
-  Use case diagram created.
-  Suitable unit tests defined.
-  Tests running on GitHub Actions.

#### Graded Criteria Submission 2 (12% of CW mark)

The following criteria will be assessed for overall quality:

- Metrics from GitHub.  Also used to assess individual contribution.
- Code quality including comments.
- Correct usage of branches (following workflow and only change, add and remove files in `feature` branches).
- Continuous integration working.
- Use cases well-defined.
- Project requirements met.

### Code Review 3

The aim of this code review is to check that testing has been correctly specified.  At this stage, at least 66% of the work of the project should be completed.

#### Checklist Submission 3 (14% of CW mark)

The following must be in place:

-  Suitable integration tests defined.
-  Tests running on GitHub Actions.

#### Graded Criteria Submission 3 (16% of CW mark)

The following criteria will be assessed for overall quality:

- Metrics from GitHub.  Also used to assess individual contribution.
- Code quality including comments.
- Correct usage of branches (following workflow and only change, add and remove files in `feature` branches).
- Continuous integration working.
- Kanban/Project Board being used.
- Quality and coverage of unit-tests.
- Project requirements met.

### Code Review 4

The aim of this code review is to check that the project is deploying correctly.  At this stage, 100% of the work of the project should be completed.

#### Checklist Submission 4 (14% of CW mark)

The following must be in place:

-  Deployment working.
-  Bug reporting system set-up.

#### Graded Criteria Submission 4 (16% of CW mark)

The following criteria will be assessed for overall quality:

- Metrics from GitHub.  Also used to assess individual contribution.
- Code quality including comments.
- Correct usage of branches (following workflow and only change, add and remove files in `feature` or `bugfix` branches).
- Continuous integration working.
- Kanban/Project Board being used.
- Quality and coverage of unit tests.
- Project requirements met.
