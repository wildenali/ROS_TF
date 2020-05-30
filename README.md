# ROS_TF

What's in the ROS TF

1. Publish and Subscribe to TF data topics
2. Use the tools necessary to visualize TF data
3. Publish fixed TF transforms
4. Use RobotStatePublisher to generate TF data for robots too complex to publish it manually
5. Understand the use of JointStatePublisher and how it relates to RobotMovement Controllers

# --------------------------------------
# TF Basic

1. Terminal #1
    roslaunch turtle_tf_3d irobot_follow_turtle.launch

2. Terminal #2
    roslaunch turtle_tf_3d turtle_keyboard_move.launch

3. View_frames:
    a. roslaunch turtle_tf_3d irobot_follow_turtle.launch
    b. roscd
       cd ../src
       rosrun tf view_frames
    c. evince frames.pdf
        Their common parent is the /world frame. It also gives you some extra information, like:

        Broadcaster: That's the name of the broadcaster of the TF data.
        The average rate of publication; in this case, around 5.2Hz.
        Most recent transform number and how old it is.
        How much data the TF buffer has stored; in this case, 4.8 seconds of data.

4. rqt_tf_tree:
    rqt_tf_tree gives the same functionality as the view_frames, with an interesting extra: you can refresh and see changes without having to generate another PDF file each time.

    This is very useful when testing new TF broadcasts, and also for checking if what you are using is still publishing or if it's an old publication.
    
        a. Terminal #1
            rosrun rqt_tf_tree rqt_tf_tree
        b. Terminal #2
            roslaunch turtle_tf_3d irobot_follow_turtle.launch

5. tf_echo
    In this exercise, you are going to see how to echo the **/tf** topic and how to use the **tf_echo** tool to filter the /tf topic data.
    a. Terminal #1
        roslaunch turtle_tf_3d irobot_follow_turtle.launch
    b. Terminal #2
        rostopic echo -n1 /tf
        rostopic echo -n2 /tf
        rostopic echo -n3 /tf

6. RVIZ magis
    In this exercise, you are going to represent the /tf topic data in 3D space and see how it changes when moving around the turtle.
    a. Terminal #1
        roslaunch turtle_tf_3d irobot_follow_turtle.launch
    b. Terminal #2
        roslaunch turtle_tf_3d turtle_keyboard_move.launch
    c. Terminal #3
        rosrun rviz rviz
    
    Then, add a TF element and select the Fixed Frame /world.

# -------------------------------------
# TF Publisher

Exercise 2.1
1. catkin_create_pkg your_package rospy turtle_tf_3d
2. Create a python script multiple_broadcaster.py
3. rosrun your_package multiple_broadcatser.py
4. roslaunch turtle_tf_3d run_turtle_tf_listener.launch
    a. Terminal #3
        rostopic echo -n1 /tf
        rostopic echo -n2 /tf
        rostopic echo -n3 /tf
    b. Terminal #3
        rosrun rqt_tf_tree rqt_tf_tree
    c. Terminal #4
        rosrun rviz rviz

If all went well, the iRobot should be trying to follow the turtle now.

Exercise 2.2

# ------------------------------------------------------------------------------
