---
name: ps-utf8-io
description: 在 Windows PowerShell 中读取 / 写入本地文本文件时避免乱码。只要需要用 PowerShell 查看文件内容（例如 Get-Content/type）或写入文本（Set-Content/Out-File/Add-Content），就使用此技能并强制显式指定 UTF-8 编码。
---

# PowerShell UTF-8 I/O

## Read

- Prefer: `Get-Content -Path <path> -Raw -Encoding utf8`
- Avoid relying on PowerShell defaults (they may decode UTF-8 files incorrectly).

## Write

- Prefer: `Set-Content -Path <path> -Encoding utf8`
- Prefer redirection via: `Out-File -FilePath <path> -Encoding utf8`
- Prefer append via: `Add-Content -Path <path> -Encoding utf8`

## Notes

- If a file is not UTF-8, detect/convert first (or ask the user to confirm the correct encoding) before processing.