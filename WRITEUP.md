# Project: Search and Sample Return

## Summary
- Utilized image processing and computer vision techniques to do the following:
- - Identify rock samples, obstacles and navigable terrain.
- - Map more than 40% of the environment with atleast 60% fidelity to the ground truth.
- Rover picked up 3-6 samples and identified all samples.

## Videos
- [Notebook Analysis Output Video](https://github.com/rohitsub11/RoboND-Rover-Project/tree/master/output/test_mapping.mp4)

## Data Analysis
- The initial analaysis of methodologies was performed in the python notebook.
- - [The Notebook](https://github.com/rohitsub11/RoboND-Rover-Project/blob/master/code/Rover_Project_Test_Notebook.ipynb)
- - [Output Video of this Notebook](https://github.com/rohitsub11/RoboND-Rover-Project/tree/master/output/test_mapping.mp4)

## Perception
- Given a camera image from the rover, obstacles (`obs`), samples (`rock`), and navigable terrain (`world`) are identified using their color.

- I used color thresholding to identify rock, ground and obstacles.
- I transformed the image from camera view to sky view using cv2.getPerspectiveTransform and cv2.warpPerspective functions. Source and destination points are derived from given calibration images located in the calibration_images folder.
- I converted these transformed pixels into rover coordinates then world coordinates by rotating, scaling and rranslating then to polar coordinates and added them to the image map as navigable, obstacles and rock pixels.
- I then updated the rover properties such as Rover.angles, Rover.found_rock.

## Decision
- Given the updated results of the perception, I updated the decision step as follows:
### 1. Update last recorded position
- We are given the current x, y position, and heading of the rover, if this is significantly different from the last recorded position we can say that we've sufficiently moved.
- If we did, let's update the last recorded position, and note that there is sufficient movement

### 2. Check if we're stuck or near a sample
- If we're near a sample, we should stop moving so make `mode = stop`
- If we're not near a sample and our velocity is zero even though we are throttling, then this means we are `stuck`
- If we're not near a sample and there is no sufficient movement even though a significant time has passed, then we can safely say we are stuck.

### 3A. If we're in `forward` mode, command appropriately
- Check if the path is sufficiently clear
- If the path is clear, we should move forward given the suggested steering angle. We should accelerate if we haven't reached our allowed velocity.
- If the path is clear and we have found the rock, we should move slowly by keeping our acceleration to a minimum.
- If the path is blocked let's brake and and switch to `stop mode`

### 3B. If we're in `stop` mode, command appropriately
- If we're not yet completely stopped, keep braking.
- If we've completely stopped, check if path is sufficiently clear
- If path is sufficiently clear, turn in place, else switch to `forward mode`

### 3C. If we're `stuck`, let's turn in place

### 4. Pick-up the sample if we could and haven't yet
- If we're not moving and we're near a sample, then let's pick it up

The following image is one where the rover is picking up a rock.
![image](output/Pickup_object.png)

# Known issues and areas for improvements
- There are some cases that the rover gets stuck but isn't detected by our pipeline, investigate these cases and check how to detect them.
- The rover wanders around without considering if it has already traversed that path before. We can develop a smarter method by considering its previous traversed positions and avoid heading back there.
- Needed to change world size and scale to get rover to pick up more rocks but that reduces fidelity.
- The rover cannot go back to it's starting location when it has collected all rock sample. This feature is not implemented!
