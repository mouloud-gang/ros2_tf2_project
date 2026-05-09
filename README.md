source /opt/ros/jazzy/setup.bash

source ~/ros2_ws/install/setup.bash

cd ~/ros2_ws/src

ros2 pkg create --build-type ament_python tf2_pkg

cd tf2_pkg/tf2_pkg

nano rotating_tf.py


    import rclpy
    import math

    from rclpy.node import Node

    from tf2_ros import TransformBroadcaster

    from geometry_msgs.msg import TransformStamped

    import tf_transformations


    class RotatingTF(Node):

    def __init__(self):

        super().__init__('rotating_tf')

        self.br = TransformBroadcaster(self)

        self.angle = 0.0

        self.create_timer(
            0.05,
            self.broadcast
        )

    def broadcast(self):

        t = TransformStamped()

        t.header.stamp = self.get_clock().now().to_msg()

        t.header.frame_id = 'world'

        t.child_frame_id = 'robot'

        t.transform.translation.x = 1.0

        self.angle += 0.5 * 0.05

        q = tf_transformations.quaternion_from_euler(
            0,
            0,
            self.angle
        )

        t.transform.rotation.x = q[0]
        t.transform.rotation.y = q[1]
        t.transform.rotation.z = q[2]
        t.transform.rotation.w = q[3]

        self.br.sendTransform(t)


    def main(args=None):

    rclpy.init(args=args)

    node = RotatingTF()

    rclpy.spin(node)

    rclpy.shutdown()
-------------------
chmod +x rotating_tf.py

cd ~/ros2_ws/src/tf2_pkg

nano setup.py

----remplace dans:
      
    entry_points={

    'console_scripts': [
    
----avec:

    entry_points={
   
    'console_scripts': [
        'rotating_tf = tf2_pkg.rotating_tf:main',
    ],
    },
-----------------------
nano package.xml
----ajoute avant test depend:

`<depend>rclpy</depend>`
`<depend>geometry_msgs</depend>`
`<depend>tf2_ros</depend>`

---------------------------

sudo apt install ros-jazzy-tf-transformations

cd ~/ros2_ws


source install/setup.bash

ros2 run tf2_pkg rotating_tf

----pas de output

----dans un 2eme terminal:

source /opt/ros/jazzy/setup.bash

source ~/ros2_ws/install/setup.bash

ros2 run tf2_ros tf2_echo world robot

-resultat: 

- Translation: [1.000, 0.000, 0.000]
- Rotation: in Quaternion (xyzw) [0.000, 0.000, -0.554, 0.832]
- Rotation: in RPY (radian) [0.000, 0.000, -1.175]
- Rotation: in RPY (degree) [0.000, 0.000, -67.297]
- Matrix:
- 
  0.386  0.923  0.000  1.000
  
 -0.923  0.386 -0.000  0.000
 
 -0.000  0.000  1.000  0.000
 
  0.000  0.000  0.000  1.000
  
At time 1778343184.87549310
- Translation: [1.000, 0.000, 0.000]
- Rotation: in Quaternion (xyzw) [0.000, 0.000, -0.366, 0.931]
- Rotation: in RPY (radian) [0.000, 0.000, -0.750]
- Rotation: in RPY (degree) [0.000, 0.000, -42.946]
- Matrix:
- 
  0.732  0.681  0.000  1.000
  
 -0.681  0.732 -0.000  0.000
 
 -0.000  0.000  1.000  0.000
 
  0.000  0.000  0.000  1.000
  
At time 1778343185.71985873
- Translation: [1.000, 0.000, 0.000]
- Rotation: in Quaternion (xyzw) [0.000, 0.000, -0.124, 0.992]
- Rotation: in RPY (radian) [0.000, 0.000, -0.250]
- Rotation: in RPY (degree) [0.000, 0.000, -14.299]
- Matrix:
-
  0.969  0.247  0.000  1.000

 -0.247  0.969 -0.000  0.000
 
 -0.000  0.000  1.000  0.000
 
  0.000  0.000  0.000  1.000

if __name__ == '__main__':
    main()
