Degree level: Master’s
# Project proposal

## 1. The problem and goal

(Explain the problem you want to solve and why it matters. Describe what you
want to improve, how you will measure improvement, and which limits or
requirements must be respected. A short example can help someone outside your
field understand the problem.)

**The problem and goal in my project:**

In our gait research, we use infrared cameras to track markers attached to a person's body while they walk. After each recording session, we use Qualisys Track Manager (QTM) to label the markers and check their movement over time, called their trajectories. This requires manual work, especially when markers disappear briefly, their labels become mixed up, or their trajectories contain sudden jumps. We tried QTM's Automatic Identification of Markers (AIM), but it was not reliable enough for our recordings.

My goal is to develop an agent that helps assign marker labels and identifies suspicious sections for review. For example, if an ankle marker suddenly appears far from the ankle and then returns, the system should flag the section and investigate whether a labeling error occurred. The first version will focus on one marker layout and a limited set of problems: missing labels, swapped labels, and spikes.

I have manually checked recordings that can serve as reference examples. I will measure labeling accuracy, detected errors, false alarms, incorrect corrections, and the time needed for manual review. Some recordings will be kept separate for evaluation, preferably from different participants. The system must preserve the original data and leave uncertain cases for human review. Unusual walking patterns, including patient gait, must not automatically be treated as errors.


## 2. The simulator

(Describe a program or model that could simulate your system and calculate what
happens under different conditions. Explain what inputs it takes and what
results it produces. State whether it can run on your computer and accept inputs
in a text file, such as JSON or YAML. If you have not chosen a simulator yet,
name an option you want to explore and explain what you need to check.)

**The simulator in my project:**

I plan to build a Python evaluation environment that tests proposed changes to recorded marker trajectories. It will act as the project's ground: a repeatable numerical tool that the agent can call. It will evaluate recorded movement rather than simulate a whole person walking.

The environment will read recordings exported from QTM in C3D format, using the free, open-source ezc3d library. A JSON text file will specify the recording, marker layout, selected frames, proposed label changes, and checking settings. The numerical checks will examine movement continuity, speed, acceleration, and distances between related markers. The output will include suspicious frame ranges and scores showing how a proposed change affects these checks. A smoother trajectory alone will not be considered proof of a correct label.

For controlled testing, I will introduce known errors into copies of manually checked recordings, such as removed labels, swapped labels, and added spikes. The original checked recordings will provide the reference answers for evaluation, without exposing those answers to the agent during testing. Where available, I will also test on real errors using recordings before and after manual correction.

This environment is intended to run locally on my computer without QTM running during each evaluation. I still need to check the exported data, runtime, and suitable checking limits. The installed QTM version and its scripting capabilities will be checked before direct integration.

The intended final workflow is to use the agent with QTM and apply approved marker labels and trajectory edits through its scripting interface, once compatibility has been verified. The final output should be a labeled recording that can be viewed and used in QTM, together with flagged sections that still need manual review. Exported recordings are the starting point for development and testing. Running the Python evaluator independently only means that numerical checks can be tested without opening QTM each time; QTM remains part of the intended final workflow.



## 3. The agent's role

(Explain what the language-model-based agent will decide or change, and how it
will use the simulator's results to choose its next action. If known, list the
variables it may change and their allowed ranges. Explain what you expect the
language model to contribute: could a simple program that tries different
options and picks the best result achieve the same goal? Include one question
you still need to answer.)

**The agent in my project:**

The language-model-based agent will use the evaluator's results to decide what to investigate next. It may propose assigning a label to an unidentified trajectory, connecting a separated section to the correct marker, or swapping labels over a selected frame range. It will test a proposed change on a copy, read the numerical results, and then revise its proposal, recommend it for approval, or request human review.

Its choices will be limited to the marker names in the defined layout and frame ranges within the recording. Numerical checks and allowed limits will be defined during development and kept fixed for evaluation. The first version will flag suspicious spikes rather than automatically smooth them or invent replacement positions.

A fixed program can already detect large jumps and compare candidate labels. The possible contribution of the language model is choosing useful follow-up checks when the evidence is ambiguous or conflicting. I will compare the agent with a fixed numerical workflow using the same tools and candidate corrections. The agent's value must be demonstrated through better results or less manual review, rather than assumed.

One question I still need to answer is: can the agent resolve ambiguous labeling errors more effectively than a fixed workflow, without introducing more incorrect corrections?



## Explain your project to someone outside your field

(Now describe your whole project again, as if you were explaining it to someone
from another field who has no background in your topic. For example, if you
study chemical engineering, imagine explaining it to a physicist; if you study
aeronautical engineering, imagine explaining it to someone in medicine; if you
study data science, imagine explaining it to a materials engineer. In one or two
short paragraphs, connect all three parts: the problem you want to solve, how
the simulator helps you test ideas, and what the agent will decide or change.
Use everyday language, explain any technical terms you need, and avoid
unexplained abbreviations. Do not simply copy your answers above.)

**My project in simple words:**

We record people walking by attaching small markers to their bodies and tracking them with cameras. The recordings show moving points, but we need to know which point belongs to each body location. Points can disappear, get mixed up, or suddenly jump because of recording errors. Checking and correcting these problems by hand takes time.

I want to build an AI assistant that helps with this checking. A separate Python program will test possible label changes and calculate whether they make the recorded movement more consistent. The assistant will use these results to decide what to try next and which cases need a person's judgment. We will test it using recordings that have already been checked by hand, including copies with deliberately added errors. The aim is to reduce repetitive work while keeping the final data trustworthy.
