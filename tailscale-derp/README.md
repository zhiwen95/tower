# 自建 Tailscale DERP 节点

Tailscale 官方 DERP 节点都在国外，中转流量时就会很卡，自建一个稳定很多。

## 快速开始

1. 按照 docker-compose.tailscale-derp.yml 启动
2. 端口转发
* 家庭场景：路由器设置端口转发，转发 12345/tcp 3478/udp
* 服务器场景：设置防火墙，允许 12345/tcp 3478/udp 通过
3. 修改 tailscale 访问控制
在 https://login.tailscale.com/admin/acls/file 增加 derpMap 配置
```json
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

