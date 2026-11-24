背景与目标

仓库：C:\Users\A200394483\Desktop\Code\ODP\ingress-nginx（远程 git@github.com:mycheryhr/ingress-nginx.git）。
ingress-nginx controller 上游已停止维护，镜像里的 NGINX 存在多个漏洞。目标是把 NGINX 升到已修复版本，并通过 GitHub Actions 远程构建、发布到个人 DockerHub（mycheryhr）。

已确认的结论

CVE 清单（数据来自 NVD API，5 个都是 CVSS v4 9.2 Critical，v3.1 为 8.1）：

┌─────────────────┬─────────────────────────────────────────────────────┬────────────────────────────────────────┐
│       CVE       │                        类型                         │            本镜像是否受影响            │
├─────────────────┼─────────────────────────────────────────────────────┼────────────────────────────────────────┤
│ CVE-2026-123007 │ HTTP/2 流控窗口下溢，导致堆溢出                     │ 是                                     │
├─────────────────┼─────────────────────────────────────────────────────┼────────────────────────────────────────┤
│ CVE-2026-123633 │ HTTP/2 + proxy 时 UAF（worker_connections           │ 是                                     │
│                 │ 接近耗尽）                                          │                                        │
├─────────────────┼─────────────────────────────────────────────────────┼────────────────────────────────────────┤
│ CVE-2026-123634 │ proxy 名称含变量，加 SSI/mirror 子请求时 UAF        │ 视配置（用了 mirror 注解或 SSI         │
│                 │                                                     │ 才触发）                               │
├─────────────────┼─────────────────────────────────────────────────────┼────────────────────────────────────────┤
│ CVE-2026-121810 │ HTTP/3 UAF                                          │ 仅启用 QUIC 时                         │
├─────────────────┼─────────────────────────────────────────────────────┼────────────────────────────────────────┤
│ CVE-2026-113425 │ mail proxy UAF                                      │ 否（没有编译 mail 模块）               │
└─────────────────┴─────────────────────────────────────────────────────┴────────────────────────────────────────┘

受影响范围是 1.x 到 1.30.7 之前，以及 1.31.0 到 1.31.9 之前。修复版本：stable 1.30.7，mainline 1.31.9。

方案：以 release-nginx-1.30.1 分支为基线（该分支已完成 NGINX 1.30.1、lua 模块、njs、alpine 3.23.4、Go 1.26.3 的升级，以及 CI 调整），再把 NGINX 升到 1.30.7。创建release-nginx-1.30.7分支

Involved CVEs and reference links

CVE-2026-123007
https://nvd.nist.gov/vuln/detail/cve-2026-123007
https://my.f5.com/manage/s/article/K000163926

CVE-2026-123633
https://nvd.nist.gov/vuln/detail/cve-2026-123633
https://my.f5.com/manage/s/article/K000163928

CVE-2026-123634
https://nvd.nist.gov/vuln/detail/cve-2026-123634
https://my.f5.com/manage/s/article/K000163925

CVE-2026-121810
https://nvd.nist.gov/vuln/detail/cve-2026-121810
https://my.f5.com/manage/s/article/K000163927

CVE-2026-113425
https://nvd.nist.gov/vuln/detail/cve-2026-113425
https://my.f5.com/manage/s/article/K000163931



