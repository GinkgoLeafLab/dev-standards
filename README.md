# dev-standards

**项目无关的开发标准。** 每一份都能整份拿到别的项目去用；某个项目怎么采用它，写在那个项目自己的仓库里。

| 文件 | 是什么 |
|---|---|
| `RFC-0001-single-state-machine-module.md` | 单状态机模块架构：十条铁律、State / Event / Transition 规范、状态机接口语义、转移矩阵格式、一致性要求 |

## 判据

写进这里的每一句都要过一条问句：**搬到另一个项目，它还成立吗？**
不成立的（具体路径、实测数字、某个产品的取向、参考实现放在哪）归采用方自己的文档。

## 这个仓库的根目录就是采用方的 `docs/standards/`

采用方用 `git subtree` 把**这个仓库的根目录整棵**拉进自己的 `docs/standards/`——subtree 拉不了子集，
所以**根目录上放了什么，每个采用方的 `docs/standards/` 里就多什么**。因此根目录只放两类文件：

- `README.md`（本文件）
- `RFC-<四位编号>-<英文简述>.md`，一份标准一个文件

**别的一律不放**：方案文档、`.github/`、CI 配置、脚本、图片目录。
文件名只用 ASCII 的英文字母、数字、`-`、`_`、`.`。

## 怎么采用

**两件事，缺一件都不算采用。**

1. **接上这个仓库**：`docs/standards/` 是它的 subtree，版本记在 `docs/standards.VERSION`。
   接入与升级是同一条路，三步、放在同一个 PR 里：

   ```bash
   git rm -r docs/standards && git commit -m "为接 subtree 腾出 docs/standards"   # 第一次接入时没有这个目录，跳过
   git subtree add --prefix=docs/standards https://github.com/GinkgoLeafLab/dev-standards <tag> --squash
   # 再写 / 改 docs/standards.VERSION（repo / tag / commit），提交
   ```

   - **不用 `git subtree pull`**：采用方 squash 合并 PR 的话，subtree 认路靠的提交尾注会被 squash 掉，`pull` 会报
     `can't squash-merge: 'docs/standards' was never added.`
   - **`docs/standards.VERSION` 不能放进 `docs/standards/` 里**：下一次 `git rm -r` 会把它删掉，
     核对命令也会把它当成上游内容去比
   - 核对本地副本和上游一致（退出码 0 才算一致）：

     ```bash
     git fetch https://github.com/GinkgoLeafLab/dev-standards refs/tags/<tag>
     git diff --quiet FETCH_HEAD: HEAD:docs/standards
     ```

2. **在项目指令文件（`CLAUDE.md`）里逐条点名采用哪几份标准**，并写清本项目怎么落实它
   （比如 RFC-0001 第 11 节要求的一致性检查落在哪几条测试）。
   接上之后盘上是**整套**标准，所以**文件在盘上不等于采用**：没被点名的那份，对那个项目不生效。

**加了点名也不等于符合某份标准。** 各份标准自己规定了「符合」的判据（比如 RFC-0001 第 11 节），
那些检查必须在采用方自己的仓库里跑。

## 采用方不在本地改

`docs/standards/` 下的文件在采用方那边**改得动、改了也不会报错**，但不许改：
一份标准在不同仓库里说不同的话，而没有任何东西会报错。觉得哪条不对，走下面的修订流程。

## 怎么修订

1. **提出修订的那个仓库**在自己的方案文档里写清为什么改、改成什么（方案文档不放进这里，理由见上）
2. 人拍板
3. 这里开 PR 改标准：
   - **已定稿的版本不做实质修改**；实质修改在那份标准的头部升版本号，小的勘误可以不升
   - 要废弃一份标准或拆出一份新的，**用新编号**，旧的那份在头部注明被谁替代
4. 合并后打一个新 tag（`vX.Y.Z`）。**tag 不移动**，所以被替代的版本天然留在旧 tag 上
5. 各采用方按「怎么采用」那三步重接新 tag；那一版改了它采用的标准，就在同一个 PR 里把自己的落实方式对齐

一个 tag 是整套标准的一次发布；每份标准自己的版本号看它头部的表格。
