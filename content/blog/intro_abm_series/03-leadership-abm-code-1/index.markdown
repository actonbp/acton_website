---
title: "Making an ABM for leadership Pt 1"
weight: 1
subtitle: "Walkthrough of my code for building ABM for leadership"
excerpt: "Let's walkthrough an example of building an ABM for leadership"
date: 2021-08-20
lastmod: 2021-08-20
draft: false
alt: "Test for alt text in archetype"
author: Bryan Acton
---
<script src="{{< blogdown/postref >}}index_files/clipboard/clipboard.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/xaringanExtra-clipboard/xaringanExtra-clipboard.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/xaringanExtra-clipboard/xaringanExtra-clipboard.js"></script>
<script>window.xaringanExtraClipboard(null, {"button":"<i class=\"fa fa-clipboard\"><\/i> Copy Code","success":"<i class=\"fa fa-check\" style=\"color: #90BE6D\"><\/i> Copied!","error":"Press Ctrl+C to Copy"})</script>
<link href="{{< blogdown/postref >}}index_files/font-awesome/css/all.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/font-awesome/css/v4-shims.css" rel="stylesheet" />



# Walkthrough of code for simulation 👇

## Pseudocode Part 1: Agents 


**1. Make team of 5 people**

**2. Set the majority race and minority race**

**3. Assign badge/no badge randomly to team**

<span style="background-color: #FFFF00">**Code:**</span>
```{eval=FALSE}
to do_make_agents
  create-turtles people [set Racei TRUE set Badge TRUE] ; intialize agents
  layout-circle sort turtles 6 ; distribute agents on the world's surface
  ask turtles [ set label who] ;associate each agent with number
  set n random 5
  ask n-of n turtles[
  set breed majors
  ]
  ask turtles with [breed != majors] [set breed minors]
  ask majors [set color white
  set shape "dot"
  set size 3]
  ask minors [set color grey
  set shape "square"
  set size 3]
end

```

## Pseudocode Part 2: Interpersonal Teamwork Behavior 
**4. Each agents decides whether to encourage others** 
> If they have a badge, then more racial similarity leads to more likelihood of encouragement 

> If they do not have a badge, then more racial similarity leads to to less likelihood of encouragement 


<span style="background-color: #FFFF00">**Code:**</span>
```{eval=FALSE}
  set cutoff1 random-float 100
  ifelse Badge = TRUE
      [
    ifelse (race_sim * 100) > cutoff1
    [set Encouragei 1]
    [set Encouragei 0]
    ]
      [
     ifelse (race_sim * 100) > cutoff1
    [set Encouragei 0]
    [set Encouragei 1]
    ]

```

**5. Each agent decides whether they should perform Interpersonal Teamwork Behavior (ITWB)**

> If they have a badge, then more racial similarity leads to more likelihood of decision to perform ITWB

> If they do not have a badge, then more racial similarity leads to to less likelihood of decision to perform ITWB

<span style="background-color: #FFFF00">**Code:**</span>
```{eval=FALSE}
  set cutoff1 random-float 100
  ifelse Badge = TRUE
      [
    ifelse (race_sim * 100) > cutoff1
    [set Decidei 50]
    [set Decidei 0]
    ]
      [
     ifelse (race_sim * 100) > cutoff1
    [set Decidei 0]
    [set Decidei 50]
      ]
```

**6. Each agent performs ITWB based on previous decision as well as encouragement from the group and validation from the group the prior time point**

<span style="background-color: #FFFF00">**Code:**</span>
```{eval=FALSE}
set cutoff3 random-float 150
    set encour_sum count turtles with [Encouragei = 1]
    set valid_sum count turtles with [Validatei = 1]
    ifelse (encour_sum + valid_sum * 10) + Decidei > cutoff3
    [set Influencei 1]
    [set Influencei 0]
```

**7. Each agent decides to validate other group members performance of ITWB**

> If they have a badge, then more racial similarity leads to more likelihood of validation

> If they do not have a badge, then more racial similarity leads to to less likelihood of validation

<span style="background-color: #FFFF00">**Code:**</span>
```{eval=FALSE}
  set cutoff1 random-float 100
  ifelse Badge = TRUE
      [
     ifelse (race_sim * 100) > cutoff1
    [set Validatei 1]
    [set Validatei 0]
    ]
      [
   ifelse (race_sim * 100) > cutoff1
    [set Validatei 0]
    [set Validatei 1]
    ]
```

## Pseudocode Part 3: Leadership

**8. Each agent either perceives or does not perceive another agent as leader based on their ITWB performed compared to possible**

 > For agent*i*, if agent*k*'s performed ITWB is congruent with their ILT, then they will add 1 to their leadership perception score (do this for all agents)
 
<span style="background-color: #FFFF00">**Code:**</span>
```{eval=FALSE}
ask person_1[
        create-LP-from person_2
        [
         set weight 0
    ]
    create-LP-from person_3
        [
          set weight 0
    ]
    create-LP-from person_4
        [
          set weight 0
    ]
    create-LP-from person_5
        [
          set weight 0
    ]
  ]
    ]
    
ask LPS with [end1 = person_1 and end2 = person_2]; this is a tie with int 2 as the person being perceived
  [
      if([ITWB_prop] of person_2 > [ILT] of person_1)
                       [
                          set weight weight + 1
    ]
    ]
```

**9. Calculate leadership score for each agent**

<span style="background-color: #FFFF00">**Code:**</span>
```{eval=FALSE}
to calculate_leader_score
ask turtles[
    set leadership_score mean [weight] of my-in-LPS; calculates the mean of all the perceptions of others
]
end

```
