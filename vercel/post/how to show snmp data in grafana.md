---
category: 技术
title: how to show snmp data in grafana
draft: true
---
> [!important] 目录
> 
> - [[#snmp 是什么]]
> - [[#如何在grafana中展示snmp数据]]
> - [[#安装snmp_exporter]]
> - [[#使用 Docker 安装 SNMP Exporter]]

### snmp 是什么

snmp 是一种网络管理协议，用于监视和管理网络设备。它可以通过查询设备的指标来获取有关设备性能的信息，并将该信息报告给其他设备或管理系统。Grafana 是一种开源的数据可视化工具，可用于创建和共享交互式的仪表板和报告。将 snmp 数据显示在 Grafana 中可以帮助您更好地了解您的网络设备性能和健康状况。

### 如何在grafana中展示snmp数据

展示 snmp 数据在 grafana 中需要使用 snmp_exporter，这是一个 Prometheus 的 exporter，它可以将 snmp 设备的数据转换为 Prometheus 格式。首先，您需要安装并配置 snmp_exporter。然后，您可以使用 Prometheus 数据源将 snmp 数据导入 Grafana 中，并创建仪表板以展示所需的指标。

### 安装snmp_exporter

### 使用 Docker 安装 SNMP Exporter

您可以使用 Docker 安装 SNMP Exporter，以下是安装步骤：

1. 在您的主机上安装 Docker。您可以在 Docker 官网上找到相关的安装指南。
2. 从 Docker Hub 上拉取 SNMP Exporter 镜像，运行以下命令：
    
    ```Plain
    docker run -d --name snmp-exporter -p 9116:9116 \
    -e SNMP_TARGETS=10.10.10.1 \
    -e SNMP_COMMUNITY=ikuai \
    prom/snmp-exporter
    ```
    
    其中：
    
    - `d` 参数表示在后台运行容器；
    - `-name` 参数指定容器的名称；
    - `p` 参数将容器的 9116 端口映射到主机的 9116 端口；
    - `e SNMP_TARGETS` 参数设置 SNMP 查询的目标 IP 地址；
    - `e SNMP_COMMUNITY` 参数设置 SNMP 共同体字符串。
    
    您需要将 `SNMP_TARGETS` 和 `SNMP_COMMUNITY` 替换为您的 SNMP 设备的相关信息。
    
3. 等待一段时间，让 SNMP Exporter 将数据从设备中收集并转换为 Prometheus 格式。
4. 访问 `http://localhost:9116/metrics`，您应该可以看到 SNMP Exporter 输出的指标数据。

您可以在 Prometheus 数据源中添加 `http://localhost:9116`，将 SNMP 数据导入 Grafana 中，并创建您自己的仪表板以展示所需的指标。

要在 Grafana 中监控当前客户端流量，您可以按照以下步骤操作：

1. 首先，确保您已经安装和配置了 snmp_exporter，并且可以查询客户端流量指标。您可以使用以下命令查询客户端流量指标：
    
    ```Plain
    snmpwalk -v 2c -c ikuai 10.10.10.1 1.3.6.1.2.1.2.2.1.10
    ```
    
    其中，`10.10.10.1` 是您的 SNMP 设备的 IP 地址，`ikuai` 是您的 SNMP 共同体字符串。`1.3.6.1.2.1.2.2.1.10` 是客户端流量的 OID。
    
2. 在 Grafana 中创建一个新的仪表板。
3. 添加一个 Prometheus 数据源，将 `http://localhost:9090` 添加为数据源地址。确保您已经配置了 Prometheus，以便可以在其中查询 snmp_exporter 输出的指标。
4. 在仪表板中添加一个新的面板，并将数据源设置为 Prometheus。
5. 在查询编辑器中，输入以下查询表达式：
    
    ```Plain
    sum(snmp_ifInOctets{ifDescr="eth0"}) + sum(snmp_ifOutOctets{ifDescr="eth0"})
    ```
    
    其中，`eth0` 是客户端所连接的网络接口的名称。如果您的网络接口名称不同，请将其替换为正确的名称。
    
6. 配置面板，以便它显示您需要的指标。例如，您可以选择一个合适的图形类型，并添加标题和标签。
7. 保存您的仪表板，并在其中查看当前客户端流量的指标。