
---
fileClass: Machine
---

<p align="center"> <img src= "https://www.hackthebox.com//storage/avatars/28b71ec11bb839b5b58bdfc555006816.png"> </p>

#machine

## Operation system - Windows
<img style = "max-width:70px" src = "app://local//home/cher0/HTNotes/HTB/.res/Windows.png">

## Metadata

|                       |   |
| ----------------      | - |
| ID                    |633 |
| Name                  |Certified |
| Active                |❌  |
| User Flag             |✅ |
| Root Flag             |✅|
| Difficulty Text       |Medium  |
| Stars                 |⭐️ 4.8 |
| Created Note          |05/11/25 |
| Published             |11/02/24 |
| tags                  | |

<p style = "display:none">
id:: 633
active:: False
name:: Certified
os::Windows
user_flag:: True
root_flag:: True
difficulty_text:: Medium
stars:: 4.8
created:: 05/11/2025
published:: 11/02/24
avatar:: /storage/avatars/28b71ec11bb839b5b58bdfc555006816.png
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
    score: 6.8
  - item: "REAL"
    user: "user"
    score: 7.6
  - item: "CVE"
    user: "user"
    score: 6.5
  - item: "CUSTOM"
    user: "user"
    score: 3.5
  - item: "CTF"
    user: "user"
    score: 2.4
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
      count: 164
     
    - folder: "VERY EASY"
      count: 142

    - folder: "EASY"
      count: 792
      
    - folder: "NOT TO EASY"
      count: 1165
      
    - folder: "MEDIUM"
      count: 1452
     
    - folder: "A BIT HARD"
      count: 818
      
    - folder: "HARD"
      count: 462
      
    - folder: "EXTREMELY HARD"
      count: 93
      
    - folder: "INSANE"
      count: 35
      
    - folder: "BRAINFUCK"
      count: 44

    

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

