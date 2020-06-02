# Dual Lidar calibration
```bash
source ~/.bashrc
cd catkin_ws/src/
git clone https://github.com/ram-lab/lidar_appearance_calibration.git
```
[ros-kinetic-pcl-ros](https://github.com/uzh-rpg/rpg_svo_example/issues/44)
```bash
sudo apt-get install ros-kinetic-pcl-ros
```
## Warning (openni2)
If some warning about *openni2*, please see [here](https://github.com/autowarefoundation/autoware/issues/1072)

## Modify the code
- calib_evaluation.cpp <br/>
search jjiao --> modify
- calib_preprocess.cpp <br/>
search jjiao --> modify
  
- eigen3랑 pcl-1.8 include할 때 필요한 버전들이 local에 있기 때문에 경로 설정 다시!
  
## cloudcompare
Install [here](https://snapcraft.io/install/cloudcompare/ubuntu)
```bash
sudo apt update
sudo apt install snapd
sudo snap install cloudcompare
```  
  ======================================================================================================
  
  veloview 다운로드 (아마 이거 통해서 csv파일로 바꿔줘야할듯요..
https://www.paraview.org/veloview/
  

----------------------------------------------------------------------------------------------------------
1. Create a yaml file cfg.yaml into a fold, please follow ../data/example/top_tail/cfg.yaml to write

2. Preproces raw pointcloud to keep points with only planes. You can use the below function or CloudCompare software like this: input="pcd" output="csv"

2-a 
bag filte to pcd
```bash
rosrun pcl_ros bag_to_pcd room_left.bag velodyne_points vel_to_pcd/
rosrun pcl_ros bag_to_pcd <input_file>  <topic>         <output_directory>
```

2-b
pcd to csv
```bash
cd Library/pcd2ILCCcsv/bin/
./pcd2csv ~/vel_to_pcd/planes/ref.pcd
./pcd2csv ~/vel_to_pcd/planes/data.pcd
```
Using cloudcompare, set the bounding box range by users.

```bash
rosrun lidar_appearance_calibration calib_preprocess vel_to_pcd/planes/ref_con.yaml vel_to_pcd/planes/data_con.yaml
```

3. Extract planes from pointcloud
terminal 1:
roscore 먼저
terminal 2:
  cd ~/catkin_ws/src/lidar_appearance_calibration/config/
  rosrun lidar_appearance_calibration calib_plane_extraction pcd ../data/example/top_front/cfg.yaml
terminal 3:
실행하고난뒤에 rviz를 켜놓는다(위치**)
  cd ~/catkin_ws/src/lidar_appearance_calibration/config/
  rviz -d ../rviz/plane_extraction.rviz
terminal 4:
터미널 2에서 계산되어 나옴
  rostopic pub /contact/icp std_msgs/String "data: ''" 

Visualize and check the extracted plane order



rosrun pcl_ros pcd_to_pointcloud ../data/example/top_front/plane/ref_planes.pcd

---------------------------------------------------------------------------------
로스백 record
rosbag record -a -O <파일 이름>

로스백 play
rosbag play <파일 이름>.bag

$ rosrun pcl_ros bag_to_pcd room_left.bag velodyne_points vel_to_pcd/
$ rosrun pcl_ros bag_to_pcd <input_file>  <topic>         <output_directory>

----------------------------------------------------------------------
pcd2csv parser 참고
https://github.com/jacoblambert/pcd2ILCCcsv
