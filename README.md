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
@staticmethod
def _plot_data(ax, times, data, key, ylabel, color):
    ax.set_ylabel(ylabel, color=color)
    ax.plot(times, data, color=color)
    ax.tick_params(axis='y', labelcolor=color)

@staticmethod
def draw_single_node(statistics_processor, data, name):
    times = [datetime.strptime(t, "%Y-%m-%d-%H:%M:%S.%f") for t in data["times"]]
    
    for stat, col, suf in zip(
            statistics_processor.statistics,
            statistics_processor.columns,
            statistics_processor.suffix):
        
        # 创建图表和第一个y轴
        fig, ax1 = plt.subplots()
        
        # 绘制第一个y轴的数据
        Plotter._plot_data(ax1, times, data[stat + str(col[0])], 
                           ylabel=suf[0], color='tab:red')
        
        # 创建第二个y轴
        if len(col) > 1:
            ax2 = ax1.twinx()
            Plotter._plot_data(ax2, times, data[stat + str(col[1])], 
                               ylabel=suf[1], color='tab:blue')
            ax2.spines["right"].set_position(("axes", 1 + 0.2))  # 移动第二个y轴

        # 设置中文字体和自动调整x轴数据
        plt.rcParams["font.sans-serif"] = ["SimHei"]
        fig.autofmt_xdate()
        
        # 保存图表
        plt.savefig(f"{name}_{stat}.png")
        plt.close(fig)  # 关闭当前图形以释放内存
```
