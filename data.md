# Data
The data that will be used to predict severity level and train the model is the shared data fetched from GISWeb and Seattle government. 
This data contains the detailed traffic accident record in the time period 2004-present and is renewed weekly.

The example of a data:

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
- `COLLISIONTYPE` (10 different types): Can be changed to values of 0-9 for effective predicting
- `INCDATE` & `INCDTTM` (date & time): although not used for prediction, can be used to plot the frequency of accidents in each year
- `JUNCTIONTYPE` (6 different types): Changed to 0-5. The 9 `Unknown`s can be seen as majority `Mid-Block (not related to intersection)`.
- `INATTENTIONIND` (Y/N): Change to 0-1. The NaN values represents No, so need processing.
- `UNDERINFL` (Y/N): Change to 0-1
- `WEATHER` (11 different types): Can be combined into 
  `Clear` (`Clear`, `Partly Cloudy`, `Overcast`), 
  `Waterdrop` (`Raining`, `Snowing`, `Sleet/Hail/Freezing Rain`), 
  `Severe Condition` (`Blowing Sand/Dirt`, `Severe Crosswind`, `Fog/Smog/Smoke`),
  `Other` (`Other`, `Unknown`) and then change to 0-3
- `ROADCOND` (9 different types): Can be combined into 
  `Good` (`Dry`), 
  `Sweeping` (`Wet`, `Sand/Mud/Dirt`, `Oil`), 
  `Bad` (`Ice`, `Standing Water`, `Snow/Slush`), 
  `Other` (`Other`, `Unknown`) and then change to 0-3
- `LIGHTCOND` (9 different types): Can assume the unknown in `Dark` to be street light on, as that is the majority. Then can be combined into 
  `Light` (`Daylight`), 
  `Partial Light` (`Dawn`, `Dusk`, `Dark - Street Lights On`, `Dark - Unknown Lighting`),
  `Dark` (`Dark - No Street Lights`, `Dark - Street Lights Off`),
  `Other` (`Other`, `Unknown`) and then change to 0-3
- `PEDROWNOTGRNT` (Y/N): NaN = No, then change to 0-1
- `SPEEDING` (Y/N): NaN = No, then change to 0-1
- `HITPARKEDCAR` (Y/N): Change to 0-1

Together, we have 10 independent inputs and 1 target parameter (sevirity). We can then use machine learning models to perform the prediction.

