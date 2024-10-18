# 自建 Tailscale DERP 节点

Tailscale 官方 DERP 节点都在国外，中转流量时就会很卡，自建一个稳定很多。

## 快速开始

1. 按照 docker-compose.tailscale-derp.yml 启动
2. 端口转发
* 家庭场景：路由器设置端口转发 12345/tcp 3478/udp
* 服务器场景：设置防火墙，允许 12345/tcp 3478/udp 通过
3. 修改 tailscale 访问控制
   在 https://login.tailscale.com/admin/acls/file 增加 derpMap 配置
   
   ```json5
   // Example/default ACLs for unrestricted connections.
   {
    ...,
    "derpMap": {
        // 设为 true 可以让 Tailscale 只使用自建节点，推荐 false
        "OmitDefaultRegions": false,
        "Regions": {
            "900": {
                // tailscale 900-999 是保留给自定义 derper 的
                "RegionID":   900, 
                // 填写 DERP 服务所在区域代码，没有严格限制，例如官方香港节点是：hkg
                "RegionCode": "bj",
                // 填写 DERP 服务所在区域名称，没有严格限制，例如官方香港节点是：“Hong Kong”
                "RegionName": "BeiJing",
                "Nodes": [
                    {
                        // 节点名
                        "Name":             "xxxx",
                        "RegionID":         900,
                        // 服务器域名
                        "HostName":         "xxx.xxx.xxx",
                        "DERPPort":         12345,
                        // 忽略SSL证书检查
                        "InsecureForTests": true,
                    },
                ],
            },
        },
    },
   }
   ```

```
4. 检查客户端状态

Nearest DERP 显示你的自建节点就说明成功了

```shell
$ tailscale netcheck

Report:
        * UDP: true
        * IPv4: yes, xxxxxx:xxxx
        * IPv6: no, but OS has support
        * MappingVariesByDestIP: false
        * PortMapping:
        * CaptivePortal: false
        * Nearest DERP: BeiJing
        * DERP latency:
                -  bj: 6.2ms   (BeiJing)
                - tok: 63.1ms  (Tokyo)
                - hkg: 106.4ms (Hong Kong)
                - sin: 127.4ms (Singapore)
                - sfo: 183ms   (San Francisco)
                - lax: 185.6ms (Los Angeles)
                - den: 194.8ms (Denver)
                - sea: 203.9ms (Seattle)
                - dfw: 213.4ms (Dallas)
                - mad: 216ms   (Madrid)
                - hnl: 217.3ms (Honolulu)
                - blr: 219.2ms (Bangalore)
                - syd: 219.2ms (Sydney)
                - ams: 225.9ms (Amsterdam)
                - nyc: 226.2ms (New York City)
                - ord: 230.8ms (Chicago)
                - lhr: 232ms   (London)
                - tor: 241.2ms (Toronto)
                - mia: 242.4ms (Miami)
                - par: 243.3ms (Paris)
                - fra: 244ms   (Frankfurt)
                - waw: 251.6ms (Warsaw)
                - sao: 315.9ms (São Paulo)
                - dbi: 339.9ms (Dubai)
                - nai: 359.8ms (Nairobi)
                - jnb: 361.2ms (Johannesburg)
```

5. DERP 节点地址请不要告诉任何人，因为它是公开访问的，如果你想限制公开访问需要在 DERP 服务器启动 Tailscale 并使用 -verify-clients 启动 derp 节点，具体步骤请自行搜索。