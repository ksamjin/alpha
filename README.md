# Nrf52840_DW3000
Our project is RC car autonomous driving using UWB positioning

## Project Introduction
This project implemented autonomous driving of an RC car using only UWB communication with one tag and three anchors. It was confirmed that various routes such as circles, triangles, and squares were tracked well, and the details of our research were written in 'Realization of RC Car Autonomous Driving Using UWB Positioning'.

Considering the advantages of using UWB communication, positioning is possible indoors where GPS does not work, and that positioning is possible with one UWB communication module instead of positioning using many sensors such as cameras and lidars in an autonomous driving environment, we decided on this project. proceeded to
In this project, 1 Tag, 3 Anchors were implemented as Single-Sided Two Way Ranging (SS-TWR). In the future, we plan to develop the project using AOA (Angle of Arrival) technology using only one tag and one anchor.

## Contents
- [How to install and run the project](#How-to-install-and-run-the-project)
- [How to use project](#How-to-use-project)
  
    -[Description of each file](#Descriptio-of-each-file)
  
    -[How to combined inititaor and PWM example](#How-to-combined-inititaor-and-PWM-example)
- [Team members and references](#Team-members-and-references)


