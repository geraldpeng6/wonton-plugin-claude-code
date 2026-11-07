# Claude Code

开发流程见[插件开发指南](https://github.com/geraldpeng6/wonton-docs/blob/main/develop.md)。

Claude Code 是住在云吞里的 Agent：Anthropic 的 Claude Code（经官方的 claude-agent-acp 适配器说 ACP）
用核心分配的 Matrix 身份和主人聊天，经 Wonton MCP 总线调用工具。改文件、跑命令之前出确认卡片，
同意才动手。

模型有两个来源，每次容器启动时选定：

- **模型网关**：网关接好、并且提供 Anthropic Messages 接口时，默认走网关，不用账号、不用填 key。
- **自己的 Anthropic 登录**：没接网关（或网关没有 Anthropic 接口）时用这个。三种方式任选：
  Claude 订阅、Anthropic Console、API key。登录态存在数据目录，容器重建后还在。

Claude Code 只讲 Anthropic Messages，OpenAI 兼容地址喂不进去；网关不提供 Anthropic 接口时按没接网关处理。

## 登录（没接网关时）

在 App 的插件页打开这个插件的网页，或者在聊天里发 `/wonton login`，三选一：

- **Claude 订阅**（Pro、Max、Ultra、Team、Enterprise；免费版不含 Claude Code）：页面上会出现一个
  claude.com 的链接，在自己电脑或手机的浏览器里打开、同意，页面会给一个登录码，粘回网页的输入框
  回车。登录态落在容器里，Wonton 不留 token。
- **Anthropic Console**（给自己账号的 API 用量付费）：流程同上。
- **Anthropic API key**：在 console.anthropic.com 建一个 key，粘进去就行。

订阅登录和 Console 登录会先删掉之前存下的 API key——CLI 里 key 比订阅登录优先，不删就换不过去。
API key 存在数据目录的 `~/.claude/api-key`（0600），不写进日志。

接上网关后网关凭据优先于两者，登录态原样留着；网关卸掉后重启容器就回到原来的登录。

## 构建镜像

```bash
docker build -t ghcr.io/geraldpeng6/wonton-plugin-claude-code:0.1.0 -f images/wonton-plugin-claude-code/Dockerfile images/wonton-plugin-claude-code
```

镜像里只有适配器，闭源的 Claude CLI（约 340 MB）在容器首次启动时从官方 npm registry 按摘要下载到数据卷，
首启要多等一会儿；健康检查以 ACP socket 出现为准。发布后用 `scripts/publish.sh` 把摘要写回 compose。

桥镜像由主仓库维护，`scripts/publish.sh` 只构建本仓库 `images/` 下的 Agent 镜像。

## 条款

Anthropic 允许在自家产品里托管运行未改动的 Claude Code，也允许终端用户登录了官方二进制之后这样用；
不许代付、转售或中转订阅额度。所以这里不碰凭据、不代登录：登录在官方 CLI 里完成，凭据只在本机；
订阅额度按主人自己用。一份订阅只应该给一个人用。订阅账号开着“数据用于改进”时，Claude Code 的用量
会用于训练模型（商业条款另有规定）。细节和出处见 [NOTICE](NOTICE)。

## 图标

`icon.svg` 是按插件规范手绘的线条图标（星号），原创，随插件包以 MIT 许可证分发。Claude、Claude Code
和 Anthropic 是 Anthropic PBC 的商标，本插件不是 Anthropic 的官方出品。上游程序和许可证说明见
[NOTICE](NOTICE)。
