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
import os.path
import re
import subprocess

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

    all_data = (
        {}
    )  # {'R0': {'node1': [], 'node2': [], 'node3': []}, 'R1': {'node1': [], 'node2': [], 'node3': []}, 'W0': {'node1': [], 'node2': [], 'node3': []}, 'W1': {'node1': [], 'node2': [], 'node3': []}}
    for node in nodes:
        current_data = {"times": []}  # {'R0': [], 'R1': [], 'W0': [], 'W1': []}
        for i_c_zip in zip(statistics, columns):
            for column in i_c_zip[1]:
                current_data[i_c_zip[0] + str(column)] = []
                all_prefix = i_c_zip[0] + str(column)
                if not all_data.get(all_prefix):
                    all_data[all_prefix] = {}
                all_data[all_prefix][node] = []

        workload_path = os.path.join(
            nodes_path, node, "Messages/WorkLoad/WorkLoad/workload*"
        )
        exec_command = "cat {} | Workload {}".format(workload_path, input_command)
        res = subprocess.run(
            exec_command, capture_output=True, text=True, check=True, shell=True
        )
        workloads = res.stdout.strip().splitlines()
        for (
            workload
        ) in (
            workloads
        ):  # 2024-05-14-09:00:00.000	R:830,1249,111572,1	W:3039,17137,101672,2
            datas = re.split(
                r"\s+", workload
            )  # ['2024-05-14-09:00:00.000', 'R:830,1249,111572,1', 'W:3039,17137,101672,2']
            current_data["times"].append(datas[0])
            for data in datas[1:]:
                split_data = data.split(":")  # [R, 830,1249,111572,1]
                item = split_data[0]
                _data = split_data[1]
                item_index = statistics.index(item)
                column_data = _data.split(",")  # [830, 1249, 111572, 1]
                for column in columns[item_index]:
                    current_prefix = item + str(column)  # R0
                    current_data[current_prefix].append(column_data[column])
        all_data["times"] = current_data["times"]
        for i_c_zip in zip(statistics, columns):
            for column in i_c_zip[1]:
                prefix = i_c_zip[0] + str(column)
                all_data[prefix][node].append(current_data[prefix])
    print(all_data)
except subprocess.CalledProcessError as e:
    print(f"Command failed with return code {e.returncode}")
    print(f"Standard output: {e.stdout}")
    print(f"Standard error: {e.stderr}")
```

