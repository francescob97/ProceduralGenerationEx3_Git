# ProceduralGeneration

Procedural Content Generation (PCG) with classes and tools available in UE5.2 to properly populate a straight street with:

- Street lamps
- Benches
- Buildings
- Trash bins
- Parked Cars
- Trash

<img src="Documentation/Images/Cover.png" alt="Cover" style="width:720px;height:405px;">

## Implementation

### Road

The road is essentially a blueprint featuring a spline component traversing the PCG volume, using the Street PCG graph.
The spline then defines only the line that will run along the road; everything else is defined in the PCG graph instead.
The road is built in the first part of the graph, as a sequence of various segments of different types:

- Straight Street
- Straight Crosswalk Street
- 3 Way Street (or T street)
- 4 Way Street

These segments have different behaviors, so they are generated randomly through noise and percentage-based subdivision. 
For instance, after a three-way or four-way road, a road with crosswalks is also generated. 
Furthermore, three-way roads cannot generate additional elements in one of the three paths, and four-way roads cannot generate elements in any path.

| <img src="Documentation/Images/1_StreetsBehaviours.png" alt="treetsBehaviours" style="width:640px;height:360px;"> |
|:-----------------------------------------------------------------------------------------------------------------:|
|      3 Way road on the left, 4 Way road on the right. Both have a crosswalk segment on their orthogonal ways      |


| <img src="Documentation/Images/2_StreetPCG.png" alt="StreetPCG" style="width:720px;height:405px;"> |
|:--------------------------------------------------------------------------------------------------:|
|                                             Street PCG                                             |

### Left And Right sides

As can be seen in the right of the street part, the type of roads determine the left and right sides where the other elements 
will be generated. This happens for the 3 and 4 way roads, where 1 or 2 sides do not have to be considered for the placement of the other items.

#### Buildings

Buildings are generated along the previously calculated sides of the street and rotated to look at the street using a TransformPoints node.

| <img src="Documentation/Images/3_BuildingPCG.png" alt="BuildingPCG" style="width:640px;height:180px;"> | <img src="Documentation/Images/4_Building.png" alt="Building" style="width:640px;height:200px;"> |
|:------------------------------------------------------------------------------------------------------:|:------------------------------------------------------------------------------------------------:|
|                                              Building PCG                                              |                                            Buildings                                             |

#### Parked Cars

Parked cars are generated near the road as the buildings, but using BoundsModifier and SurfaceSampler to get points along 
this surface. Points are then rotated to follow the car lane direction.

| <img src="Documentation/Images/5_ParkedCarsPCG.png" alt="ParkedCarsPCG" style="width:640px;height:180px;"> | <img src="Documentation/Images/6_ParkedCars.png" alt="ParkedCars" style="width:640px;height:200px;"> |
|:----------------------------------------------------------------------------------------------------------:|:--------------------------------------------------------------------------------------------------:|
|                                              Parked Cars PCG                                               |                                     Parked Cars Sampled Points                                     |

#### Sidewalks

Sidewalks are an important section because they underlie all subsequent elements. They are obtained by appropriately scaling 
the right and left sides so that there is an appropriate surface area.

| <img src="Documentation/Images/7_SidewalksPCG.png" alt="SidewalksPCG" style="width:640px;height:220px;"> |
|:--------------------------------------------------------------------------------------------------------:|
|                                                Street PCG                                                |


##### Benches, Lamps, TrashBins

For benches, lamps, and bins, sidewalks have been sampled, the result is obtained through offset and bounds modifiers.
All these elements employ a self-pruning and difference node to prevent collisions. Additionally, lamps and bins are spawned
at a certain distance from each other to avoid proximity. This is achieved by adjusting their bounds, performing pruning, and then reverting the bounds to their original state.

Benches, Lamps, TrashBins and then Trash have a difference node to avoid spawning different object in the same location.

| <img src="Documentation/Images/8_BenchesLampsTrashPCG.png" alt="BenchesLampsTrashPCG" style="width:640px;height:320px;"> |
|:------------------------------------------------------------------------------------------------------------------------:|
|                                              Benches, Lamps, Trash bins PCG                                              |

| <img src="Documentation/Images/9_Proximity.png" alt="Proximity" style="width:320px;height:150px;"> |
|:--------------------------------------------------------------------------------------------------:|
|                                      Avoid too close objects                                       |

##### Trash

Trash  is spawned near the bins. Similar to the road generation, a filter and noise are used to ensure variety and handle trash bags, cans, and other items.

| <img src="Documentation/Images/10_TrashPCG.png" alt="TrashPCG" style="width:640px;height:150px;"> |
|:-------------------------------------------------------------------------------------------------:|
|                                             Trash PCG                                             |

| <img src="Documentation/Images/11_Trash.png" alt="Trash" style="width:640px;height:400px;"> |
|:-------------------------------------------------------------------------------------------:|
|                                    Trash spawn near bins                                    |

## Exposed Variables

- StraightRoadFreqPerc: Percentage of straight road segments
- CrosswalkRoadFreqPerc: Percentage of crosswalk road segments
- 4WayRoadFreqPerc: Percentage of 4 way road segments
- 3WayUPRoadFreqPerc: Percentage of 3 way road segments looking up
- 3WayDOWNRoadFreqPerc: Percentage of 3 way road segments looking down
- ParkedCarUPDensity: Density of parked cars in the upper car lane
- ParkedCarDOWNDensity: Density of parked cars in the lower car lane
- BenchDensity: Density of the benches on sidewalks
- LampDensity: Density of the Lamps on sidewalks
- LampDistanceExclusionMultiplier: Multiplier to avoid too close lamps
- TrashBinDensity: Density of the trash bins on sidewalks
- TrashBinDistanceExclusionMultiplier: Multiplier to avoid too close bins
- TrashDensity: Density of the trash near bins
- TrashSackFreqPerc: Percentage of trash bags
- TrashWasteFreqPerc: Percentage of other trash elements

## Reference links

Assets used **_Low Poly Town by PolyArt3D_**: https://www.unrealengine.com/marketplace/en-US/product/low-poly-town

Video by **_Adrien Logut_**  https://dev.epicgames.com/community/learning/tutorials/j4xJ/unreal-engine-introduction-to-procedural-generation-plugin-in-ue5-2