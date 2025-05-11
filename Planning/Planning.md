
---
fileClass: Machine
---

<p align="center"> <img src= "https://www.hackthebox.com//storage/avatars/c9efb253e7d1d9b407113e11afdaa905.png"> </p>

#machine

## Operation system - Linux
<img style = "max-width:70px" src = "app://local//home/cher0/HTNotes/HTB/Machines/Planning/../../.res/Linux.png">

## Metadata

|                       |   |
| ----------------      | - |
| ID                    |660 |
| Name                  |Planning |
| Active                |✅  |
| User Flag             |✅ |
| Root Flag             |✅|
| Difficulty Text       |Easy  |
| Stars                 |⭐️ 2.9 |
| Created Note          |05/11/25 |
| Published             |05/10/25 |
| tags                  | |

<p style = "display:none">
id:: 660
active:: True
name:: Planning
os::Linux
user_flag:: True
root_flag:: True
difficulty_text:: Easy
stars:: 2.9
created:: 05/11/2025
published:: 05/10/25
avatar:: /storage/avatars/c9efb253e7d1d9b407113e11afdaa905.png
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
    score: 6
  - item: "REAL"
    user: "user"
    score: 3.4
  - item: "CVE"
    user: "user"
    score: 4.8
  - item: "CUSTOM"
    user: "user"
    score: 5.2
  - item: "CTF"
    user: "user"
    score: 6.6
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
      count: 144
     
    - folder: "VERY EASY"
      count: 87

    - folder: "EASY"
      count: 227
      
    - folder: "NOT TO EASY"
      count: 59
      
    - folder: "MEDIUM"
      count: 24
     
    - folder: "A BIT HARD"
      count: 8
      
    - folder: "HARD"
      count: 3
      
    - folder: "EXTREMELY HARD"
      count: 0
      
    - folder: "INSANE"
      count: 2
      
    - folder: "BRAINFUCK"
      count: 18

    

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

