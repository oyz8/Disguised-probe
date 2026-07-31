# 一口气点亮全球 200 个国家的探针
# ⭐ **觉得有用？给个 Star 支持一下！**

> **📌 快速指引**
> - **在线生成 UUID**：访问 [uuidgenerator.net](https://www.uuidgenerator.net/) 生成 **Version 4** 即可，每个探针需唯一 UUID。
> - **国家代码哪里查**：使用[Nations Online](https://www.nationsonline.org/oneworld/country_code_list.htm)。

---

## 参数说明

| 参数 | 说明 | 示例 |
|------|------|------|
| `server` | 哪吒面板地址（含端口） | `1.2.3.4:8008`、`nezha.com:443` |
| `secret` | 面板通信密钥 | `my-secret-key` |
| `uuid` | 客户端 UUID，支持逗号分隔多个，可单独指定国家 | `agent-uuid-01` 或 `agent-uuid-01:US,agent-uuid-02:JP` |
| `tls` | 是否启用 TLS | `true` / `false` |
| `country` | 全局伪装国家代码（当 `uuid` 未单独指定时生效） | `US`、`AQ`、`JP` |

> - 每个 UUID 可单独指定国家，格式：`UUID:国家代码`，多个组合用英文逗号分隔。
> - **单次请求最多支持 30 个探针**，超出将返回错误。

---

## 调用示例

**单探针，无国家伪装**
```bash
https://keep.nett.to/report?server=nezha.com:443&secret=my-secret-key&uuid=agent-uuid-01
```

**单探针，指定国家**
```bash
https://keep.nett.to/report?server=nezha.com:443&secret=my-secret-key&uuid=agent-uuid-01&country=AQ
```

**多探针，每个探针独立指定国家**
```bash
https://keep.nett.to/report?server=nezha.com:443&secret=my-secret-key&tls=true&uuid=agent-uuid-01:US,agent-uuid-02:AQ,agent-uuid-03:JP
```

---

## 自动保活（哪吒面板计划任务）

利用哪吒面板的计划任务，**每分钟**自动上报一次，所有探针持续在线，仅需一条命令。

### 配置步骤

1. 登录哪吒面板，进入 **计划任务** 页面，点击新建。

2. 填写基本信息：

   | 字段 | 内容 |
   |------|------|
   | 名称 | 探针保活 |
   | 类型 | 计划任务 |
   | Cron | `* * * * *`（每分钟执行） |
   | 执行服务器 | 选一台稳定在线、可访问外网的服务器 |
   | 通知组 | 按需选填，建议留空 |

3. 填写执行命令（示例含多个探针）：
- 单个面板地址
```bash
urls=(
  "https://keep.nett.to/report?server=nezha.com:443&secret=my-secret-key&tls=true&uuid=fb711b0b-7cdf-4493-80a4-0227ec134785:FM,32cf3744-75e9-47f3-9887-421c71463710:DJ,3d33f0dc-5714-4350-b793-3f32e9ff4cba:KZ,cf420725-ed1e-4adb-9036-adf242470915:AR,b9fafd90-9f8e-4512-be1f-710f5ad784e9:UZ,1f6c2bb0-f871-439b-90cc-e6c7fdd27d38:FJ,90cab00a-c85b-4c43-a6c8-4dceb8399bc3:GT,7a62877b-54f2-4954-a8af-dfe817f46086:LC,3ffc595d-196d-4fdd-aef3-64d87641116b:NI,c72009ec-9293-44b8-808e-20e595d9ccec:AQ,d51433ff-6068-46eb-84f0-0ef582ef2cf0:AQ"
)

for url in "${urls[@]}"; do
  curl -s \
    -A "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36" \
    -H "Accept-Language: zh-CN,zh;q=0.9" \
    -H "Cache-Control: no-cache" \
    "$url" &
done
wait
```

- 两个面板地址（按需增减）
```bash
urls=(
  "https://keep.nett.to/report?server=面板地址:443&secret=面板KEY&tls=true&uuid=fb711b0b-7cdf-4493-80a4-0227ec134785:FM"
  "https://keep.nett.to/report?server=面板地址:8008&secret=面板KEY&tls=false&uuid=fb711b0b-7cdf-4493-80a4-0227ec134785:FM"
)

for url in "${urls[@]}"; do
  curl -s \
    -A "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36" \
    -H "Accept-Language: zh-CN,zh;q=0.9" \
    -H "Cache-Control: no-cache" \
    "$url" &
done
wait
```

4. 保存任务，探针将在下一分钟开始保活。

### 注意事项

- **替换密钥与 UUID**：务必把 `mykey` 换成真实密钥，`uuid` 替换为你自己生成的 UUID。
- **国家代码**：支持 200+ 国家和地区，可自由搭配（如 `US`、`JP`、`FR`），请使用两位 ISO 代码。
- **探针数量**：单次请求最多 30 个；超过 30 个请拆分为多个计划任务。
- **执行频率**：计划任务最小粒度为 1 分钟，每分钟上报足以保持探针在线。若仍需更短间隔（如 20 秒），可在脚本中添加循环，例如：`for i in 1 2 3; do curl ... ; sleep 20; done`。
- **网络要求**：执行任务的服务器需能访问外网，推荐海外 VPS。
- **静默日志**：若不想在任务日志中看到输出，可在命令末尾追加 `> /dev/null 2>&1`。

---

## 响应示例

上报成功后返回类似内容，可用于排查：

```
📦 IP数据库: 已加载 | FM: 0, DJ: 0, KZ: 0

[UUID: fb711b0b-7cdf-4493-80a4-0227ec134785 | 国家: FM]
  ✅ ReportSystemInfo2: OK
  ✅ ReportGeoIP: OK (ipv4=none ipv6=none)
  ✅ ReportSystemState: OK (3 frames)

[UUID: 32cf3744-75e9-47f3-9887-421c71463710 | 国家: DJ]
  ✅ ReportSystemInfo2: OK
  ✅ ReportGeoIP: OK (ipv4=none ipv6=none)
  ✅ ReportSystemState: OK (3 frames)

[UUID: 3d33f0dc-5714-4350-b793-3f32e9ff4cba | 国家: KZ]
  ✅ ReportSystemInfo2: OK
  ✅ ReportGeoIP: OK (ipv4=none ipv6=none)
  ✅ ReportSystemState: OK (3 frames)
```

---

## 常见问题

**Q：如何添加更多探针？**  
在 `uuid` 参数末尾继续追加 `,新UUID:国家代码` 即可，总数不超过 30 个。超过请新建计划任务分担。

**Q：为什么单次限制 30 个？**  
为保证在免费计划的 CPU 时限内稳定完成。30 个探针串行上报约需 3～6 秒，处于安全范围内。

**Q：可以不写国家代码吗？**  
可以。如 `uuid=agent-uuid-01,agent-uuid-02`，此时所有探针使用全局 `country` 参数指定的国家；若也未设置 `country`，则不进行 IP 伪装。

**Q：国家代码在哪里查询？**  
请使用 ISO 3166-1 alpha-2 标准两位字母代码，可查阅 [Nations Online 列表](https://www.nationsonline.org/oneworld/country_code_list.htm)。

**Q：UUID 如何生成？**  
访问 [uuidgenerator.net](https://www.uuidgenerator.net/) 生成 Version 4 UUID，每次刷新即可获得新值，保证唯一性。

---

配置完成后，哪吒面板中对应的探针将持续显示在线，并展示所设国家的旗帜。
