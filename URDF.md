# Introduction to URDF and building a two wheeled robot from scratch

 - The syntax will be similar to html syntax since XML is a markup language similar to html
 - We will write a .xacro file which stands for "XML macro" language instead of a .urdf file. Reason :Think Why?( ~Google why~)

### Some Prerequisites to be known /satisfied before moving further:
#### Installing git
```
sudo apt-get install git
```
#### Checking if RViz is working properly
 1) Open a terminal and type "roscore"
 2) Then...
```
rosrun rviz rviz
```
#### Checking if Gazebo is working properly
```
roslaunch gazebo_ros empty_world.launch
```
#### Command for Gaining keyboard control
```
rosrun teleop_twist_keyboard teleop_twist_keyboard.py 
```
#### Creating the workspace
   1) Create a folder with a name of your choice .I am naming it "Shaastra_Workshop"
   2) Type the following code
```
mkdir Shaastra_Workshop
cd Shaastra_Workshop
mkdir catkin_ws
cd catkin_ws
mkdir src
catkin_make

cd src
catkin_create_pkg two_wheeled_robot
cd two_wheeled_robot
mkdir urdf
mkdrir launch
cd urdf

catkin_make(For safety)
```

### Some of the Tags that we will be using in our code:

```
<robot>
<material>
<color>
<gazebo>
<sensor>
<inertia>
<link>
<joint>
<collision>
..... and many more .......
```

## Lets dive into the code

### Modelling the ROBOT

 **1)Include \<xml\> and \<robot\> tag**
```
<?xml version="1.0" ?>
<robot name="robot" xmlns:xacro="http://www.ros.org/wiki/xacro">
</robot>
```

 **2)Create a chassis for the robot**
 ```
 <link name="link_chassis">
    <!-- pose and inertial -->
    <pose>0 0 0.1 0 0 0</pose>
    <inertial>
      <mass value="5"/>
      <origin rpy="0 0 0" xyz="0 0 0.1"/>
      <inertia ixx="0.0395416666667" ixy="0" ixz="0" iyy="0.106208333333" iyz="0" izz="0.106208333333"/>
    </inertial>
    <!-- body -->
    <collision name="collision_chassis">
      <geometry>
        <box size="0.5 0.3 0.07"/>
      </geometry>
    </collision>
    <visual>
      <origin rpy="0 0 0" xyz="0 0 0"/>
      <geometry>
        <box size="0.5 0.3 0.07"/>
      </geometry>
      <material name="blue"/>
    </visual>
    <!-- caster front -->
    <collision name="caster_front_collision">
      <origin rpy=" 0 0 0" xyz="0.35 0 -0.05"/>
      <geometry>
        <sphere radius="0.05"/>
      </geometry>
      <surface>
        <friction>
          <ode>
            <mu>0</mu>
            <mu2>0</mu2>
            <slip1>1.0</slip1>
            <slip2>1.0</slip2>
          </ode>
        </friction>
      </surface>
    </collision>
    <visual name="caster_front_visual">
      <origin rpy=" 0 0 0" xyz="0.2 0 -0.05"/>
      <geometry>
        <sphere radius="0.05"/>
      </geometry>
    </visual>
  </link>
 ```
 **3)Create wheels for the robot** \
   a) Link
   ```
   <link name="link_right_wheel">
    <inertial>
      <mass value="0.2"/>
      <origin rpy="0 1.5707 1.5707" xyz="0 0 0"/>
      <inertia ixx="0.000526666666667" ixy="0" ixz="0" iyy="0.000526666666667" iyz="0" izz="0.001"/>
    </inertial>
    <collision name="link_right_wheel_collision">
      <origin rpy="0 1.5707 1.5707" xyz="0 0 0"/>
      <geometry>
        <cylinder length="0.04" radius="0.1"/>
      </geometry>
    </collision>
    <visual name="link_right_wheel_visual">
      <origin rpy="0 1.5707 1.5707" xyz="0 0 0"/>
      <geometry>
        <cylinder length="0.04" radius="0.1"/>
      </geometry>
    </visual>
  </link>
   ``` 
   b) Joint:
   ```
   <joint name="joint_right_wheel" type="continuous">
    <origin rpy="0 0 0" xyz="-0.05 0.15 0"/>
    <child link="link_right_wheel"/>
    <parent link="link_chassis"/>
    <axis rpy="0 0 0" xyz="0 1 0"/>
    <limit effort="10000" velocity="1000"/>
    <joint_properties damping="1.0" friction="1.0"/>
  </joint>
   ```
   Now similarly change names and values to get the Left wheel and left joint
   
   ```
   <link name="link_left_wheel">
    <inertial>
      <mass value="0.2"/>
      <origin rpy="0 1.5707 1.5707" xyz="0 0 0"/>
      <inertia ixx="0.000526666666667" ixy="0" ixz="0" iyy="0.000526666666667" iyz="0" izz="0.001"/>
    </inertial>
    <collision name="link_left_wheel_collision">
      <origin rpy="0 1.5707 1.5707" xyz="0 0 0"/>
      <geometry>
        <cylinder length="0.04" radius="0.1"/>
      </geometry>
    </collision>
    <visual name="link_left_wheel_visual">
      <origin rpy="0 1.5707 1.5707" xyz="0 0 0"/>
      <geometry>
        <cylinder length="0.04" radius="0.1"/>
      </geometry>
    </visual>
  </link>
  
  <joint name="joint_left_wheel" type="continuous">
    <origin rpy="0 0 0" xyz="-0.05 -0.15 0"/>
    <child link="link_left_wheel"/>
    <parent link="link_chassis"/>
    <axis rpy="0 0 0" xyz="0 1 0"/>
    <limit effort="10000" velocity="1000"/>
    <joint_properties damping="1.0" friction="1.0"/>
  </joint>
   ```
   **4)Adding Laser scanner**
   ```
   <!--LASER-->
<link name="sensor_laser">
    <inertial>
      <origin xyz="0 0 0" rpy="0 0 0" />
      <mass value="1" />
      <inertia ixx="0.02" ixy="0" ixz="0" iyy="0.02" iyz="0" izz="0.02"/>
      </inertial>

    <visual>
      <origin xyz="0 0 0" rpy="0 0 0" />
      <geometry>
        <cylinder radius="0.05" length="0.1"/>
      </geometry>
      <material name="white" />
    </visual>

    <collision>
      <origin xyz="0 0 0" rpy="0 0 0"/>
      <geometry>
        <cylinder radius="0.05" length="0.1"/>
      </geometry>
    </collision>
  </link>

  <joint name="joint_sensor_laser" type="fixed">
    <origin xyz="0.15 0 0.05" rpy="0 0 0"/>
    <parent link="link_chassis"/>
    <child link="sensor_laser"/>
  </joint>
   ```
   
   **5)Adding Materials**
   ```
   <material name="black">
    <color rgba="0.0 0.0 0.0 1.0"/>
  </material>
  <material name="blue">
    <color rgba="0.203125 0.23828125 0.28515625 1.0"/>
  </material>
  <material name="orange">
    <color rgba="1.0 0.423529411765 0.0392156862745 1.0"/>
  </material>
  <material name="red">
    <color rgba="0.80078125 0.12890625 0.1328125 1.0"/>
  </material>
  <material name="white">
    <color rgba="1.0 1.0 1.0 1.0"/>
  </material>
   ```
   **6) Adding plugins necessary for working in gazebo**(Readily available  from gazebo documentation)
 - Materials
``` <gazebo reference="link_chassis">
    <material>Gazebo/White</material>
  </gazebo>
  <gazebo reference="link_left_wheel">
    <material>Gazebo/Red</material>
  </gazebo>
  <gazebo reference="link_right_wheel">
    <material>Gazebo/Red</material>
  </gazebo>
 ```
  - Differential drive controller
 ```
 <gazebo>
    <plugin filename="libgazebo_ros_diff_drive.so" name="differential_drive_controller">
      <legacyMode>false</legacyMode>
      <alwaysOn>true</alwaysOn>
      <updateRate>20</updateRate>
      <leftJoint>joint_left_wheel</leftJoint>
      <rightJoint>joint_right_wheel</rightJoint>
      <wheelSeparation>0.2</wheelSeparation>
      <wheelDiameter>0.2</wheelDiameter>
      <torque>0.1</torque>
      <commandTopic>cmd_vel</commandTopic>
      <odometryTopic>odom</odometryTopic>
      <odometryFrame>odom</odometryFrame>
      <robotBaseFrame>link_chassis</robotBaseFrame>
    </plugin>
  </gazebo>
 ```
 - For Laser scanner
 ```
 <gazebo reference="sensor_laser">
    <sensor type="ray" name="head_hokuyo_sensor">
      <pose>0 0 0 0 0 0</pose>
      <visualize>false</visualize>
      <update_rate>20</update_rate>
      <ray>
        <scan>
          <horizontal>
            <samples>720</samples>
            <resolution>1</resolution>
            <min_angle>-1.570796</min_angle>
            <max_angle>1.570796</max_angle>
          </horizontal>
        </scan>
        <range>
          <min>0.10</min>
          <max>10.0</max>
          <resolution>0.01</resolution>
        </range>
        <noise>
          <type>gaussian</type>
          <mean>0.0</mean>
          <stddev>0.01</stddev>
        </noise>
      </ray>
      <plugin name="gazebo_ros_head_hokuyo_controller" filename="libgazebo_ros_laser.so">
        <topicName>/robot/laser/scan</topicName>
        <frameName>sensor_laser</frameName>
      </plugin>
    </sensor>
  </gazebo>
 ```
 ### Launching the ROBOT
 
  #### For launching it in Gazebo:
  - Create a file named spawn.launch inside launch folder
  - Open it
  ```
  <?xml version="1.0" encoding="UTF-8"?>
<launch>
    <include file="$(find gazebo_ros)/launch/empty_world.launch">
    </include>
    <param name="robot_description" command="cat '$(find two_wheeled_robot)/urdf/robot.xacro'"/>

    <arg name="x" default="0"/>
    <arg name="y" default="0"/>
    <arg name="z" default="0.5"/>

    <node name="robot_spawn" pkg="gazebo_ros" type="spawn_model" output="screen"
        args="-urdf -param robot_description -model robot -x $(arg x) -y $(arg y) -z $(arg z)" />

</launch>
  ```
  
 #### For launching it in RVIZ:
  - Create a file named rviz.launch inside launch folder
  - Open it 
  ```
  <?xml version="1.0"?>
<launch>

  <param name="robot_description" command="cat '$(find two_wheeled_robot)/urdf/robot.xacro'"/>

  <!-- send fake joint values -->
  <node name="joint_state_publisher" pkg="joint_state_publisher" type="joint_state_publisher">
    <param name="use_gui" value="False"/>
  </node>

  <!-- Combine joint values -->
  <node name="robot_state_publisher" pkg="robot_state_publisher" type="robot_state_publisher"/>

  <!-- Show in Rviz   -->
  <node name="rviz" pkg="rviz" type="rviz" />

</launch>
  ```
 
 For launching:
 ```
 roslaunch two_wheeled_robot <launch-file>
 ```
