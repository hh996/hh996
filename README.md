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
import os.path
import re
import subprocess
import time

input_command = input("输入统计项: ").strip()  # CA_NAS R,W
statistics = input_command.split(" ")[1].split(",")  # ['R', 'W']
columns = []  # [(0, 1), (0, 1)]
for item in statistics:
    columns_index = input("输入{}数据列, 英文逗号分割: ".format(item)).split(",")
    columns.append(list(map(int, columns_index)))

suffix = []
for i_c_zip in zip(statistics, columns):
    temp_suffix = []
    for column in i_c_zip[1]:
        temp_suffix.append(input("输入{}第{}列后缀: ".format(i_c_zip[0], column)))
    suffix.append(temp_suffix)

try:
    res = subprocess.run("pwd", capture_output=True, text=True, check=True, shell=True)
    nodes_path = res.stdout.strip()
    res = subprocess.run("ls", capture_output=True, text=True, check=True, shell=True)
    nodes = res.stdout.strip().splitlines()

    # {'R0': {'node1': [], 'node2': []}, 'R1': {'node1': [], 'node2': []}}
    all_data = {}
    start_time = time.time()
    for node in nodes:
        print("开始处理{}".format(node))
        current_data = {"times": []}  # {'R0': [], 'R1': [], 'W0': [], 'W1': []}
        for i_c_zip in zip(statistics, columns):
            for column in i_c_zip[1]:
                current_data[i_c_zip[0] + str(column)] = []
                all_prefix = i_c_zip[0] + str(column)
                if not all_data.get(all_prefix):
                    all_data[all_prefix] = {}
                all_data[all_prefix][node] = []

        workload_path = os.path.join(nodes_path, node, "Messages/WorkLoad/WorkLoad/workload*")
        exec_command = "cat {} | Workload {}".format(workload_path, input_command)
        res = subprocess.run(exec_command, capture_output=True, text=True, check=True, shell=True)
        # 2024-05-14-09:00:00.000	R:830,1249,111572,1	W:3039,17137,101672,2
        workloads = res.stdout.strip().splitlines()
        for workload in workloads:
            # ['2024-05-14-09:00:00.000', 'R:830,1249,111572,1', 'W:3039,17137,101672,2']
            datas = re.split(r"\s+", workload)
            current_data["times"].append(datas[0])
            for data in datas[1:]:
                split_data = data.split(":")  # [R, 830,1249,111572,1]
                item = split_data[0]
                _data = split_data[1]
                item_index = statistics.index(item)
                column_data = _data.split(",")  # [830, 1249, 111572, 1]
                for column in columns[item_index]:
                    current_prefix = item + str(column)  # R0
                    current_data[current_prefix].append(int(column_data[column]))
        all_data["times"] = current_data["times"]
        for i_c_zip in zip(statistics, columns):
            for column in i_c_zip[1]:
                prefix = i_c_zip[0] + str(column)
                all_data[prefix][node] = current_data[prefix]
    print(all_data)
    end_time = time.time()
    time_str = time.strftime("%M:%S", time.gmtime(end_time - start_time))
    print("共耗时{}".format(time_str))
except subprocess.CalledProcessError as e:
    print(f"Command failed with return code {e.returncode}")
    print(f"Standard output: {e.stdout}")
    print(f"Standard error: {e.stderr}")


def draw_single(datas, statistics, columns, suffix):
    fig, ax1 = plt.subplots()
    ax1.set_xlabel("时间")
    times = [
        datetime.strptime(time, "%Y-%m-%d-%H:%M:%S.%f")
        for time in current_data["times"]
    ]
    color_index = 0
    lines = []
    labels = []
    for item, column, suf in zip(statistics, columns, suffix):
        # R [0, 1] ["请求次数", "时延"]
        key = item + str(column[0])  # R0
        ax1.set_ylabel(suf[0])  # 请求次数
        (line1,) = ax1.plot(
            times, datas[key], color=colors[color_index], linestyle="--"
        )
        lines.append(line1)
        labels.append(item + suf[0])
        if len(column) == 2:
            ax2 = ax1.twinx()
            key = item + str(column[1])  # R1
            ax2.set_ylabel(suf[1])
            (line2,) = ax2.plot(
                times, datas[key], color=colors[color_index], linestyle="-"
            )
            lines.append(line2)
            labels.append(item + suf[0])
        color_index += 1
    fig.legend(handles=lines, labels=labels)
    plt.rcParams["font.sans-serif"] = ["SimHei"]
    plt.gca().xaxis.set_major_formatter(mdates.DateFormatter("%m:%d:%H:%M:%S"))
    plt.gcf().autofmt_xdate()
    plt.show()

def draw_all(datas, statistics, columns, suffix):
    times = [
        datetime.strptime(time, "%Y-%m-%d-%H:%M:%S.%f")
        for time in datas["times"]
    ]
    # R [0, 1] ["请求次数", "时延"]
    for item, column, suf in zip(statistics, columns, suffix):
        # 0 请求次数
        for c, s in zip(column, suf):
            key = item + str(c)
            plt.xlabel("时间")
            plt.ylabel(s)
            # node5 [xxx]
            for node, data in datas.get(key).items():
                plt.plot(times, data, label=node + item + s)
            plt.rcParams["font.sans-serif"] = ["SimHei"]
            # 显示图例
            plt.legend()
            plt.gcf().autofmt_xdate()
            plt.show()
```

