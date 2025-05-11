
---
fileClass: Machine
---

<p align="center"> <img src= "https://www.hackthebox.com//storage/avatars/2d22afd496c5ae6f6c51ca24bf3719e1.png"> </p>

#machine

## Operation system - Linux
<img style = "max-width:70px" src = "app://local//home/cher0/HTNotes/HTB/.res/Linux.png">

## Metadata

|                       |   |
| ----------------      | - |
| ID                    |645 |
| Name                  |BigBang |
| Active                |✅  |
| User Flag             |✅ |
| Root Flag             |✅|
| Difficulty Text       |Hard  |
| Stars                 |⭐️ 4.2 |
| Created Note          |05/11/25 |
| Published             |01/25/25 |
| tags                  | |

<p style = "display:none">
id:: 645
active:: True
name:: BigBang
os::Linux
user_flag:: True
root_flag:: True
difficulty_text:: Hard
stars:: 4.2
created:: 05/11/2025
published:: 01/25/25
avatar:: /storage/avatars/2d22afd496c5ae6f6c51ca24bf3719e1.png
tags:: 
</p>

## Statistics


```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: Radar

#-----------------#
#- chart data    -#
#-----------------#
data:
  - item: "ENUM"
    user: "user"
    score: 6.9
  - item: "REAL"
    user: "user"
    score: 6.3
  - item: "CVE"
    user: "user"
    score: 5.4
  - item: "CUSTOM"
    user: "user"
    score: 4.6
  - item: "CTF"
    user: "user"
    score: 3.7
  - item: "ENUM"
    user: "author"
    score: 0
  - item: "REAL"
    user: "author"
    score: 0
  - item: "CVE"
    user: "author"
    score: 0
  - item: "CUSTOM"
    user: "author"
    score: 0
  - item: "CTF"
    user: "author"
    score: 0

#-----------------#
#- chart options -#
#-----------------#
options:
  xField: "item"
  yField: "score"
  seriesField: "user"
  meta:
    score:
      alias: "Score"
      min: 0
      nice: true
  xAxis:
    line: null
    tickLine: null
  yAxis:
    label: false
    grid:
      alternateColor: "rgba(0, 0, 0, 0.04)"
  point: []
  area: []
```



### User rating


```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: Column

#-----------------#
#- chart data    -#
#-----------------#
data:
    - folder: "PIECE OF CAKE"
      count: 69
     
    - folder: "VERY EASY"
      count: 28

    - folder: "EASY"
      count: 102
      
    - folder: "NOT TO EASY"
      count: 149
      
    - folder: "MEDIUM"
      count: 230
     
    - folder: "A BIT HARD"
      count: 211
      
    - folder: "HARD"
      count: 470
      
    - folder: "EXTREMELY HARD"
      count: 394
      
    - folder: "INSANE"
      count: 266
      
    - folder: "BRAINFUCK"
      count: 683

    

#-----------------#
#- chart options -#
#-----------------#
options:
  xField: "folder"
  yField: "count"
  padding: auto
  label:
    position: "middle"
    style:
      opacity: 0.6
      fontSize: 12
  columnStyle:
    fillOpacity: 0.5
    lineWidth: 1
    strokeOpacity: 0.7
    shadowColor: "grey"
    shadowBlur: 10
    shadowOffsetX: 5
    shadowOffsetY: 5
  xAxis:
    label:
      autoHide: false
      autoRotate: true
  meta:
    count:
      alias: "Votes"
```



```button
name Update this Machine info
type link
action obsidian://shell-commands/?vault=HTB&execute=g7sm2q030y
templater true
```

