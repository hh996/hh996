- 👋 Hi, I’m @hh996
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
hh996/hh996 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
输入统计项：CA_NAS R,W

处理统计项：split

输入数据列（坐标从0开始）：0 1

pwd获取路径

ls获取当前节点

file_path = os.path.join(directory, filename)

执行命令，获取结果

每行结果split，存入data，存入all_data

画单个图

画总图

```Python
import os
import pandas as pd
import matplotlib.pyplot as plt

# 假设所有文件都在同一个目录下，并且是CSV格式
directory = 'path_to_your_directory'  # 替换为你的文件目录
file_extension = '.csv'  # 文件扩展名
all_data = pd.DataFrame()  # 初始化一个空的DataFrame用于聚合所有文件的数据

# 遍历目录中的所有文件
for filename in os.listdir(directory):
    if filename.endswith(file_extension):
        # 构建完整的文件路径
        file_path = os.path.join(directory, filename)
        
        # 读取文件数据
        data = pd.read_csv(file_path)
        
        # 为当前文件的数据绘制图表
        plt.figure()
        plt.plot(data)  # 根据你的数据结构，可能需要指定列名，例如 plt.plot(data['column_name'])
        plt.title(f'图表：{filename}')
        plt.xlabel('X轴标签')  # 替换为你的X轴标签
        plt.ylabel('Y轴标签')  # 替换为你的Y轴标签
        plt.savefig(f'{filename}.png')  # 保存图表
        plt.close()  # 关闭图表，防止重叠
        
        # 将当前文件的数据追加到all_data中
        all_data = all_data.append(data, ignore_index=True)

# 绘制所有文件聚合后的总图表
plt.figure()
plt.plot(all_data)  # 根据你的数据结构，可能需要指定列名，例如 plt.plot(all_data['column_name'])
plt.title('所有文件聚合后的图表')
plt.xlabel('X轴标签')  # 替换为你的X轴标签
plt.ylabel('Y轴标签')  # 替换为你的Y轴标签
plt.savefig('all_files_aggregated.png')  # 保存总图表
plt.show()  # 显示总图表
```
