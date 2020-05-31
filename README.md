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

1. Create pi_robot_rst.launch launch file
2. Something happed

        a. Terminal #2
            rosrun rqt_tf_tree rqt_tf_tree
            Lihat di Layar, muncul sesuatu
        b. Terminal #3
            roscd your_package
            rosrun tf view_frames
            evince frames.pdf
        c. Terminal #4
            rosrun rviz rviz

As you can see in the tf_tree, the links are not connected. You just see some of them connected. Why does this happen?

Let's have a sneak peek into the URDF file that defines the Pi-Robot, which we know plays a major role on the robot_state_publisher node. For that, you will copy the URDF file to your catkin_ws/src in order to be able to visualize it properly through the IDE:

4. Terminal #3
    roscp pi_robot_pkg pi_robot_v2.urdf /home/user/catkin_ws/src/ROS_TF


# ------------------------------------------------------------------------

# joint_state_publisher

1. Create pi_robot_rst_jst.launch file to run robot_state_publisher and the joint_state_publisher
2. Terminal #1
    roslaunch your_package pi_robot_rst_jst.launch
3. Terminal #2
    rosrun rqt_tf_tree rqt_tf_tree
    Disini baru kelihatan kan, udah nyabung semua
4. But wait... what about the robot in the simulation? It's not moving. In fact, maybe the arms are still wandering around freely. Why?

    Although the joint state values are published, and therefore, the TFs can be calculated, these are just internal variables of ROS. Nothing here is telling the arms to move. It would be exactly the same on a real robot. If you had a way to publish the encoder values into your system and change them, it wouldn't mean the real robot would move.
    And here is where ros_control comes into play.
5. Ubah isi dari file pi_robot_v2.urd

        <joint name="left_shoulder_forward_joint" type="revolute">
            <parent link="left_shoulder_link"/>
            <child link="left_shoulder_forward_link"/>
            <origin xyz="0 0.025 0" rpy="0 0 0"/>
            <limit lower="-1.57" upper="1.57" effort="10" velocity="3"/>
            <axis xyz="0 0 1"/>
            <dynamics damping="0.7"/>
        </joint>

        <transmission name="tran4">
            <type>transmission_interface/SimpleTransmission</type>
            <joint name="left_shoulder_forward_joint">
                <hardwareInterface>EffortJointInterface</hardwareInterface>
            </joint>
            <actuator name="motor4">
                <hardwareInterface>EffortJointInterface</hardwareInterface>
                <mechanicalReduction>1</mechanicalReduction>
            </actuator>
        </transmission>

    If the joint is defined as revolute, but the limits are 3.14 and -3.14, then change them to continuous. Otherwise, it won't work
6. Define the new transmission controller with the name xxx_position_controller (left_shoulder_forward_joint_position_controller) in a configuration yaml file
7. roslaunch your_package pi_robot_rst_jst.launch
8. roslaunch pi_robot_pkg pi_robot_control.launch (ini uda ada di dalem kumpulan package nya)
9. rosrun rqt_gui rqt_gui
10. From the Plugins menu, add the Topics->Message Publisher. Then add, for instance, the following topic:
    /pi_robot/left_elbow_joint_position_controller/command
11. rostopic echo /pi_robot/joint_states