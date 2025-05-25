# ros2
ลิ้งก์ที่ต้อง ดาวน์โหลดไฟล์ไว้
1. Ubuntu22
https://releases.ubuntu.com/jammy/ubuntu-22.04.5-desktop-amd64.iso

2. VBOX
https://download.virtualbox.org/virtualbox/7.1.6/VirtualBox-7.1.6-167084-Win.exe

3. Vbox Extension
https://download.virtualbox.org/virtualbox/7.1.6/Oracle_VirtualBox_Extension_Pack-7.1.6.vbox-extpack

4. File image vbox(.ova)
https://drive.google.com/file/d/1d00gRHT4OWJcUJvdwqY9dRCrOsN3UBwk/view?usp=sharing

sudo apt update

sudo apt install terminator -y

ตั้งค่า Terminator ให้สะดวก
Ctrl + Shift + U เพื่อใช้งานโหมด Unicode เช่น การพิมพ์สัญลักษณ์พิเศษ
Ctrl + Shift + E เพื่อแบ่งหน้าต่างแนวนอน
Ctrl + Shift + O เพื่อแบ่งหน้าต่างแนวตั้ง
Ctrl + Tab เพื่อสลับระหว่างหน้าต่าง

https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debs.html

locale  # check for UTF-8

sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

locale  # verify settings

sudo apt update
sudo apt upgrade

sudo apt install ros-humble-desktop

sudo apt install ros-dev-tools

sudo gedit ~/.bashrc
sudo nano ~/.bashrc

source /opt/ros/humble/setup.bash

https://shorturl.asia/QexnH

สร้าง ros2 workspece
mkdir ros2_ws
cd ros2_ws
mkdir src
colcon build
ย้อนกลับไปที่ ros2_ws
cd ..
sudo gedit ~/.bashrc
sudo gedit ~/.bashrc

add last 2 line

source /opt/ros/humble/setup.bash
source ~/ros2_ws/install/setup.bash

ไปที่ ros2_ws/src
ros2 pkg create my_pkg --build-type ament_python --dependencies rclpy

touch node1.py

please act as ROS2 humble expert in python development code.
create node1.py that publishes a counting number from 99 to 0 every second.
this node name “node_one” pub int data to topic name “box”
show me the python code and how to setup the setup.py in my_pkg under ros2_ws/src
import rclpy
from rclpy.node import Node
from std_msgs.msg import Int32

class NodeOne(Node):
	def __init__(self):
    	super().__init__('node_one')
    	self.publisher_ = self.create_publisher(Int32, 'box', 10)
    	self.count = 99
    	self.timer = self.create_timer(1.0, self.timer_callback)

	def timer_callback(self):
    	if self.count >= 0:
        	msg = Int32()
        	msg.data = self.count
        	self.publisher_.publish(msg)
        	self.get_logger().info(f'Publishing: {self.count}')
        	self.count -= 1
    	else:
        	self.get_logger().info('Done counting.')

def main(args=None):
	rclpy.init(args=args)
	node = NodeOne()
	rclpy.spin(node)
	node.destroy_node()
	rclpy.shutdown()

if __name__ == '__main__':
	main()



entry_points={
    	'console_scripts': [
        	'node_one = my_pkg.node1:main',
    	],
	},

=========================Build again================

cd ros2_ws
colcon build

ros2 run my_pkg node_one

ทดสอบเต็มระบบ
ros2 run my_pkg node_one
ros2 topic list
ros2 topic echo /box

touch node2.py



# sub_mqtt.py
import paho.mqtt.client as mqtt

# Callback เมื่อต่อกับ broker สำเร็จ
def on_connect_fn(client, userdata, flags, rc):
    print("✅ Connected with result code", rc)
    client.subscribe("test/topic")  # สามารถเปลี่ยน topic ได้

# Callback เมื่อมี message เข้ามา
def on_message_fn(client, userdata, msg):
    print(f"📩 Received on [{msg.topic}]: {msg.payload.decode()}")

# สร้าง client
client = mqtt.Client()

# ผูก callback
client.on_connect = on_connect_fn
client.on_message = on_message_fn

# เชื่อมต่อกับ broker
client.connect("localhost", 1883, 60)

# เริ่มรับข้อความ
client.loop_forever()


curl -sSL https://ngrok-agent.s3.amazonaws.com/ngrok.asc \
  | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null \
  && echo "deb https://ngrok-agent.s3.amazonaws.com buster main" \
  | sudo tee /etc/apt/sources.list.d/ngrok.list \
  && sudo apt update \
  && sudo apt install ngrok


File Edit View
-Install Arduino
-Install CP210X, 340
-Install Board ESP32 in Arduino
-Install Lib "PubSubClient" by nick
-Install Arduino


ros2 pkg create mqtt_pkg --build-type ament_python --dependencies rclpy std_msgs

🧭 บทเรียน: เริ่มต้นใช้งาน ROS 2 ด้วย Python
🔧 ขั้นตอนที่ 1: สร้าง ROS 2 Workspace และ Package
เปิดเทอร์มินัลและสร้าง workspace ใหม่:

bash
คัดลอก
แก้ไข
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src
สร้างแพ็กเกจชื่อ mqtt_pkg ที่ใช้ ament_python:

bash
คัดลอก
แก้ไข
ros2 pkg create mqtt_pkg --build-type ament_python --dependencies rclpy std_msgs
คำสั่งนี้จะสร้างโครงสร้างพื้นฐานของแพ็กเกจในโฟลเดอร์ mqtt_pkg

🧠 ขั้นตอนที่ 2: สร้าง Node สำหรับ MQTT Publisher
สร้างไฟล์ pub_node.py ภายในโฟลเดอร์ mqtt_pkg/mqtt_pkg/:

bash
คัดลอก
แก้ไข
cd ~/ros2_ws/src/mqtt_pkg/mqtt_pkg
touch pub_node.py
แก้ไขไฟล์ pub_node.py ด้วยเนื้อหาต่อไปนี้:

python
คัดลอก
แก้ไข
#!/usr/bin/env python3

import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class MqttPublisher(Node):
    def __init__(self):
        super().__init__('mqtt_publisher')
        self.publisher_ = self.create_publisher(String, 'mqtt_topic', 10)
        timer_period = 1.0  # วินาที
        self.timer = self.create_timer(timer_period, self.timer_callback)
        self.count = 0

    def timer_callback(self):
        msg = String()
        msg.data = f'Hello MQTT: {self.count}'
        self.publisher_.publish(msg)
        self.get_logger().info(f'Publishing: "{msg.data}"')
        self.count += 1

def main(args=None):
    rclpy.init(args=args)
    mqtt_publisher = MqttPublisher()
    rclpy.spin(mqtt_publisher)
    mqtt_publisher.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
ทำให้ไฟล์สามารถรันได้:

bash
คัดลอก
แก้ไข
chmod +x pub_node.py
🛠️ ขั้นตอนที่ 3: แก้ไขไฟล์ setup.py และ package.xml
แก้ไขไฟล์ setup.py ในโฟลเดอร์ mqtt_pkg ให้มีเนื้อหาดังนี้:

python
คัดลอก
แก้ไข
from setuptools import setup

package_name = 'mqtt_pkg'

setup(
    name=package_name,
    version='0.0.0',
    packages=[package_name],
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
    ],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='innovedex',
    maintainer_email='innovedex@todo.todo',
    description='MQTT Publisher Package',
    license='TODO: License declaration',
    tests_require=['pytest'],
    entry_points={
        'console_scripts': [
            'pub_node = mqtt_pkg.pub_node:main',
        ],
    },
)
หมายเหตุ: ตรวจสอบให้แน่ใจว่าไม่มีเครื่องหมายพิเศษหรืออักขระที่ไม่ถูกต้องในไฟล์ setup.py ซึ่งอาจทำให้เกิดข้อผิดพลาดในการคอมไพล์

แก้ไขไฟล์ package.xml ให้รวม dependencies ที่จำเป็น:

xml
คัดลอก
แก้ไข
<?xml version="1.0"?>
<package format="2">
  <name>mqtt_pkg</name>
  <version>0.0.0</version>
  <description>MQTT Publisher Package</description>
  <maintainer email="innovedex@todo.todo">innovedex</maintainer>
  <license>TODO: License declaration</license>

  <buildtool_depend>ament_python</buildtool_depend>

  <depend>rclpy</depend>
  <depend>std_msgs</depend>

  <exec_depend>python3</exec_depend>

  <export>
    <build_type>ament_python</build_type>
  </export>
</package>
🔨 ขั้นตอนที่ 4: สร้างและติดตั้งแพ็กเกจ
กลับไปที่ root ของ workspace:

bash
คัดลอก
แก้ไข
cd ~/ros2_ws
สร้างแพ็กเกจด้วย colcon:

bash
คัดลอก
แก้ไข
colcon build --packages-select mqtt_pkg
แหล่งที่มาของ workspace:

bash
คัดลอก
แก้ไข
source install/setup.bash
🚀 ขั้นตอนที่ 5: รัน Node
รัน Node ที่สร้างขึ้น:

bash
คัดลอก
แก้ไข
ros2 run mqtt_pkg pub_node
คุณควรเห็นข้อความที่ถูกพิมพ์ออกมาทุกวินาทีในเทอร์มินัล

หากคุณต้องการขยายบทเรียนเพิ่มเติม เช่น การสร้าง Subscriber, การใช้งานกับเซ็นเซอร์, หรือการเชื่อมต่อกับ MQTT Broker ภายนอก โปรดแจ้งให้ทราบครับ ผมยินดีที่จะช่วยเสริมเนื้อหาให้ครบถ้วนยิ่งขึ้น!
