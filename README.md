## Project: 3D Motion Planning
![Quad Image](./misc/enroute.png)

---


# Required Steps for a Passing Submission:
1. Load the 2.5D map in the colliders.csv file describing the environment.
2. Discretize the environment into a grid or graph representation.
3. Define the start and goal locations.
4. Perform a search using A* or other search algorithm.
5. Use a collinearity test or ray tracing method (like Bresenham) to remove unnecessary waypoints.
6. Return waypoints in local ECEF coordinates (format for `self.all_waypoints` is [N, E, altitude, heading], where the drone’s start location corresponds to [0, 0, 0, 0].
7. Write it up.
8. Congratulations!  Your Done!

## [Rubric](https://review.udacity.com/#!/rubrics/1534/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it! Below I describe how I addressed each rubric point and where in my code each point is handled.

### Explain the Starter Code

#### 1. Explain the functionality of what's provided in `motion_planning.py` and `planning_utils.py`
These scripts contain a basic planning implementation that includes event driven control and planning of the drone implementing a grid based a*search of the city while avoiding obstacles. At the start of the project these files held a basic implementation of this that used predefined waypoints, and start/goal locations.

And here's a lovely image of my results
![Top Down View](./misc/high_up.png)

### Implementing Your Path Planning Algorithm

#### 1. Set your global home position
Here students should read the first line of the csv file, extract lat0 and lon0 as floating point values and use the self.set_home_position() method to set global home. Explain briefly how you accomplished this in your code.

In my code I read the first line of the csv file and extracted it into the row_one array variable. I then extracted the lon0 and lat0 values from row and coverted them into floats. Lastly the home position of the drone was set using set_home_position with the latitude and longitude extracted from the csv file. 

And here is a lovely picture of our downtown San Francisco environment from above!
![Map of SF](./misc/map.png)

#### 2. Set your current local position
Here as long as you successfully determine your local position relative to global home you'll be all set. Explain briefly how you accomplished this in your code.

To accomplish setting the drone's local position I used the provided global_to_local function with the global position of the craft and the global home of the craft as parameters. This was then saved to the current_loc_pos function.

#### 3. Set grid start position from local position
This is another step in adding flexibility to the start location. As long as it works you're good to go!

To get the start location of the drone I extracted the current_loc_pos north and east values into their own variables and converted them to integers. Then I created a new variable grid_start with the following values: ((north - north_offset) and (east - east_offset))

#### 4. Set grid goal position from geodetic coords
This step is to add flexibility to the desired goal location. Should be able to choose any (lat, lon) within the map and have it rendered to a goal location on the grid.

To find the desired goal location I flew the drone around for a bit and saved the latitude and longitude to the goal_pos_gbl_1 and then coverted it to geodetic coordinates using the provided global_to_local function. Then using this value I extracted and coverted the north and east values into their own variables. Lastly I calculated the goal position using the following: ((north_goal + -north_offset), (east_goal + -east_offset))

#### 5. Modify A* to include diagonal motion (or replace A* altogether)
Minimal requirement here is to modify the code in planning_utils() to update the A* implementation to include diagonal motions on the grid that have a cost of sqrt(2), but more creative solutions are welcome. Explain the code you used to accomplish this step.

To account for diagonal motions I create a few new valid actions: 
* NORTH_EAST
* NORTH_WEST
* SOUTH_EAST
* SOUTH_WEST
Then in the a* function I used sqrt(2) for the third parameter of the action to be saved to the path. This allowed for the drone to move diagonally.

#### 6. Cull waypoints 
For this step you can use a collinearity test or ray tracing method like Bresenham. The idea is simply to prune your path of unnecessary waypoints. Explain the code you used to accomplish this step.

To prune the flight path found by a* I created the following functions: 
* point_transform() - takes a (x, y) point and changes it to a (x y, 1) shape
* collinearity_check - takes in three points and an epsilon then it puts the points in a matrix and determines if it is less than epsilon( if so it returns true, any other case returns false)
* prune_path - takes in a path, loops through the path three points at a time,  transforms them to (x, y, 1) shape, puts them through the collinearity check, and then either takes off the middle point if collinear or moves on to the next point and check. Once this is finished the function returns a pruned path.


### Execute the flight
#### 1. Does it work?
It works!

### Double check that you've met specifications for each of the [rubric](https://review.udacity.com/#!/rubrics/1534/view) points.
  
# Extra Challenges: Real World Planning - FUTURE TODO 

For an extra challenge, consider implementing some of the techniques described in the "Real World Planning" lesson. You could try implementing a vehicle model to take dynamic constraints into account, or implement a replanning method to invoke if you get off course or encounter unexpected obstacles.


