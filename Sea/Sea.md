
---
fileClass: Machine
---

<p align="center"> <img src= "https://www.hackthebox.com//storage/avatars/0011f6725aed869f8683589cb08c90d0.png"> </p>

#machine

## Operation system - Linux
<img style = "max-width:70px" src = "app://local//home/cher0/HTNotes/HTB/.res/Linux.png">

## Metadata

|                       |   |
| ----------------      | - |
| ID                    |620 |
| Name                  |Sea |
| Active                |❌  |
| User Flag             |✅ |
| Root Flag             |✅|
| Difficulty Text       |Easy  |
| Stars                 |⭐️ 3.5 |
| Created Note          |05/11/25 |
| Published             |08/10/24 |
| tags                  | |

<p style = "display:none">
id:: 620
active:: False
name:: Sea
os::Linux
user_flag:: True
root_flag:: True
difficulty_text:: Easy
stars:: 3.5
created:: 05/11/2025
published:: 08/10/24
avatar:: /storage/avatars/0011f6725aed869f8683589cb08c90d0.png
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
    score: 5.3
  - item: "CVE"
    user: "user"
    score: 5.8
  - item: "CUSTOM"
    user: "user"
    score: 4.2
  - item: "CTF"
    user: "user"
    score: 4.7
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
      count: 540
     
    - folder: "VERY EASY"
      count: 660

    - folder: "EASY"
      count: 2711
      
    - folder: "NOT TO EASY"
      count: 3151
      
    - folder: "MEDIUM"
      count: 1545
     
    - folder: "A BIT HARD"
      count: 765
      
    - folder: "HARD"
      count: 466
      
    - folder: "EXTREMELY HARD"
      count: 139
      
    - folder: "INSANE"
      count: 27
      
    - folder: "BRAINFUCK"
      count: 165

    

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

