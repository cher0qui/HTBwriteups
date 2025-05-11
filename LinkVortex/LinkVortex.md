
---
fileClass: Machine
---

<p align="center"> <img src= "https://www.hackthebox.com//storage/avatars/97f12db8fafed028448e29e30be7efac.png"> </p>

#machine

## Operation system - Linux
<img style = "max-width:70px" src = "app://local//home/cher0/HTNotes/HTB/.res/Linux.png">

## Metadata

|                       |   |
| ----------------      | - |
| ID                    |638 |
| Name                  |LinkVortex |
| Active                |❌  |
| User Flag             |✅ |
| Root Flag             |✅|
| Difficulty Text       |Easy  |
| Stars                 |⭐️ 4.2 |
| Created Note          |05/11/25 |
| Published             |12/07/24 |
| tags                  | |

<p style = "display:none">
id:: 638
active:: False
name:: LinkVortex
os::Linux
user_flag:: True
root_flag:: True
difficulty_text:: Easy
stars:: 4.2
created:: 05/11/2025
published:: 12/07/24
avatar:: /storage/avatars/97f12db8fafed028448e29e30be7efac.png
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
    score: 6.7
  - item: "REAL"
    user: "user"
    score: 5.5
  - item: "CVE"
    user: "user"
    score: 5.7
  - item: "CUSTOM"
    user: "user"
    score: 4.3
  - item: "CTF"
    user: "user"
    score: 4.5
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
      count: 490
     
    - folder: "VERY EASY"
      count: 821

    - folder: "EASY"
      count: 3884
      
    - folder: "NOT TO EASY"
      count: 3693
      
    - folder: "MEDIUM"
      count: 1437
     
    - folder: "A BIT HARD"
      count: 544
      
    - folder: "HARD"
      count: 314
      
    - folder: "EXTREMELY HARD"
      count: 86
      
    - folder: "INSANE"
      count: 21
      
    - folder: "BRAINFUCK"
      count: 85

    

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

