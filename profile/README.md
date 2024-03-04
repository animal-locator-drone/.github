# Animal Locator Drone

Drone software for locating animals with a focus on dogs.

## Proposed overall design and implementation

### Design

1. Proposed design diagram
   1. <https://excalidraw.com/#room=7e2e5fed98110a848b6a,OxINdgfSKA8y0GQ-5m-grA>
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
      4. For Colosseum See: <https://github.com/animal-locator-drone/simulation>
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
