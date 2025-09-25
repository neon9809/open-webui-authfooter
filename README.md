# [Open WebUI 👋](https://github.com/open-webui/open-webui)

![GitHub stars](https://img.shields.io/github/stars/open-webui/open-webui?style=social)
![GitHub forks](https://img.shields.io/github/forks/open-webui/open-webui?style=social)
![GitHub watchers](https://img.shields.io/github/watchers/open-webui/open-webui?style=social)
![GitHub repo size](https://img.shields.io/github/repo-size/open-webui/open-webui)
![GitHub language count](https://img.shields.io/github/languages/count/open-webui/open-webui)
![GitHub top language](https://img.shields.io/github/languages/top/open-webui/open-webui)
![GitHub last commit](https://img.shields.io/github/last-commit/open-webui/open-webui?color=red)
[![Discord](https://img.shields.io/badge/Discord-Open_WebUI-blue?logo=discord&logoColor=white)](https://discord.gg/5rJgQTnV4s)
[![](https://img.shields.io/static/v1?label=Sponsor&message=%E2%9D%A4&logo=GitHub&color=%23fe8e86)](https://github.com/sponsors/tjbck)

**Open WebUI is an [extensible](https://docs.openwebui.com/features/plugin/), feature-rich, and user-friendly self-hosted AI platform designed to operate entirely offline.** It supports various LLM runners like **Ollama** and **OpenAI-compatible APIs**, with **built-in inference engine** for RAG, making it a **powerful AI deployment solution**.

---

这是[Open WebUI](https://github.com/open-webui/open-webui)项目的分支版本，新增了在`/auth`页面底部显示页脚的功能。此修改通过外部`registration.html`文件实现注册内容的动态加载，无需重建Docker镜像即可轻松更新。
**Open WebUI**是一款[可扩展](https://openwebui.com/)、功能丰富且用户友好的自托管AI平台，专为完全离线运行设计。它支持**Ollama**等多种大型语言模型运行器及**OpenAI兼容API**，内置用于RAG的**推理引擎**，堪称**强大的AI部署解决方案**。

## ✨ 新增功能：注册信息展示
此分叉仓库包含一项修改，可在登录/注册页面（`/auth`）显示自定义页脚。内容通过外部`registration.html`文件动态加载，该文件可挂载至Docker容器，实现灵活更新。

### 工作原理：
1.  **前端修改**：`src/routes/auth/+page.svelte` 文件已更新为从 `/static/registration.html` 获取内容，并在认证页面底部渲染。
2.  **后端静态文件服务**：OpenWebUI 的 Flask 后端已配置为从 `backend/open_webui/static` 目录提供静态文件服务。`registration.html`文件通过挂载方式置于Docker容器内的该目录中。
3.  **外部挂载**：您可将`registration.html`文件放置于主机项目根目录，通过挂载方式引入Docker容器，实现无需重建镜像即可更新的功能。

具体修改请见Release Note v0.6.26。

（仅提供了amd64/arm64版本 如需其他版本可自行build）

### 快速配置注册功能：
克隆此分叉仓库后，请按以下步骤启用该功能：
1.  **创建 `registration.html` 文件**：在项目根目录（与 `Dockerfile` 和 `docker-compose.yml` 同级目录）创建名为 `registration.html` 的文件，并按需编写 HTML 内容。
2.  **更新 `docker-compose.yml`**：修改 `open-webui` 服务的 `volumes` 部分，挂载 `registration.html`：
```yaml
        volumes:
          - ./registration.html:/app/backend/open_webui/static/registration.html:ro
```
4.  **运行**：构建自定义 Docker 镜像并启动服务：
```bash
    docker-compose up -d
```

# 注意 ⚠️
商业用途需购买商业许可证！请联系 [Open-WebUI](https://docs.openwebui.com/enterprise) 进行进一步洽谈。


## 故障排除

请观察浏览器控制台输出：

-   **`404 Not Found` 错误**：
    -   **原因**：最常见的原因是 `registration.html` 文件没有被正确地挂载到容器内部的 `/app/backend/open_webui/static/` 路径，或者文件本身不存在或权限不正确。
    -   **解决方案**：
        -   仔细检查 `docker-compose.yml` 中 `volumes` 配置的路径是否正确：`./registration.html:/app/backend/open_webui/static/registration.html:ro`。
        -   确认宿主机上 `registration.html` 文件的绝对路径和读取权限。确保OpenWebUI容器内的用户（通常是 `root` 或 `node` 用户）有读取权限。
        -   检查Docker容器的日志 (`docker-compose logs open-webui`)，看是否有关于文件访问的错误信息。

-   **footer不显示**：
    -   **原因**：除了404问题外，可能是Svelte组件中的条件渲染逻辑 (`{#if registrationLoaded && registrationContent}`) 未满足，或者 `registration.html` 文件内容为空。
    -   **解决方案**：
        -   确保 `registration.html` 文件中有实际的HTML内容。
        -   检查浏览器控制台，看是否有JavaScript错误，这可能会阻止Svelte组件的正常渲染。
