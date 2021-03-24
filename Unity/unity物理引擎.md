游戏引擎每秒重新运算60次。也可以由渲染线程运算也是每秒60次。（渲染）

unity物理引擎（物理线程）每秒运算`50`次。（正常应该是游戏引擎运算的两倍，unity太垃圾了）

所以同步有可能照成数据丢失（60次同步50次）

在unity中 渲染线程的是用`update`方法

物理引擎渲染是 `FixedUpdate`方法

在物理引擎中，分为以下三种对象。（为了节能）

static	 静态对象 ，静止的物体（例如墙壁） 物理引擎每秒不渲染它

dynamic	动态对象 （例如 人物 unityChan） 每秒都会被物理引擎渲染50次

kinematic	自动能对象 （任意在哪出现的东西，位置没办法确定的）（设置一个时间点和位置，让他出现这种也是）

在unity中带有RigidBody的东西通通被认为是动态的东西，未携带RigidBody的被认为是静态的东西。

Collider的兄弟层级上有 RigidBody组件，则该 Collider 就被认为是动态Collider，否则就是静态Collider。

例如：

![image-20200825135250056](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200825135250056.png)

则，unityChan就被认为是动态对象。



比如 鬼的话 就是自动能对象，因为物理引擎已经没有办法做了。（它自己会动）



我们可以通过添加组件来使一个物体变成动态的

添加Rigidbody组件 他就变成了动态物体

`Use Gravity` 是否有重力 

`Is	Kinematic` 是是否为 自动能物体

![image-20200825142126659](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200825142126659.png)



#  下面两张表 表示 三种物体 是否有物理碰撞



| ***Collision detection occurs and messages are sent upon collision\*** |                 |                    |                              |                         |                            |                                      |
| :----------------------------------------------------------- | --------------- | ------------------ | ---------------------------- | ----------------------- | -------------------------- | ------------------------------------ |
|                                                              | Static Collider | Rigidbody Collider | Kinematic Rigidbody Collider | Static Trigger Collider | Rigidbody Trigger Collider | Kinematic Rigidbody Trigger Collider |
| Static Collider                                              |                 | Y                  |                              |                         |                            |                                      |
| Rigidbody Collider                                           | Y               | Y                  | Y                            |                         |                            |                                      |
| Kinematic Rigidbody Collider                                 |                 | Y                  |                              |                         |                            |                                      |
| Static Trigger Collider                                      |                 |                    |                              |                         |                            |                                      |
| Rigidbody Trigger Collider                                   |                 |                    |                              |                         |                            |                                      |
| Kinematic Rigidbody Trigger Collider                         |                 |                    |                              |                         |                            |                                      |

| ***Trigger messages are sent upon collision\*** |                 |                    |                              |                         |                            |                                      |
| :---------------------------------------------- | --------------- | ------------------ | ---------------------------- | ----------------------- | -------------------------- | ------------------------------------ |
|                                                 | Static Collider | Rigidbody Collider | Kinematic Rigidbody Collider | Static Trigger Collider | Rigidbody Trigger Collider | Kinematic Rigidbody Trigger Collider |
| Static Collider                                 |                 |                    |                              |                         | Y                          | Y                                    |
| Rigidbody Collider                              |                 |                    |                              | Y                       | Y                          | Y                                    |
| Kinematic Rigidbody Collider                    |                 |                    |                              | Y                       | Y                          | Y                                    |
| Static Trigger Collider                         |                 | Y                  | Y                            |                         | Y                          | Y                                    |
| Rigidbody Trigger Collider                      | Y               | Y                  | Y                            | Y                       | Y                          | Y                                    |
| Kinematic Rigidbody Trigger Collider            | Y               | Y                  | Y                            | Y                       | Y                          | Y                                    |