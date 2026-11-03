---
title: 把 Git 历史瘦下来
parent: 建站笔记
nav_order: 2
---

# 把 Git 历史瘦下来
{: .no_toc }

## 一句话

**删文件不会让仓库变小**。只有当旧提交不再被任何分支或标签引用，那些大对象才会被回收。

## 原理

git 是内容寻址存储：

```text
ref（分支/标签） → commit → tree → blob（文件内容）
```

- 在新提交里删掉文件，只是**新树**没有它；旧提交仍指向旧树，blob 依然「可达」
- 只要还有 ref 能走到旧提交，`git clone` 就照样把大文件拉下来
- 重写历史 = 重建 tree / commit，把大 blob 从所有历史版本里剔除 → 旧对象不再可达
- 不可达不等于已删除：还要清 reflog 并 gc，对象才会真正消失

## 操作

```bash
# 0. 备份（唯一后悔药）
git bundle create ../pre-rewrite.bundle --all

# 1. 重写：剔除历史里大于 2MiB 的 blob
python git-filter-repo --strip-blobs-bigger-than 2M --force

# 2. 清引用、回收对象
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# 3. 强推（filter-repo 会移除 origin，先加回）
git remote add origin <仓库地址>
git push --force origin master
```

如果只想删某个文件，把第 1 步换成：

```bash
python git-filter-repo --invert-paths --path assets/media/xxx.mp4 --force
```

## 阈值怎么定

阈值必须**大于当前工作区里最大的文件**，否则现在还在用的文件会被一起剔除。

```text
当前最大文件   2,088,691 字节
2 MiB          2,097,152 字节   ← 阈值放这里才安全
```

判断依据：

| 情况 | 结果 |
| --- | --- |
| 阈值 > 当前最大文件 | 只清历史旧版本，站点不受影响 |
| 阈值 ≤ 当前最大文件 | 连当前版本也会被删，工作区文件消失 |

## 代价

| 项 | 影响 |
| --- | --- |
| commit hash | 全部改变，别的机器要重新 clone |
| 标签 | 指向的提交被重写（Release 仍按标签名挂载） |
| 远端 | 旧对象要等 GitHub 自己回收，体积不会立刻变化 |
| 备份 | 重写前一定先 bundle，否则回不到旧历史 |

## 验证清单

- `git rev-list --objects --all | grep <大文件>` 输出为空
- `git count-objects -vH` 和 `.git` 目录体积明显下降
- 构建通过，文章配图、头像等当前资源仍能正常加载
- CI 与 Pages 部署都是绿的

## 实测

```text
本地 .git     187.4 MB → 38.1 MB（−79.7%）
GitHub 仓库   36.8 MB
剔除内容      72MB 视频 + 旧头像/旧配图约 50MB
```

两次重写分别踩过的坑：

1. 只删 `assets/media/...` 路径不够——同一个 blob 还挂在旧路径 `_posts/images/...` 下
2. 清完 master 还要清 `backup`、`upgrade`、`备份` 分支和 `v*` 标签，否则对象依旧可达
3. `gc` 之后体积没降，多半是 reflog 还引用着旧提交
