## meta.yaml配置说明：

```yaml
dns:
  enable: true
  enhanced-mode: fake-ip
  nameserver:
    - system
```
- `dns`: dns配置项。
- `enable`: 是否启用dns功能。
- `enhanced-mode`: dns增强模式，这里设置为fake-ip模式，会将请求的DNS服务器IP地址替换成配置文件中指定的虚拟IP地址。
- `nameserver`: 指定DNS服务器地址，这里设置为系统默认的nameserver。

```yaml
tun:
  enable: false
  stack: gvisor
  auto-route: true
  auto-detect-interface: true
  dns-hijack:
    - any:53
```
- `tun`: tun配置项，用于配置虚拟网卡的相关参数。
- `enable`: 是否启用虚拟网卡。
- `stack`: 虚拟网卡使用的协议栈，这里设置为gvisor。
- `auto-route`: 是否自动路由流量到虚拟网卡。
- `auto-detect-interface`: 是否自动检测网络接口。
- `dns-hijack`: dns劫持配置，这里设置为拦截所有DNS流量。

```yaml
p: &p
  type: http
  interval: 3600
  health-check:
    enable: true
    url: https://www.gstatic.com/generate_204
    interval: 300
```
- `p`: 定义了一个命名锚点(p)。
- `&p`: 定义了一个命名锚点(p)，可以在后面使用`*p`引用它。
- `type`: 代理类型，这里设置为http。
- `interval`: 健康检查和定时更新的时间间隔。
- `health-check`: 健康检查配置项。
- `enable`: 是否启用健康检查。
- `url`: 用于健康检查的URL地址。
- `interval`: 健康检查的时间间隔。

```yaml
use: &use
  use: 
    - provider1
    - provider2
```
- `use`: 定义了一个使用代理提供者的命名锚点(use)。
- `&use`: 定义了一个命名锚点(use)，可以在后面使用`*use`引用它。
- `use`: 指定要使用的代理提供者，这里使用了provider1和provider2两个提供者。

```yaml
proxy-providers:
  provider1: 
    <<: *p
    path: ./providers/proxy/provider1.yaml
    url: ""
  provider2: 
    <<: *p
    type: file
    path: ./provider2.yaml
```
- `proxy-providers`: 代理提供者配置项。
- `provider1`: 第一个代理提供者的配置项。
- `<<`: 指定继承自命名锚点的配置项。
- `*p`: 引用了命名锚点(p)的配置。
- `path`: 配置文件路径。
- `url`: 配置文件URL地址。
- `provider2`: 第二个代理提供者的配置项。
- `nameserver`: 定义DNS服务器，这里设置为使用系统默认的DNS服务器。
- `tun`: 定义TUN模式的参数，这里禁用了TUN模式。
- `p` 和 `use`: 定义了两个 YAML 锚点，方便其他地方进行引用。
- `proxy-providers`: 定义代理提供者，包括 `provider1` 和 `provider2`，其中 `provider1` 使用了 HTTP 方式获取代理地址，而 `provider2` 使用了本地文件的方式获取代理地址。
- `proxy-groups`: 定义了三个代理组，分别为 `代理`、`自动` 和 `美国`。`代理` 组使用了 `select` 类型，代理列表中只包含 `自动` 这个组；`自动` 组使用了 `url-test` 类型，通过访问指定的网站来测试代理服务器是否可用；`美国` 组也使用了 `url-test` 类型，不过还设置了一些额外的参数，如容忍度、惰性加载和筛选规则等。
- `rule-providers`: 定义规则提供者，这里只定义了一个 `openai`，其类型为 HTTP，使用了域名匹配行为，从指定的 URL 或本地路径获取规则，每 600 秒更新一次。
- `rules`: 定义了一些规则，其中包括引用 `openai` 规则提供者的规则集、根据地理位置判断是否直连、匹配 `代理` 代理组等规则。