# NFL Data Bowl: Quantifying Defensive Processing & Pursuit Efficiency

![NFL Data Bowl](https://img.shields.io/badge/NFL-Big_Data_Bowl-red)
![Status](https://img.shields.io/badge/Research-Ongoing-orange)
![R](https://img.shields.io/badge/Maintained%20in-R-blue)
![nflfastR](https://img.shields.io/badge/Data-nflfastR-orange)

## Executive Summary
This project attempts to quantify **defensive player processing** in the NFL using player tracking data and play-by-play information. By merging frame-by-frame observations with `nflfastR` data, this model analyzes how defenders react when the ball is in the air and how their reaction time and spatial positioning influence the outcome of a play.

The main goal is to measure individual defender impact by identifying how they position themselves in relation to a ball carrier and the football’s trajectory, ultimately relating these behaviors to **Expected Points Added (EPA)**.

### Key Performance Predictors:
* **Pursuit Vectors:** Directional movement relative to the target.
* **Predicted Tackle Points:** Anticipatory movement toward a ball carrier's future location based on current velocity.
* **Reaction Timing:** Identifying the exact frame a player begins closing space toward the catch point.
* **Optimal Pursuit Angles:** Geometric efficiency of the path taken.
* **Ball-in-Air Tracking:** Evaluating movement efficiency specifically during the window between ball release and catch.

---

## Technical Workflow

### 1. Data Integration & ETL
The pipeline merges 2023 weekly tracking data with `nflfastR` play-by-play metrics. This allows the model to link physical movement variables (speed, acceleration, coordinates) with situational context (EPA, down/distance, completion status).

### 2. Spatial Modeling & Vector Calculus
We utilize Euclidean distance and vector decomposition to track the relationship between players and the ball:
* **Distance to Ball:** $dist = \sqrt{(x - ball\_land\_x)^2 + (y - ball\_land\_y)^2}$
* **Optimal Pathing:** Calculating the straight-line distance from a player's initial position to the landing spot.
* **Velocity Decomp:** Decomposing speed ($s$) and direction ($dir$) into radians to calculate **Speed Toward Ball**:
    * $motion\_vec\_x = s \cdot \cos(dir \cdot \frac{\pi}{180})$
    * $motion\_vec\_y = s \cdot \sin(dir \cdot \frac{\pi}{180})$

### 3. Metric Generation
* **Reaction Frame:** Defined as the first frame where `dist_to_ball < lag(dist_to_ball)`, marking the defender's physical commitment to the ball.
* **Pursuit Efficiency:** The ratio of speed directed toward the ball versus total movement speed.
* **Angle Error:** The degree difference between a player's actual motion vector and the mathematically optimal pursuit angle.

---

## 📊 Key Analysis Modules

### Defensive Back (DB) EPA Prevented
We quantify DB impact by measuring how often tight coverage (separation < 2 yards) forces incomplete passes or interceptions. Defenders are ranked by the total offensive value (EPA) they prevented throughout the season.

### Linebacker (LB) Anticipation
Specifically focuses on LB response to **Screens** and **Play-Action**. By isolating these play types, we grade which linebackers possess the highest processing speed by identifying who reacts earliest to the ball's actual trajectory versus the offensive deception.

### Contested Catch Analysis
A specialized look at Wide Receivers (WRs) and Tight Ends (TEs) who succeed in tight windows. We model the relationship between catch probability and separation distance, identifying "Contested Catch Specialists" who outperform league averages in coverage under 2 yards.

---

## Head-to-Head (H2H) Synthesis Engine
The project features a predictive engine that pits players against each other in hypothetical 1v1 scenarios.

**Scoring Logic:**
* **WR/Offense Score:** Based on distance to ball, route efficiency, and angle error.
* **DB/Defense Score:** Based on closing speed, reaction frame, and distance closed while the ball is airborne.

```r
# Example usage of the head-to-head function
h2h('CeeDee Lamb', 'Christian Gonzalez')
