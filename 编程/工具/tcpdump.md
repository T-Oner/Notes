# tcpdump 命令参数解释

TcpDump 可以将网络中传输的数据包的”头“完全截获下来提供分析。它支持针对网络层、协议、主机、网络或端口的过滤，并提供 and、or、not 等逻辑语句来帮助你去掉无用的信息。

数据过滤

不带任何参数的 tcpdump 将搜索系统中所有的网络接口，并显示它截获的所有数据，这些数据对我们不一定全都需要，而且数据太多不利于分析。所以，我们应当先想好需要哪些数据，tcpdump 提供以下参数供我们选择数据：

| 参数                                | 描述                                                         | 实例                                     |
| ----------------------------------- | ------------------------------------------------------------ | ---------------------------------------- |
| -i \<interface>                     | 只过滤指定的接口上通过的数据                                 | tcpdump -i eth0                          |
| -i \<vlan>                          | 只过滤指定的 vlan 上通过的数据                               | tcpdump -i external                      |
| -e                                  | 显示 MAC 地址                                                | tcpdump -e                               |
| -n 注：在 bigip v9 中，需要使用 -nn | 不进行 IP 地址到主机名的转换                                 | tcpdump -n                               |
| -X                                  | Display packets in hex and decondes in ASC II                | tcpdump -X                               |
| -s \<value>                         | 显示指定的字节数，默认为 76 字节，实际使用时，建议用 -s 1600，这样可以保证抓取完整的数据包 | tcpdump -s 1600                          |
| -w                                  | 将输出内容保存在指定二进制文件中                             | tcpdump -w chen                          |
| -r                                  | 查看指定的二进制文件                                         | tcpdump -r chen                          |
| \> \<filename>                      | 将输出内容保存在指定文件中，非二进制文件                     | tcpdump -n chen                          |
| host \<ip>                          | 过滤指定的主机，只显示过滤的内容，包括访问与应答             | tcpdump host 192.168.1.1                 |
| \<protocol>                         | 过滤指定的协议：ICMP、UDP、ARP、etc                          | tcpdump -i exp1 udp                      |
| port \<port>                        | 过滤指定的端口                                               | tcpdump -i exp1 80                       |
| 条件 1 and 条件 2                   |                                                              | tcpdump -i exp1 host 192.168.1.1 and udp |
| 条件 1 or 条件 2                    |                                                              | tcpdump -i exp1 host 192.168.1.1 or udp  |
| 条件 1 not 条件 2                   | 所有满足条件 1，并且不满足条件 2 的数据                      | tcpdump -i exp1 host 192.168.1.1 not udp |
| src \<ip>                           | 过滤指定的来源地址，只显示访问 IP 的请求包，不再显示F5 返回的包。 | tcpdump -i exp1 src 192.168.1.1 not udp  |
| dst                                 | 过滤指定的来源地址，只显示 F5 返回的包，不再显示访问 IP 的请求包。 |                                          |

注：tcpdump 命令只针对经过 CPU 处理的数据包进行捕获，一旦在 BIGIP 中的某个 VIP 采用的是 performance L4 的方式，数据包则由四层加层 ASIC 芯片处理而没有流经 CPU，无法捕获数据。解决该问题的方法是，选取该 Virtual Server 将 type 由 Performance Layer 4 临时改为 Standard 再来用 tcpdump 命令抓包，抓包以后，改回到 Performance Layer 4。

# tcpdump 出现 “truncated-ip -1215 bytes missing!”错误

在 BIG-IP V9 里面出现 “Truncated-IP xxxx bytes missing” 信息，一般来说并不是网络上有丢包引起的，而是在执行 tcpdump 命令时没有加上 -s0 或者 -s1600 参数时，而数据包大小超过 tcpdump 缺省的抓包大小（如果不加 -s0 或者 -s1600 参数，则缺省的每个数据包只抓前面 400 bytes），就会出现 truncated-ip 的情况。出现这种情况，只需要重新输入 tcpdump 命令，加上 -s0 或者 -s1600 即可。

# tcpdump 命令中的 -i 参数用 VLAN 名称与接口编号有什么区别

如果采用 VLAN 名称作为 -i 的参数，tcpdump 收集的数据包是经由内部接口到达 TMM 进程经由中央 CPU 处理的数据包。

采用 VLAN 名称作为 -i 参数的**局限性**在于，由于 PVA 四层加速芯片时位于 BIG-IP 的交换板上，并不需要经由主机板与交换机板的内部接口到达中央 CPU，因此 tcpdump 无法抓取这些四层加速的数据包。

因此采用 VLAN 名称作为 -i 的参数一般是用于对采用 Standard 作为 Virtual Server 类型的应用抓包时采用。

注：如果 Virtual Server 是用 PVA 四层加速芯片作加速处理，则在 Virtual Server 的属性中 PVA Acceleration 显示为 Full。