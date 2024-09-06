- 👋 Hi, I’m @hh996
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

  
每个节点的 读请求、时延；写请求、时延单独画一张图
对齐刻度

每个节点数据时间不一样

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
datas = {
    "R0": {
        "node5": [
            [
                "21",
                "25",
                "21",
                "25",
                "21",
                "25",
                "21",
                "25",
                "21",
                "25",
                "28",
                "25",
                "21",
                "25",
                "21",
                "25",
                "21",
                "25",
                "21",
                "25",
            ]
        ],
        "node6": [
            [
                "35",
                "35",
                "45",
                "37",
                "39",
                "36",
                "38",
                "32",
                "34",
                "31",
                "134",
                "30",
                "30",
                "26",
                "38",
                "23",
                "35",
                "104",
                "32",
                "29",
            ]
        ],
    },
    "R1": {
        "node5": [
            [
                "56",
                "59",
                "54",
                "57",
                "58",
                "56",
                "59",
                "59",
                "59",
                "58",
                "57",
                "55",
                "55",
                "56",
                "54",
                "58",
                "56",
                "56",
                "57",
                "58",
            ]
        ],
        "node6": [
            [
                "2550",
                "1886",
                "1679",
                "2101",
                "1844",
                "2065",
                "1861",
                "1876",
                "1442",
                "1586",
                "1733",
                "1669",
                "765",
                "999",
                "829",
                "1812",
                "548",
                "1640",
                "1088",
                "1708",
            ]
        ],
    },
    "times": [
        "2024-06-17-01:00:59.356906",
        "2024-06-17-01:01:59.435921",
        "2024-06-17-01:02:59.527907",
        "2024-06-17-01:03:59.603923",
        "2024-06-17-01:04:59.713062",
        "2024-06-17-01:05:59.798884",
        "2024-06-17-01:06:59.889896",
        "2024-06-17-01:07:59.966894",
        "2024-06-17-01:09:00.058979",
        "2024-06-17-01:10:00.148999",
        "2024-06-17-01:11:00.390951",
        "2024-06-17-01:12:00.477959",
        "2024-06-17-01:13:00.561904",
        "2024-06-17-01:14:00.644983",
        "2024-06-17-01:15:00.735944",
        "2024-06-17-01:16:00.832934",
        "2024-06-17-01:17:00.918904",
        "2024-06-17-01:18:01.002942",
        "2024-06-17-01:19:01.102884",
        "2024-06-17-01:20:01.265916",
    ],
}
import os
import re
import subprocess
import time
from datetime import datetime

from matplotlib import pyplot as plt
import matplotlib.dates as mdates


class StatisticsProcessor:
    def __init__(self):
        self.input_command = ""
        self.statistics = []
        self.columns = []
        self.suffix = []

    def get_input_command(self) -> None:
        self.input_command = input("输入统计项：").strip()  # CA_NAS R,W
        self.statistics = re.split(r"\s+", self.input_command)[1].split(
            ","
        )  # ['R', 'W']

    def get_columns(self) -> None:
        for item in self.statistics:
            columns_index = input(f"输入{item}数据列, 英文逗号分割: ").split(",")
            self.columns.append(list(map(int, columns_index)))

    def get_suffix(self) -> None:
        for stat, cols in zip(self.statistics, self.columns):
            temp_suffix = []
            for column in cols:
                temp_suffix.append(input(f"输入{stat}第{column}列后缀: "))
            self.suffix.append(temp_suffix)

    def process(self) -> None:
        self.get_input_command()
        self.get_columns()
        self.get_suffix()

    def display(self):
        print("统计项:", self.statistics)
        print("数据列:", self.columns)
        print("后缀:", self.suffix)


class NodeDataProcessor:
    def __init__(self, statistics_processor: StatisticsProcessor):
        self.stats_processor = statistics_processor
        self.node_path = ""
        self.nodes = []
        """
        self.all_data = {
            "R0": {
                "node5": [],
                "node6": [],
            },
            "R1": {
                "node5": [],
                "node6": [],
            },
            "W0": {
                "node5": [],
                "node6": [],
            },
            "W1": {
                "node5": [],
                "node6": [],
            },
            "times": [],
        }
        """
        self.all_data = {}

    def fetch_nodes(self) -> None:
        # 获取当前工作目录
        res = subprocess.run("pwd", text=True, check=True, shell=True)
        self.node_path = res.stdout.strip()

        # 获取节点列表
        res = subprocess.run("ls", text=True, check=True, shell=True)
        self.nodes = res.stdout.strip().splitlines()

    def run_workload_command(self, node) -> list[str]:
        # 构建 workload 命令并执行
        workload_path = os.path.join(
            self.node_path, node, "Messages/WorkLoad/WorkLoad/workload*"
        )
        exec_command = (
            f"cat {workload_path} | Workload {self.stats_processor.input_command}"
        )
        res = subprocess.run(exec_command, text=True, check=True, shell=True)
        return res.stdout.strip().splitlines()

    def process_node(self, node) -> None:

        print(f"开始处理 {node}")
        current_data = {"times": []}

        """ 
        初始化当前节点的数据结构
        current_data = {
            "times": [],
            "R0": [],
            "R1": [],
            "W0": [],
            "W1": [],
        }
        """
        for stat, cols in zip(
            self.stats_processor.statistics, self.stats_processor.columns
        ):
            for col in cols:
                current_data[stat + str(col)] = []
                all_prefix = stat + str(col)
                if not self.all_data.get(all_prefix):
                    self.all_data[all_prefix] = {}
                self.all_data[all_prefix][node] = []

        workloads = self.run_workload_command(node)

        # 处理每行的 workload
        for workload in workloads:
            datas = re.split(r"\s+", workload)
            current_data["times"].append(datas[0])
            for data in datas[1:]:
                split_data = data.split(":")
                stat = split_data[0]
                _data = split_data[1]
                stat_index = self.stats_processor.statistics.index(stat)
                column_data = _data.split(",")
                for col in self.stats_processor.columns[stat_index]:
                    current_prefix = stat + str(col)
                    current_data[current_prefix].append(int(column_data[col]))

        # 将当前节点的数据保存到全局数据结构
        if not self.all_data.get("times"):
            self.all_data["times"] = current_data["times"]
        for stat, cols in zip(self.stats_processor.statistics, self.stats_processor.columns):
            for col in cols:
                prefix = stat + str(col)
                self.all_data[prefix][node] = current_data[prefix]
        print(f"{node} 数据处理完成.")

    def process(self) -> None:
        start_time = time.time()
        self.fetch_nodes()

        for node in self.nodes:
            self.process_node(node)

        end_time = time.time()
        self.display_time_taken(start_time, end_time)

    @staticmethod
    def display_time_taken(start_time, end_time) -> None:
        time_str = time.strftime("%M:%S", time.gmtime(end_time - start_time))
        print(f"共耗时 {time_str}")


class Plotter:
    def __init__(self, statistics_processor: StatisticsProcessor):
        self.statistics_processor = statistics_processor
        self.colors = ["b", "g", "r", "c", "m", "y", "k"]

    def draw_single_node(self, data) -> None:
        fig, ax1 = plt.subplots()
        ax1.set_xlabel("时间")
        times = [
            datetime.strptime(t, "%Y-%m-%d-%H:%M:%S.%f")
            for t in data["times"]
        ]
        color_index = 0
        lines = []
        labels = []

        for stat, col, suf in zip(self.statistics_processor.statistics, self.statistics_processor.columns, self.statistics_processor.suffix):
            key = stat + str(col[0])  # R0
            ax1.set_ylabel(suf[0])  # 请求次数
            (line1,) = ax1.plot(
                times, data[key], color=self.colors[color_index], linestyle="--"
            )
            lines.append(line1)
            labels.append(stat + suf[0])

            if len(col) == 2:
                ax2 = ax1.twinx()
                key = stat + str(col[1])  # R1
                ax2.set_ylabel(suf[1])  # 时延
                (line2,) = ax2.plot(
                    times, data[key], color=self.colors[color_index], linestyle="-"
                )
                lines.append(line2)
                labels.append(stat + suf[1])
            color_index += 1
        fig.legend(handles=lines, labels=labels)
        plt.rcParams["font.sans-serif"] = ["SimHei"]  # 设置中文字体
        plt.gca().xaxis.set_major_formatter(mdates.DateFormatter("%m:%d:%H:%M:%S"))
        plt.gcf().autofmt_xdate()
        plt.show()

    def draw_all(self, data) -> None:
        times = [
            datetime.strptime(t, "%Y-%m-%d-%H:%M:%S.%f")
            for t in data["times"]
        ]
        for stat, col, suf in zip(self.statistics_processor.statistics, self.statistics_processor.columns, self.statistics_processor.suffix):
            for c, s in zip(col, suf):
                key = stat + str(c)  # R0
                plt.xlabel("时间")
                plt.ylabel(s)

                for node, data in data.get(key).items():
                    plt.plot(times, data, label=node + stat + s)

                plt.rcParams["font.sans-serif"] = ["SimHei"]  # 设置中文字体
                plt.legend()  # 显示图例
                plt.gcf().autofmt_xdate()
                plt.show()

# 使用方法
stats_processor = StatisticsProcessor()
stats_processor.process()  # 用户输入统计项和列等信息

processor = NodeDataProcessor(stats_processor)
processor.process()

all_datas = {
    "R0": {
        "node5": [

        ],
        "node6": [

        ],
    },
    "R1": {
        "node5": [

        ],
        "node6": [

        ],
    },
    "W0": {
        "node5": [

        ],
        "node6": [

        ],
    },
    "W1": {
        "node5": [

        ],
        "node6": [

        ],
    },
    "times": [
       
    ],
}


current_data = {
    "times": [
        
    ],
    "R0": [
        
    ],
    "R1": [
       
    ],
    "W0": [
        
    ],
    "W1": [
        
    ],
}


statistics = ["R", "W"]

columns = [[0, 1], [0, 1]]

suffix = [["请求次数", "时延"], ["请求次数", "时延"]]

colors = ["pink", "green"]
```

