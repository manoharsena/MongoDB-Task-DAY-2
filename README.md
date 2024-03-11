# MongoDB Task Day - 2 

`This Repository contains a Document file.`  

## <h2 align="left">Programming Language Used :</h2>

<div align="left">
  <img src="https://img.shields.io/badge/MongoDB-%234ea94b.svg?style=for-the-badge&logo=mongodb&logoColor=white" height="40" alt="mongod logo"  />
  <img width="40" />
  </div>


## <h2 align="left">Programming Tool Used :</h2>

<div align="left">
  <img src="https://www.svgrepo.com/show/303232/mongodb-logo.svg" height="100" alt="mongodb logo"  />
  <img width="50" />
  </div>

## Execution of 6 Questions :

**Executed Queries for each Questions**

1. Find all the topics and tasks which are thought in the month of October 

```bash
        db.topics.aggregate([
    {
        $lookup: {
            from: "tasks",
            localField: "topicid",
            foreignField: "topicid",
            as: "taskinfo"
        }
    },
    {
        $match: {
            $and: [
                { topic_date: { $gte: new Date("2020-10-01"), $lt: new Date("2020-11-01") } },
                {
                    $or: [
                        { "taskinfo.due_date": { $gte: new Date("2020-10-01"), $lt: new Date("2020-11-01") } },
                        { "taskinfo.due_date": { $exists: false } }
                    ]
                }
            ]
        }
    },
    {
        $project: {
            _id: 0,
            topicid: 1,
            topic: 1,
            topic_date: 1,
            tasks: "$taskinfo.task",
            due_dates: "$taskinfo.due_date"
        }
    }]);

```

2. Find all the company drives which appeared between 15 oct-2020 and 31-oct-2020 

```bash
db.companydrives.find({
    $or: [
        { drive_date: { $gte: new Date("15-oct-2020") } },
        { drive_date: { $lte: new Date("31-oct-2020") } }
    ]
});

```

3. Find all the company drives and students who are appeared for the placement

```bash
db.codekata.aggregate([
    {
        $lookup: {
            from: "users",
            localField: "userid",
            foreignField: "userid",
            as: "userinfo"
        }
    },
    {
        $group: {
            _id: {
                userid: "$userid",
                username: "$userinfo.name"
            },
            total_problems_solved: { $sum: "$problems" }
        }
    },
    {
        $project: {
            _id: 0,
            userid: "$_id.userid",
            username: "$_id.username",
            total_problems_solved: 1
        }
    }
]);
```

4. Find the number of problems solved by the user in codekata

```bash
db.codekata.aggregate([
    {
        $lookup: {
               from: "users",
               localField: "userid",
               foreignField: "userid",
               as: "userinfo"
             }
    },
    {
        $project:{
            _id:0,userid:1,problems:1,"userinfo.name":1
        }
    }
    ]);
```

5. Find all the mentors with who has the mentee's count more than 15

```bash
db.mentors.aggregate([{$match:{mentee_count:{$gt:15}}}]);

```

6. Find the number of users who are absent and task is not submitted  between 15 oct-2020 and 31-oct-2020

```bash
db.attendance.aggregate([
    {
        $lookup: {
            from: "topics",
            localField: "topicid",
            foreignField: "topicid",
            as: "topics"
        }
    },
    {
        $lookup: {
            from: "tasks",
            localField: "topicid",
            foreignField: "topicid",
            as: "tasks"
        }
    },
    {
        $match: {
            attended: false,
            "tasks.submitted": false,
            $and: [
                { "topics.topic_date": { $gte: new Date("15-oct-2020") } },
                { "topics.topic_date": { $lte: new Date("31-oct-2020") } },
                { "tasks.due_date": { $gte: new Date("15-oct-2020") } },
                { "tasks.due_date": { $lte: new Date("31-oct-2020") } }
            ]
        }
    }]);
```