# Custora's Job Queue Game #

At Custora, we process a lot of data, calculating complex statistics from it as we go.
Those statistics need to be calculated on demand, coming in an irregular pattern as users
access the site, and they are also very heavy computations that can sometimes take a long
time to compute.

To handle this system, we have a job queue that can add and remove machines dynamically
based on demand. We want to process the jobs as quickly as possible, but each machine
incurs a cost based on how long it runs, so we try to optimize for both speed and expense.
The goal of this game is to process an imaginary queue of jobs as quickly and with as little
cost as possible.

## The Basics ##

We've set up an API that you can access to get all of the data you need for your game. It is
a turn-based game where each turn includes a batch of jobs that need to be processed. For
each turn, you will get the batch of jobs, create or remove machines as necessary, and assign
jobs to the machines for processing. Here are some basic ground rules:

1. There are 500 turns. Each turn contains between 1 and 40 jobs, for a total of 7537 jobs.

2. Each job requires a fixed amount of memory and a fixed number of turns to complete.

3. You can create machines whenever you want, and they are immediately available for jobs.

4. Each machine has 64GB of memory. If you assign greater than 64GB of jobs to a machine, the jobs will go on a queue for that machine and will be processed as memory is freed up in the order that they were received.

5. You can delete a machine whenever you want, but you will pay for it until all of its jobs, including the ones in its queue, have finished processing.

## Scoring ##

Each machine costs $1 per turn to operate. The goal is to process the jobs as quickly as
possible while incurring the minimum possible cost. At the end of the game, you will receive
a score from 0 - 100 for each of those two factors as well as a total score, which is the
sum of the two.

## API Endpoints ##

The game is operated for a REST-ful API with the following endpoints:

1. Create a new game, which is the first thing you'll need to do in each run of your program.
This requires no parameters and returns some basic data about the game, including its id.

    ```
    POST http://job-queue-dev.elasticbeanstalk.com/games

    {
      "id":80, "cost":0, "current_turn":0, "completed":false,
      "created_at":"2015-04-02T12:46:24.024Z", "updated_at":"2015-04-02T12:46:24.024Z",
      "jobs_completed":0, "total_score":null, "cost_score":null, "time_score":null
    }
    ```

2. Get data about a game. This returns data in the same form as the create game call above.

    ```
    GET http://job-queue-dev.elasticbeanstalk.com/games/{game_id}
    ```

3. Advance to the next turn in the game. This will move you to the next turn, cycle out
jobs that have completed, and add in cost for the machines you have running. It returns
data about the jobs in the current turn as well as the state of the game.

    ```
    GET http://job-queue-dev.elasticbeanstalk.com/games/{game_id}/next_turn

    {
      "jobs":[
        {"id":32912,"turn":1,"turns_required":6,"memory_required":3}
      ],
      "status":"active", "machines_running":0, "jobs_running":0,
      "jobs_queued":0,"jobs_completed":0,"current_turn":1
    }
    ```

4. Create a new machine.

    ```
    POST http://job-queue-dev.elasticbeanstalk.com/games/{game_id}/machines

    {
      "id":457,"game_id":80,"terminated":null
    }
    ```

5. Schedule a machine for termination.

    ```
    DELETE http://job-queue-dev.elasticbeanstalk.com/games/{game_id}/machines/{machine_id}

    {
      "id":457,
      "game_id":80,
      "terminated":true
    }
    ```

6. Assign jobs to a machine. This requires that you pass a job_ids parameter in the body that contains a JSON-encoded array of job ids to assign. It returns the number of jobs that were placed in the queue and added to the running list.

    ```
    POST http://job-queue-dev.elasticbeanstalk.com/games/{game_id}/machines/{machine_id}/job_assignments

    Request body: { job_ids: "[32912,32913]" }

    {
      "queued":0,
      "running":1
    }
    ```

## Reference Implementations ##

This project includes reference implementations written in Ruby and Javascript (with Node),
our two most-used languages at Custora. You can find them under the reference_implementations
folder of this project. These implementations are given only for example purposes, and there
is no requirement that you start by using one. They are intentionally simple, but using them
will give you a head start in terms of connecting to the variouls API endpoints.

## Expectations ##

There is no need for you to work on this project until you get a perfect score. We're looking
for your code to be well organized and for you to have put some effort into finding a good
solution to the problem, but you shouldn't spend more than a couple of hours on this project.
This is intended to be a fun opportunity for you to showcase your skills rather than a
contest to see who can get the highest score.

## Submitting Your Project ##

When you've completed your project, zip up the code and email it to aubrey@custora.com. We
will take a look at it within a couple of days and get back to you.

## Trouble? Problems? Questions? ##

If you run into problems along the way, feel free to email aubrey@custora.com, and we will
help you out.
