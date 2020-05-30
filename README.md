# ROS_TF

What's in the ROS TF

1. Publish and Subscribe to TF data topics
2. Use the tools necessary to visualize TF data
3. Publish fixed TF transforms
4. Use RobotStatePublisher to generate TF data for robots too complex to publish it manually
5. Understand the use of JointStatePublisher and how it relates to RobotMovement Controllers

# ------------------------------------------------------------------------
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

# ------------------------------------------------------------------------
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

In this exercise, you are going to create a new publisher that publishes the TF of a new model, the coke_can. Here is data to help you on this exercise:
1. rosservice call /gazebo/get_world_properties "{}"
2. rosrun your_package multiple_broadcatser.py
3. rosrun rviz rviz
4. rosrun rqt_tf_tree rqt_tf_tree
5. evince frames.pdf


# ------------------------------------------------------------------------

# robot_state_publisher

A. Know how Pi-Robot works

1. roslaunch pi_robot_pkg pi_robot_control_norsp.launch
2. Now let's move the Pi Robot a litle bit.

    a. Terminal #2
        rostopic list | grep command
        rostopic pub /pi_robot/head_tilt_joint_position_controller/command std_msgs/Float64 "data: 0.0"
        rostopic pub -1 /pi_robot/head_pan_joint_position_controller/command std_msgs/Float64 "data: 0.7"
        rosrun rviz rviz

        disini belum ada TF yg terpublish, makannnya RobotModel di RViz banyak errornya
3. The first step is to STOP the controllers that you launched
4. Terminal #1
    roslaunch pi_robot_pkg pi_robot_control.launch
5. Move the robot

        a. Terminal #2
            rostopic list | grep command
            rostopic pub /pi_robot/head_tilt_joint_position_controller/command std_msgs/Float64 "data: 0.0"                
            rostopic pub -1 /pi_robot/head_pan_joint_position_controller/command std_msgs/Float64 "data: 0.7"
            rosrun tf view_frames
            rosrun rqt_tf_tree rqt_tf_tree
        b. Terminal #3
            rostopic echo -n1 /tf
            rosrun tf tf_echo frames.gv frames.pdf
        c. Terminal #4
            rosrun rviz rviz
        
        Nah disini baru TF di RobotModel nya udah ada

        d. Terminal #3 (sebelumnya di ctrl+c dulu)
            rosrun tf tf_echo torso_link upper_base_link



B. Create your own robot_state_publihser launch



