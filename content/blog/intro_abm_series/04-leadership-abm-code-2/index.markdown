---
title: "Making an ABM for leadership Pt 2"
author: "Bryan Acton"
date: '2021-08-30'
excerpt: 'Here is where we add a rule about thin slices of behavior '
subtitle: Walkthrough of my code for adding thin slices rule
lastmod: '2021-08-30'
draft: no
alt: Test for alt text in archetype
weight: 1
mathjax: yes
---
<script src="{{< blogdown/postref >}}index_files/clipboard/clipboard.min.js"></script>
<link href="{{< blogdown/postref >}}index_files/xaringanExtra-clipboard/xaringanExtra-clipboard.css" rel="stylesheet" />
<script src="{{< blogdown/postref >}}index_files/xaringanExtra-clipboard/xaringanExtra-clipboard.js"></script>
<script>window.xaringanExtraClipboard(null, {"button":"<i class=\"fa fa-clipboard\"><\/i> Copy Code","success":"<i class=\"fa fa-check\" style=\"color: #90BE6D\"><\/i> Copied!","error":"Press Ctrl+C to Copy"})</script>
<link href="{{< blogdown/postref >}}index_files/font-awesome/css/all.css" rel="stylesheet" />
<link href="{{< blogdown/postref >}}index_files/font-awesome/css/v4-shims.css" rel="stylesheet" />


## Purpose of this part 2: 

The goal of this post is to add to part 1, by adding a rule, that, as agents perform together, after 10 interactions, they do not rely on surface level characteristics any longer. 

> Note: What is added from this part of code will be <span style="background-color: #1aece9">**Highlighted in Blue**</span>


# Walkthrough of code for simulation 👇

## Pseudocode Part 1: Agents 


**1. Make team of 5 people**

**2. Set the majority race and minority race**

**3. Assign badge/no badge randomly to team**


<span style="background-color: #1aece9">**Agent's now also have a variable *Interpersonal teamwork behavior difference***</span>

<span style="background-color: #FFFF00">**Code:**</span>
```{eval=FALSE}
to do_make_agents
  create-turtles people [set Racei TRUE set Badge TRUE] ; intialize agents
  layout-circle sort turtles 6 ; distribute agents on the world's surface
  ask turtles [ set label who] ;associate each agent with number
    s012et n random 5
  ask n-of n turtles[
  set breed majors
  ]
  ask turtles with [breed != majors] [set breed minors]
  ask majors [set color white
  set shape "dot"
  set size 3]
  ask minors [set color grey
  set shape "square"
  set ITWB_diff (abs(ITWB - ITWB_mean) / ((ticks * 3) - 1))
  set size 3]
end

```

### What is the Thin Slices rule? 

The Thin slices rule is based on the premise that after minimal interaction (10 minutes), individuals move from surface level features to deep level features when involved in interactions:

**Interpersonal Teamwork Behavior Difference (ITWBD):**

`$$ITWBD = \frac{\left | ITWBi - ITB\mu    \right |}{\tau  - 1}$$`
`$$ITB = Interpersonal \quad Teamwork \quad Behavior \\
i = Person \\
\tau = Time \\
$$

## Pseudocode Part 2: Interpersonal Teamwork Behavior 
**4. Each agents decides whether to encourage others**.<span style="background-color: #1aece9">This is now based on ITWB difference if the thin slices rule is on</span>

> When the new **thin slices rule** is turned **ON**
>> If there is less than 10 interactions
>>> Same rule as when **thin slices rule is turned OFF** (see below)

>> If there is more than 10 interactions 
>>> The *less different* in ITWB, the *more* likelihood of encouragement 

> When the new **thin slices rule** is turned **OFF**
>> If they do have a badge, then more racial similarity leads to *more* likelihood of encouragement 

>> If they do not have a badge, then more racial similarity leads to *less* likelihood of encouragement 


<span style="background-color: #FFFF00">**Code:**</span>
```{eval=FALSE}
to do_encourage
  ask turtles [
  ifelse social_identity_rule = TRUE[
  set cutoff1 random-float 100
  set cutoff2 random-exponential 10
  ifelse ticks < rule_switch [
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
    ]
      [
ifelse (ITWB_diff * 100) < cutoff2
    [set Encouragei 1]
    [set Encouragei 0]
    ]
    ]
    [
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
    ]
    ]
end

```

**5. Each agent decides whether they should perform Interpersonal Teamwork Behavior (ITWB)**.<span style="background-color: #1aece9">This is now also based on ITWB difference if the thin slices rule is on</span>

> When the new **thin slices rule** is turned **ON**
>> If there is less than 10 interactions
>>> Same rule as when **thin slices rule is turned OFF** (see below)

>> If there is more than 10 interactions 
>>> The *less different* in ITWB, the *more* likelihood of decision 

> When the new **thin slices rule** is turned **OFF**
> If they have a badge, then more racial similarity leads *more* likelihood of decision 

> If they do not have a badge, then more racial similarity leads to *less* likelihood of decision 

<span style="background-color: #FFFF00">**Code:**</span>
```{eval=FALSE}
ask turtles[
    ifelse social_identity_rule = TRUE[
    set cutoff1 random-float 100
    set cutoff2 random-exponential 10
    ifelse ticks < rule_switch [
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
    ]
      [

    ifelse (ITWB_diff * 100) < cutoff2
    [set Decidei 50]
    [set Decidei 0]
    ]
      ]
    [
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

**7. Each agent decides to validate other group members performance of ITWB**.<span style="background-color: #1aece9">This is now also based on ITWB difference if the thin slices rule is on</span>

> When the new **thin slices rule** is turned **ON**
>> If there is less than 10 interactions
>>> Same rule as when **thin slices rule is turned OFF** (see below)

>> If there is more than 10 interactions 
>>> The *less different* in ITWB, the *more* likelihood of validation

> When the new **thin slices rule** is turned **OFF**
> If they have a badge, then more racial similarity leads to *more* likelihood of validation

> If they do not have a badge, then more racial similarity leads to to *less* likelihood of validation

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
