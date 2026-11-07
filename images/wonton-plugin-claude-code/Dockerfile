# Claude Code 子镜像：ACP 适配器（Apache-2.0）随镜像发布；它带的闭源 Claude CLI（约 340 MB）由底座
# 首次启动按 agent.toml 的 [[install]] 从 npm 下载到数据卷。入口是底座的 wonton-agent，这里只放配置。
FROM ghcr.io/geraldpeng6/wonton-agent-base:0.1.0@sha256:9d6e0ca87c14eee21b5b6503fb52dd863182e7a4f784dd644a4e8cb3f58989d7

COPY agent.toml /etc/wonton-agent/
COPY --chmod=0755 gateway oauth-login auth-status api-key-login api-key api-key-status /usr/local/lib/wonton-agent/
COPY agent_settings.py /usr/local/lib/wonton-agent/
COPY prompt.md /usr/local/share/wonton-agent/prompt.md
# skill 目录 COPY 整棵树：COPY --chmod 会连自动创建的目录一起改（0644 会丢掉目录的 x 位）。
COPY skills/ /usr/local/share/wonton-agent/skills/
COPY LICENSE.claude-agent-acp LICENSE.claude-code /usr/share/doc/wonton-claude-code/
