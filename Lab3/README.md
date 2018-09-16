# Laboratory No.2


### Objectives

#### Database Creation and Maintenance Tools

### Tasks

1. Create a database in `MyDocuments/Data` with 16MB growth of the primary file and 128MB limit, 64MB growth and 1024MB limit of the log file. For other files, create a new `Filegroup`, and limit the growth from 64MB to 1024MB.

![ex1](https://user-images.githubusercontent.com/22482507/45377107-47777100-b602-11e8-9a98-a8d735a2471a.JPG)

2. Create a database with the log file physically placed in `MyDocuments/Log` and with different names physically and logically. It also should be compatible with the newest MS SQL Server 2017 and restricted to a single user at a time.

![ex2](https://user-images.githubusercontent.com/22482507/45377108-47777100-b602-11e8-921b-2dc2a1b92d6e.JPG)

![ex2](https://user-images.githubusercontent.com/22482507/45377109-48100780-b602-11e8-8c57-598d1b25b1d2.JPG)

3. Create a maintenance plan for the 1st task, with a shrink attribute if the db overweights 2000MB, the schedule for the plan should be every Friday on 12AM.

![ex3](https://user-images.githubusercontent.com/22482507/45377110-48100780-b602-11e8-9520-3f9404637625.JPG)

4. Create a maintenance plan for the 2nd task, with `rebuild index` and `Clean up History` checks, every first sunday every month.

![ex4](https://user-images.githubusercontent.com/22482507/45377112-48100780-b602-11e8-830b-db1f34d808f4.JPG)
