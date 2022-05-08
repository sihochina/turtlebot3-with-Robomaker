**使用Robomaker进行机器人强化训练**





强化学习 (RL) 是一种高级机器学习 (ML) 技术，无需任何标记的训练数据即可学习非常复杂的行为，并且可以在针对长期目标进行优化的同时做出短期决策。 

使用 AWS RoboMaker 示例应用程序生成用于 RL 的模拟训练数据。  RL 模型将教机器人跟踪和跟随物体。  这是一个简单的演示，可以扩展到用例，例如仓库中的工人协助或在家中跟随消费者的娱乐机器人。 

在这个Demo中，您将完成构建机器人应用程序的步骤。此应用程序将使用强化学习来训练机器人（以 TurtleBot 3 Waffle Pi 为例）自动驾驶到静止的机器人（以 TurtleBot 3  Burger 为例），在 AWS RoboMaker 模拟中训练和评估强化学习模型，并使用 AWS RoboMaker  车队管理将该模型部署到物理机器人。 

该Demo包含以下内容：

- 创建 S3 存储桶、IAM 角色和策略 
- 使用 AWS Cloud9 设置 AWS RoboMaker 开发环境 
- 使用 AWS RoboMaker 模拟来训练强化学习模型并可视化应用程序 
- 通过仿真评估模型 
- 将模型部署到机器人 

# 1、环境准备

## 1.1 选择Region

该ROS应用程序可以部署在任何支持 AWS RoboMaker 服务的 AWS 区域，此Demo选择法兰克福Region（eu-central-1） 。

## 1.2 创建S3存储桶

在Amazon S3  控制台创建一个存储桶，该存储桶用于存储经过训练的强化学习模型和模拟日志。为存储桶命名为“turtlebot-with-robomaker-176979737281”，其他配置保留默认。

![image-20220509035149903](./turtlebot3_images/image-20220509035149903.png)

## 1.3 创建IAM Role和Policy

1. 创建一个角色以允许 AWS RoboMaker 运行模拟，该角色需要执行以下操作：从 Amazon S3 下载捆绑的档案以创建模拟作业，将经过训练的强化学习模型上传到 Amazon S3，以及在模拟期间将指标和 ROS 日志发布到 Amazon CloudWatch。 

2. 在IAM 控制台，选择创建策略，选择JSON表，复制并粘贴以下策略。  注意需要更改ARN为步骤1.2所创建存储桶的ARN。

   ```json
   {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "cloudwatch:PutMetricData",
                    "ec2:*",
                    "iam:PassRole",
                    "logs:CreateLogGroup",
                    "logs:CreateLogStream",
                    "logs:DescribeLogStreams",
                    "logs:PutLogEvents",
                    "robomaker:*"
                ],
                "Resource": "*"
            },
            {
                "Effect": "Allow",
                "Action": "s3:*",
                "Resource": [
                    "arn:aws:s3:::turtlebot-with-robomaker-176979737281",
                    "arn:aws:s3:::turtlebot-with-robomaker-176979737281/*"
                ]
            }
        ]
    }
   ```

3. 输入策略名称turtlebot-with-robomaker，然后选择创建策略；

4. 在IAM控制台选择创建角色，选择可信实体为Robomaker，并选择用例为RoboMaker - Simulation；

   <img src="./Turtlebot3_images/image-20220509041424206.png" alt="image-20220509041424206" style="zoom:67%;" />

5. 搜索创建的策略turtlebot-with-robomaker，并将其附加到角色，输入角色名称turtlebot-with-robomaker-role，选择创建角色；

# 2、设置Robomaker开发环境

在 AWS RoboMaker 中创建 AWS Cloud9 IDE、下载示例源代码并创建可与 AWS RoboMaker 一起使用的模拟包。 

该捆绑包包含以下内容。 

- 模拟世界 
- TurtleBot 模型 
- 用于运行机器人的逻辑 
- 应用程序需要运行的所有依赖项 

## 2.1 创建开发环境

1. 在AWS Robomaker控制台中选择创建开发环境；

2. 设置开发环境的名称turtlebot-with-robomaker，预安装的ROS软件选择Melodic，实例类型选择c4.xlarge，网络选择默认VPC；

   <img src="./Turtlebot3_images/image-20220509042618279.png" alt="image-20220509042618279" style="zoom:67%;" />

3. 创建过程大概持续几分钟，并且会自动打开Cloud9控制台。





































