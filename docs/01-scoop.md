# Scoop：Windows 上的命令行包管理器

## 为什么推荐 Scoop

Scoop 是 Windows 上的命令行安装器，一句话概括：**没有弹窗、没有安装向导、不会污染 PATH**。

用 Scoop 装 Node.js 和 Git，比手动下载安装包方便很多：

- `scoop install nodejs` → 一条命令搞定 Node.js
- `scoop install git` → 一条命令搞定 Git
- 卸载干净：`scoop uninstall nodejs`
- 所有程序隔离在 `~\scoop` 目录，不影响系统环境

## 安装 Scoop

打开 **PowerShell**（版本 5.1 以上），执行：

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```

验证安装：

```powershell
scoop --version
```

## 安装 Node.js

```powershell
scoop install nodejs
```

安装后验证：

```powershell
node --version
npm --version
```

## 安装 Git

```powershell
scoop install git
```

安装后验证：

```powershell
git --version
```

## 常用命令

```powershell
scoop search <app>    # 搜索软件包
scoop install <app>   # 安装
scoop uninstall <app> # 卸载
scoop update          # 更新 Scoop 自身
scoop update *        # 更新所有已安装的包
scoop list            # 查看已安装的包
scoop status          # 查看哪些包有更新
```

## 一键安装本指南需要的工具

如果你刚配好 Windows 环境，直接执行下面三条：

```powershell
# 1. 装 Scoop
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression

# 2. 装 Node.js 和 Git
scoop install nodejs git
```

---

参考：[Scoop 官网](https://scoop.sh/) | [Scoop GitHub](https://github.com/ScoopInstaller/Scoop)
