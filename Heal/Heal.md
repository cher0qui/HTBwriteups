
---
fileClass: Machine
---

<p align="center"> <img src= "https://www.hackthebox.com//storage/avatars/dcd5ef09ab764228c64385374ac744c1.png"> </p>

#machine

## Operation system - Linux
<img style = "max-width:70px" src = "app://local//home/cher0/HTNotes/HTB/.res/Linux.png">

## Metadata

|                       |   |
| ----------------      | - |
| ID                    |640 |
| Name                  |Heal |
| Active                |✅  |
| User Flag             |✅ |
| Root Flag             |✅|
| Difficulty Text       |Medium  |
| Stars                 |⭐️ 4.5 |
| Created Note          |05/11/25 |
| Published             |12/14/24 |
| tags                  | |

<p style = "display:none">
id:: 640
active:: True
name:: Heal
os::Linux
user_flag:: True
root_flag:: True
difficulty_text:: Medium
stars:: 4.5
created:: 05/11/2025
published:: 12/14/24
avatar:: /storage/avatars/dcd5ef09ab764228c64385374ac744c1.png
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
    score: 7.1
  - item: "REAL"
    user: "user"
    score: 6.1
  - item: "CVE"
    user: "user"
    score: 6.8
  - item: "CUSTOM"
    user: "user"
    score: 3.2
  - item: "CTF"
    user: "user"
    score: 3.9
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
      count: 209
     
    - folder: "VERY EASY"
      count: 207

    - folder: "EASY"
      count: 1265
      
    - folder: "NOT TO EASY"
      count: 1523
      
    - folder: "MEDIUM"
      count: 1562
     
    - folder: "A BIT HARD"
      count: 567
      
    - folder: "HARD"
      count: 268
      
    - folder: "EXTREMELY HARD"
      count: 49
      
    - folder: "INSANE"
      count: 6
      
    - folder: "BRAINFUCK"
      count: 26

    

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

