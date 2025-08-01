> 当有多台设备同时使用文档是，往往冲突：
> 😖 Obsidian 的笔记冲突问题，尤其是在使用 Git 同步时，确实让人头疼。最常见的冲突源头是 `.obsidian` 文件夹里的配置文件，比如 `workspace.json` 和插件的 `data.json`，因为这些文件在不同设备上打开笔记时都会自动更新。

以下是一些社区公认的做法2：

#### 1. 忽略 `.obsidian` 文件夹

在你的仓库根目录添加 `.gitignore` 文件，内容如下：

```
.obsidian/
.trash/
```

这样可以避免同步设备特定的配置文件。

#### 2. 如果已经被 Git 跟踪了怎么办？

你需要先取消 Git 对这些文件的跟踪：

```bash
git rm --cached -f .obsidian/workspace.json
```

然后再添加到 `.gitignore` 并提交：

```bash
echo ".obsidian/workspace.json" >> .gitignore
git add .gitignore
git commit -m "Ignore workspace.json to prevent conflicts"
```