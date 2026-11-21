# qBittorrentSpeedManager

一个用于管理 qBittorrent 的轻量 Python 工具：按状态筛选种子，批量设置上传 / 下载速度限制，并重新汇报（reannounce）种子。

## 功能

- 按指定状态获取种子列表（哈希、状态等信息）
- 对筛选出的种子批量设置上传 / 下载限速
- 重新汇报（reannounce）所有种子
- 一键执行完整流程
- 可选：清理下载目录中的 `.torrent` 文件

## 文件说明

| 文件 | 作用 |
|---|---|
| `main.py` | 主入口：调用 `get_hash.py` 获取种子，再调用 `qbittorrent_manager.py` 设置限速 |
| `get_hash.py` | 登录 qBittorrent，按配置筛选种子列表并输出 JSON |
| `qbittorrent_manager.py` | 登录后 reannounce，并对指定种子设置上传 / 下载限速，日志写入文件 |
| `clean_torrent_files.py` | 删除指定目录下所有 `.torrent` 文件（独立脚本） |
| `config.json` | 配置：账号、URL、筛选条件、限速值 |
| `requirements.txt` | Python 依赖（`requests`） |

## 环境要求

- Python 3
- 已开启 Web UI 的 qBittorrent 客户端

```bash
pip install -r requirements.txt
```

## 配置

编辑 `config.json`：

| 字段 | 说明 |
|---|---|
| `qbittorrent_username` | qBittorrent Web UI 用户名 |
| `qbittorrent_password` | qBittorrent Web UI 密码 |
| `qb_url` | Web UI 地址，如 `http://localhost:8080` |
| `state` | 状态过滤，如 `downloading` / `seeding` / `all`（对应 qBittorrent filter 参数） |
| `category` | 分类过滤（可选） |
| `tag` | 标签过滤（可选） |
| `sort` | 排序字段（可选） |
| `reverse` | 是否倒序（可选） |
| `limit` | 返回数量限制（可选） |
| `offset` | 偏移量（可选） |
| `upload_limit` | 上传限速，单位 **字节/秒**，默认 `30000000`（约 28.6 MB/s） |
| `download_limit` | 下载限速，单位 **字节/秒**，默认 `10000000`（约 9.5 MB/s） |

> `config.json` 已在 `.gitignore` 中忽略，填入真实密码后不会被提交。

## 使用

```bash
python main.py
```

执行流程：`get_hash.py` 获取指定状态的种子 → 提取哈希 → `qbittorrent_manager.py` 对它们设置上传 / 下载限速（并 reannounce 所有种子）。

运行日志写入 `qbittorrent_management_log.txt`。

## 可选：清理 .torrent 文件

```bash
python clean_torrent_files.py
```

> 脚本中的下载目录为硬编码的 `F:\\Downlord`，请按自己实际情况修改。

## 注意事项

- 限速单位是 **字节/秒**：1 MB/s ≈ `1048576`。
- `reannounce` 作用于**所有**种子（`hashes=all`），不止筛选出的种子。
- 运行前请确认 qBittorrent 的 Web UI 已开启，且允许本地访问。
