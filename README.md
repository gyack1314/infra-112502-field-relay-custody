# 断连中继的设备事件保管移交与区域改道

偏远场点使用若干临时接力站传递设备观测。运输信封由观测组成，各站有独立容量。所有回执和拓扑均为仿真。

## 输入消息

每个 JSONL 文件是一段独立输入记录。每行包括整数秒点 at、消息名称 kind 与对象 record。at 代表中间件收到消息的时刻，同一秒内按行序提交。业务时间和对应回执引用写在 record 中。

- **node**　登记 node、kind 和 capacity。kind 为 origin、relay 或 office，capacity 以不同观测的份数计。
- **route**　发布 revision 与有向通路 edges，生效后用于新运输决定。offices 映射允许接纳某来源的办事处，生效后是否接受一条最终凭据按该办事处接纳发生时所处的规则判定。
- **report**　来源记录 device、observation、digest 和 origin。业务身份是 device 及 observation。
- **offer**　源站和下游传输一批 observation 引用，envelope 为该次运输身份。时间及允许通路在实际发出时检查。 实际系统按设备和观测编号引用，每段合成记录中的观测编号全段唯一，因此使用编号简写。
- **received**　目标通报传输到达，引用 envelope。这一状态本身不承诺继续保管。
- **custody**　下游实际确认已持久接管一组 observation，引用 envelope 与 node。名单可以只包含信封一部分。 接管为接收端的持久事实。该事件可自身证明运输到达，不要求另有 received 回报。
- **archived**　办事处提交真正入档的 observation 与 digest，office 和 accepted_at 是接纳站及实际秒点。
- **deliver_receipt**　指定 node 收到某个办事处下发的已入档事实。
- **restart**　该站重启，保留真实持久承诺。
- **inspect**　按 observation 或 node 查询状态。

## 示例

- split-during-reroute.jsonl
- archived-before-rule-change.jsonl

## 读取演练资料

运行环境为 Python 3.11，只使用标准库。在根目录运行以下命令可核对文件格式及读取接口。

    python3 scenario_reader.py examples
    python3 -m unittest discover -v

资料读取器会列出各文件包含的消息和时间范围。演练记录只描述各参与方交给系统的输入。
