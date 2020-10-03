# Report on the Accident Severity Prediction Project
Author: Yihe Li

## Introduction / Business Problem
The problem that this data science project is aimed on is to use several conditions about a car accident that can be observed, such as 
weather condition, car type and road traffic load. The severity data can then be used to determine the priority to the accidents around the world, 
or to determine if helicopters/how many people should be used to rescure survivors. The severity can also gives an overall classifying of the magnitude 
of the accident, thus giving emergency rescurers a priority sequence that they should go to.

The city government and transportation departments are the main target audience of the data. They can use this data to 
generate annual reports to summarize the overall accident status and make suggestions to improve the overall safety. Researchers 
in the government can also use the data to determine places where most accident tend to happen, and thus place warning signs to prevent accidents.
This data and related suggestions in the conclusion part should help the government to reduce the fatailities in the future accident, 
as they can dispatch their rescures more intelligently and respond to severe accident quickly.

## Data
The data that will be used to predict severity level and train the model is the shared data fetched from GISWeb and Seattle government. 
This data contains the detailed traffic accident record in the time period 2004-present and is renewed weekly.

There are a total of 194673 accidents recorded, each record contains 38 different properties. The example of a data:

| Index          | Value                                                  |
|----------------|--------------------------------------------------------|
| SEVERITYCODE   | 2                                                      |
| X              | -122.32314840000002                                    |
| Y              | 47.70314032                                            |
| OBJECTID       | 1                                                      |
| INCKEY         | 1307                                                   |
| COLDETKEY      | 1307                                                   |
| REPORTNO       | 3502005                                                |
| STATUS         | Matched                                                |
| ADDRTYPE       | Intersection                                           |
| INTKEY         | 37475.0                                                |
| LOCATION       | 5TH AVE NE AND NE 103RD ST                             |
| EXCEPTRSNCODE  |                                                        |
| EXCEPTRSNDESC  | nan                                                    |
| SEVERITYCODE.1 | 2                                                      |
| SEVERITYDESC   | Injury Collision                                       |
| COLLISIONTYPE  | Angles                                                 |
| PERSONCOUNT    | 2                                                      |
| PEDCOUNT       | 0                                                      |
| PEDCYLCOUNT    | 0                                                      |
| VEHCOUNT       | 2                                                      |
| INCDATE        | 2013/03/27 00:00:00+00                                 |
| INCDTTM        | 3/27/2013 2:54:00 PM                                   |
| JUNCTIONTYPE   | At Intersection (intersection related)                 |
| SDOT_COLCODE   | 11                                                     |
| SDOT_COLDESC   | MOTOR VEHICLE STRUCK MOTOR VEHICLE, FRONT END AT ANGLE |
| INATTENTIONIND | nan                                                    |
| UNDERINFL      | N                                                      |
| WEATHER        | Overcast                                               |
| ROADCOND       | Wet                                                    |
| LIGHTCOND      | Daylight                                               |
| PEDROWNOTGRNT  | nan                                                    |
| SDOTCOLNUM     | nan                                                    |
| SPEEDING       | nan                                                    |
| ST_COLCODE     | 10                                                     |
| ST_COLDESC     | Entering at angle                                      |
| SEGLANEKEY     | 0                                                      |
| CROSSWALKKEY   | 0                                                      |
| HITPARKEDCAR   | N                                                      |

The usage of different parameters are as follows:
(Full description is in [Metadata.pdf](https://github.com/Mick235711/Coursera_Capstone/blob/main/Metadata.pdf)).
- `SEVERITYCODE` (1 or 2), `PERSONCOUNT` (0-81), `PEDCOUNT` (0-6), `PEDCYLCOUNT` (0-2), `VEHCOUNT` (0-12), `ST_COLCODE` (0-84): 
  These parameters describe the severity of the accident. We can uniformalize the severity of an accident by combining all these parameters. 
  The example data have a severity code of 2, person and vehicle count as 2, pedestrian and pedcylcist count of 0. The state collision code is 10.
  From these data, we can determine that the final severity value as 6 (sum of all the counts).
- `COLLISIONTYPE` (10 different types): Can be changed to values of 0-9 for effective predicting. The example have a collision type of `Angles` (1).
- `INCDATE` & `INCDTTM` (date & time): although not used for prediction, can be used to plot the frequency of accidents in each year. The example happened in March 27th, 2013.
- `JUNCTIONTYPE` (6 different types): Changed to 0-5. The 9 `Unknown`s can be seen as majority `Mid-Block (not related to intersection)`.
  The example have a junction type of `At Intersection (intersection related)` (1).
- `INATTENTIONIND` (Y/N): Change to 0-1. The NaN values represents No, so need processing. Example have an id of No (0).
- `UNDERINFL` (Y/N): Change to 0-1. Example have an value of No (0).
- `WEATHER` (11 different types): Can be combined into 
  `Clear` (`Clear`, `Partly Cloudy`, `Overcast`), 
  `Waterdrop` (`Raining`, `Snowing`, `Sleet/Hail/Freezing Rain`), 
  `Severe Condition` (`Blowing Sand/Dirt`, `Severe Crosswind`, `Fog/Smog/Smoke`),
  `Other` (`Other`, `Unknown`) and then change to 0-3. Example have weather of `Overcast` (0).
- `ROADCOND` (9 different types): Can be combined into 
  `Good` (`Dry`), 
  `Sweeping` (`Wet`, `Sand/Mud/Dirt`, `Oil`), 
  `Bad` (`Ice`, `Standing Water`, `Snow/Slush`), 
  `Other` (`Other`, `Unknown`) and then change to 0-3. Example have road condition of `Wet` (`Sweeping`, 1).
- `LIGHTCOND` (9 different types): Can assume the unknown in `Dark` to be street light on, as that is the majority. Then can be combined into 
  `Light` (`Daylight`), 
  `Partial Light` (`Dawn`, `Dusk`, `Dark - Street Lights On`, `Dark - Unknown Lighting`),
  `Dark` (`Dark - No Street Lights`, `Dark - Street Lights Off`),
  `Other` (`Other`, `Unknown`) and then change to 0-3. Example have light condition of `Daylight` (`Light`, 0).
- `PEDROWNOTGRNT` (Y/N): NaN = No, then change to 0-1. Example have value of No (0).
- `SPEEDING` (Y/N): NaN = No, then change to 0-1. Example have value of No (0).
- `HITPARKEDCAR` (Y/N): Change to 0-1. Example have value of No (0).

Together, we have 10 independent inputs and 1 target parameter (severity). The value of example data is as below:

|   SEVERITY |   COLLISIONTYPE |   JUNCTIONTYPE |   INATTENTIONIND |   UNDERINFL |   WEATHER |   ROADCOND |   LIGHTCOND |   PEDROWNOTGRANT |   SPEEDING |   HITPARKEDCAR |
|------------|-----------------|----------------|------------------|-------------|-----------|------------|-------------|------------------|------------|----------------|
|          6 |               1 |              1 |                0 |           0 |         0 |          1 |           0 |                0 |          0 |              0 |

We can then use machine learning models to perform the prediction.

