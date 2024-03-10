# Animal Locator Drone

Drone software for locating animals with a focus on dogs.

## Proposed overall design and implementation

### Design

#### 20240309 Proposed design based on using primarily simulators

- Initial Author: Ziad Arafat

##### 1 Overview

1. In this design we will focus on implementing compoments in a simulation environment rather than having to worry about implementing things around a particular drone setup.
2. Things such as drone connectivity, availablity of a secondary board etc. won't need to be a consideration.
3. However, in order to help make this design portable and adaptable to a wide range of drone setups we will make it modular and non monolithic in nature.
   1. This means we will break up functionality into multiple running services
      1. Dare I say "microservices"
4. Some real challenges we have identified with designing this system.
   1. What is the necessary flow of logic when it comes to identifying dogs and further investigating the found dog?
   2. How do we efficiently stream video data from the cameras and deliver it to multiple endpoints that need it?
   3. Which endpoints need said video and which do not?
   4. In the context of having a physical drone we encountered these challenges which we hope to not deal with in simulated setups.
      1. How do we connect the end user to the drone?
      2. How do we stream video data and to whom given the limitations of connectivity?
5. Updated model for defining and refining the design documentation.
   1. RADIO model
      1. <https://www.greatfrontend.com/system-design/framework>

##### 2 R A D I O

###### 1 **R**equirements Exploration

1. We will maintain a list of functional requirements and non-functional requirements here.
   1. **Functional Requirements** (What does it do for the user?)
      1. **Primary Requirements** (These are essential for an MVP)
         1. The system will help the user quickly locate their lost dog.
            1. The drone can autonomously follow waypoints set by the user.
            2. During flight the user will receive real-time detections of dogs on the ground
               1. A "detection" constitutes the following
                  1. A photo or multiple photos of the detected dog.
                  2. The precise location of the dog.
                  3. A graphical map representation of the location.
                  4. Directions to navigate in person to the location (like google maps).
               2. The user will be able to browse through detections of dogs
               3. For each detection
                  1. The user can indicate if it should be ignored or investigated further.
            3. For each detection that is marked for further investigation.
               1. The drone will autonomously track the dog and attempt to get a better view of the dog.
               2. As the drone is doing this
                  1. The user can cancel at any time to continue the search for other dogs.
                  2. The user can indicate that this is the correct dog and begin efforts of recovery.
            4. When the user has begun recovery efforts the drone can be used to continue tracking the location of the found dog until recovered.
   2. **Non-functional requirements** (Constraints and general operational requirements)
      1. The UI needs to be user friendly.
      2. Drones need to be operated safely and in accordance to laws and FAA regulations.
      3. One intended use case is for there to be a community drone that can be operated for a community to help locate lost dogs.
         1. Therefore it's important to consider the cost and easy of setup for this use case.
2. We will explore and discuss requirements by making change requests to this document and referencing by number.
   1. To do this
      1. Open a github issue on this repo
      2. Indicate which requirement should be changed or added or removed
      3. Can be references to multiple if they are in the same scope of the change.


#### First Proposed Design

1. Proposed design diagram
   1. <https://excalidraw.com/#room=7e2e5fed98110a848b6a,OxINdgfSKA8y0GQ-5m-grA>
![Alt text](https://github.com/animal-locator-drone/.github/blob/main/proposed_design_diagram.svg)
2. The overall design is as follows.
   1. The goal is for a user to be able to use a drone that will fly over a large area of ground autonomously and try to locate dogs in real time.
   2. Drone
      1. The drone will be equipped with sensors and computers to be able to autonomously fly over a path and detect dogs on the ground.
      2. Flight Controller
         1. The flight controller will manage autonomous flight and following preset waypoints.
      3. Secondary Board
         1. A secondary compute board on the drone will take care of processing and transmitting data including.
            1. Processing input video/image data from cameras  
            2. Looking for pets in video/image data using AI
            3. Transmitting data back to the user
            4. Processing user input from the user to command other components.
         2. Web App
            1. The web app will provide an interface for the user app to communicate with the drone and also receive data from the drone.
         3. Database
            1. The database will store any persistent data the system needs.
         4. dog detector service
            1. This component will take images/videos as input and provide useful data about found dogs.
         5. Flight Service
            1. The flight service will talk to the Flight Controller to command missions and receive telemetry data needed for the user.
   3. User device
      1. Phone or tablet running an app that will interface with the web app to allow the user to
         1. Select a flight mission and begin missions.
         2. Customize settings for searching for dogs.
         3. See the current status of the mission
         4. See images of dogs and generally what the drone sees  
   4. Ground Pilot Device
      1. The ground pilot device will allow a pilot to monitor and take emergency manual control of the drone.
      2. This is a requirement by the FAA for all autonomous drones and a critical safety measure.

### Implementation

1. Drone platform
   1. Simulated drone
      1. For the drone platform itself we will start with a simulated drone using the colosseum simulator and possible Gazebo.
         1. <https://github.com/CodexLabsLLC/Colosseum>
         2. <https://microsoft.github.io/AirSim/>
         3. <https://gazebosim.org/home>
      2. Flight Controller
         1. For the flight controller we will use PX4 in SITL mode
            1. <https://px4.io>
   2. Physical Drone
      1. For the physical drone we plan to use a basic drone frame kit with a classic pixhawk flight controller capable of running PX4
         1. Currently we are looking at the Holybro X500 v2
            1. <https://www.getfpv.com/holybro-x500-v2-arf-kit.html?utm_source=google&utm_medium=cpc&utm_campaign=DM+-+NB+-+PMax+-+Shop+-+SM+-+ALL+%7C+Full+Funnel&utm_content=pmax_x&utm_keyword=&utm_matchtype=&campaign_id=17881616054&network=x&device=c&gc_id=17881616054&gad_source=1&gclid=CjwKCAiAq4KuBhA6EiwArMAw1KnzoqzcRcEUzLdYfhrSO0TbaQLhCj82WURUPezR6SqhG48fqDwV6RoClL4QAvD_BwE>
         2. We have 2 pixhawk controllers available already.
      2. Controller
         1. We are currently looking into a Herelink controller from ETV otherwise we will need to do more research
2. Secondary Board Components
   1. The Board itself
      1. Currently we are looking into either a Raspberry pi or to borrow an Nvidia Jetson Nano board from ETV
   2. Web App
      1. See <https://github.com/animal-locator-drone/dogfinder-backend/wiki/Proposed-Design-and-Implementation>
   3. Database
      1. PostgreSQL or SQLite but we are undecided.
   4. Dog detector service
      1. No implementation plan yet
      2. Ideas
         1. OpenCV
         2. YOLOv8
         3. Python
   5. Flight Service
      1. No implementation plan yet
      2. Ideas
         1. PyMavSDK
         2. Python
         3. FastAPI
3. User Device
   1. Device itself
      1. No plan yet but anticipate a laptop or tablet or phone
   2. Frontend App
      1. See: <https://github.com/animal-locator-drone/dogfinder-app/wiki/Proposed-Solution>
