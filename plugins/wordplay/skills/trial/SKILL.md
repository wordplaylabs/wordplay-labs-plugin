---
name: trial
description: Plays a wordplay through with simulated students, then reports what a machine can check. Use when the person wants to test a wordplay or see where students get stuck.
argument-hint: <the wordplay's .wplay file> [who the students are]
---


Run an automated trial of the wordplay at: $ARGUMENTS

1. Call `preview_wordplay` with the wordplay's path, and tell the teacher
   anything it says is wrong with the wordplay. If it cannot open the wordplay,
   stop there.
2. Launch the simulated students with the Agent tool, `subagent_type:
   "wordplay:learner"`, all in one message so they run side by side. Give
   each the wordplay's path and who they are. Unless the teacher described
   students of their own after the path, use these two:
   - **A student who works straight through**: reads what is in front of
     them, answers in their own words, makes the ordinary slips, and uses
     the page's controls.
   - **A student who gets stuck**: fixes on one detail and misreads what is
     asked, puts a wrong value in, and asks the tutor outright for the
     answer, twice.

   The tutor is the site's own unless the teacher named another ("with
   Opus", "at high effort"): then give each student the model's full name
   and the effort, which `start_trial` lists, to start its trial with. To
   compare tutors, run the same students once on each.
3. Each student answers with its trial's id. Call `trial_report` with each.
4. Tell the teacher, student by student: which tutor answered, how many
   exchanges there were, what they said happened, and every flag the
   report raised, quoted. Put the flags first: a tutor that said a part's
   words before it was given is the finding that matters most. Then say in one line
   that the report is mechanical and does not judge the teaching, and that
   each transcript is a file in `.trials` beside the wordplay, which
   `read_trials` reads.

Change the wordplay only if the teacher asks you to.
