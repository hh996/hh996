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

```python
def draw_single_node(statistics_processor, data, name):
    times = [datetime.strptime(t, "%Y-%m-%d-%H:%M:%S.%f") for t in data["times"]]

    for stat, col, suf in zip(
            statistics_processor.statistics,
            statistics_processor.columns,
            statistics_processor.suffix):
        # 创建图表和第一个y轴
        fig, ax1 = plt.subplots()
        # 绘制第一个y轴的数据
        color = 'tab:red'
        ax1.set_xlabel('时间')
        ax1.set_ylabel(suf[0], color=color)
        ax1.plot(times, data[stat + str(col[0])], color=color)
        ax1.tick_params(axis='y', labelcolor=color)

        # 创建第二个y轴
        if len(col) > 1:
            ax2 = ax1.twinx()
            color = 'tab:blue'
            ax2.set_ylabel(suf[1], color=color)
            ax2.plot(times, data[stat + str(col[1])], color=color)
            ax2.tick_params(axis='y', labelcolor=color)

        # 设置中文字体
        plt.rcParams["font.sans-serif"] = ["SimHei"]

        # 自动调整x轴数据
        fig.autofmt_xdate()

        # 保存图表
        # plt.savefig(f"{name}_{stat}.png")
        # plt.close(fig)  # 关闭当前图形以释放内存
        plt.show()
```
