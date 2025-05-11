
---
fileClass: Machine
---

<p align="center"> <img src= "https://www.hackthebox.com//storage/avatars/bf7ae27f4e0ce1703bdd10d538334d9e.png"> </p>

#machine

## Operation system - Linux
<img style = "max-width:70px" src = "app://local//home/cher0/HTNotes/HTB/.res/Linux.png">

## Metadata

|                       |   |
| ----------------      | - |
| ID                    |646 |
| Name                  |Cat |
| Active                |✅  |
| User Flag             |✅ |
| Root Flag             |✅|
| Difficulty Text       |Medium  |
| Stars                 |⭐️ 3.5 |
| Created Note          |05/11/25 |
| Published             |02/01/25 |
| tags                  | |

<p style = "display:none">
id:: 646
active:: True
name:: Cat
os::Linux
user_flag:: True
root_flag:: True
difficulty_text:: Medium
stars:: 3.5
created:: 05/11/2025
published:: 02/01/25
avatar:: /storage/avatars/bf7ae27f4e0ce1703bdd10d538334d9e.png
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
    score: 6.3
  - item: "REAL"
    user: "user"
    score: 5.8
  - item: "CVE"
    user: "user"
    score: 6.1
  - item: "CUSTOM"
    user: "user"
    score: 3.9
  - item: "CTF"
    user: "user"
    score: 4.2
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
      count: 126
     
    - folder: "VERY EASY"
      count: 88

    - folder: "EASY"
      count: 372
      
    - folder: "NOT TO EASY"
      count: 882
      
    - folder: "MEDIUM"
      count: 1424
     
    - folder: "A BIT HARD"
      count: 859
      
    - folder: "HARD"
      count: 592
      
    - folder: "EXTREMELY HARD"
      count: 194
      
    - folder: "INSANE"
      count: 48
      
    - folder: "BRAINFUCK"
      count: 155

    

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

