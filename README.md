# 订阅转换模板（ACL4SSR 格式 · 规则源 meta-rules-dat）

两份 ACL4SSR 格式的 subconverter / OpenClash 模板，规则数据全部来自
[MetaCubeX/meta-rules-dat](https://github.com/MetaCubeX/meta-rules-dat) 的 `meta` 分支。

| 文件 | 规则行 | 分组 | 用途 |
|---|---|---|---|
| `ACL4SSR_meta.ini` | 26 | 21 | 细分版：流媒体 / AI / 苹果 / 微软 / 游戏各自成组 |
| `ACL4SSR_Mini_meta.ini` | 11 | 5 | 精简版，规模对齐 ACL4SSR_Mini |

## 用法

**OpenClash**：覆写设置 → 自定义模板 → 填入本仓库文件的 URL；或放到 `/etc/openclash/custom/`。

**subconverter**：

```
/sub?target=clash&url=<你的订阅>&config=<本 ini 的 URL>&new_name=true
```

## 为什么规则源用 `.yaml` 而不是 `.list`

ACL4SSR 的 `.list` 是 classical 文本：

```
DOMAIN-SUFFIX,example.com
```

meta-rules-dat 的 `.list` 是裸 payload：

```
+.example.com
1.0.1.0/24
```

直接把 ACL4SSR 模板里的 URL 换掉，subconverter 会按 classical 解析，认不出来。
所以本模板统一使用 meta-rules-dat 的 `.yaml`（`payload:` 结构），配合 subconverter 的
`clash-domain:`（域名）与 `clash-ipcidr:`（IP 段），两种写法都原生支持，且不依赖 geodata 下载。

## 踩过的坑

1. **`enable_rule_generator` 必须为 `true`。** 写成 `false` 时输出只有 `proxies` 和
   `proxy-groups`，`rules:` 是空的，而且没有任何报错。
2. **subconverter 按 URL 缓存远程模板。** 改完 ini 必须带 `?v=2` 之类的查询参数才会拉到
   新版本，否则一直用旧的。
3. **`&list=true` 只输出 `proxies`。** 分组和规则全部消失，不要用。
4. **模板精简 ≠ 产物变小。** subconverter 默认把规则内联展开进 `rules:`，精简版输出仍有
   7 MB 左右、约 15 万条。要产物小只能走 mihomo 原生 mrs 规则集，ini 模板做不到。

## 验证记录

两个模板都经过真实链路验证，不是只做语法检查：

| 项目 | 细分版 | 精简版 |
|---|---|---|
| subconverter 转换 | HTTP 200 | HTTP 200 |
| 生成规则数 | 150,269 | 148,929 |
| `mihomo -t` 校验 | 通过 | 通过 |
| 广告域名 | 4 ms 秒拒 | 11 ms 秒拒 |
| 国内域名 | HTTP 200 直连 | HTTP 200 直连 |
| 境外域名 | 走代理组 | 走代理组 |

验证环境：subconverter v0.9.0 + mihomo v1.19.33。

## 许可

模板本身可自由取用。规则数据版权归 MetaCubeX/meta-rules-dat（GPL-3.0）所有。
