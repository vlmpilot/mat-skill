# MAT mobileapp-testing skill

让 AI agent(Claude Code / Trae / CodeBuddy / ZCode 等,凡支持 SKILL.md 标准)用自然语言驱动 **MAT** 在真机上做移动 App UI 自动化测试:

- **自然语言 → 用例**:`mat gen` 一句话生成结构化测试用例(带页面图谱防幻觉)
- **真机执行**:截图级步骤证据、每步五分类失败归因、`run.json` 精简结果(agent 读数据,人看 HTML 报告)
- **自愈**:App 改版后定位器断裂,`mat heal` 出提案、回写为新文件不覆盖原用例
- **遍历探索**:真机自动遍历 App 产出页面图谱,顺带发现崩溃/白屏
- **LLM 归因**:失败步骤四分类(app-bug / ui-change / env-flake / precondition)+ 证据 + 建议

## 安装

### 方式一:随包自举(推荐——skill 随 MAT 版本同步更新)

```bash
pip install mat-uitest
mat skill install        # 装进当前 AI 助手的 skill 目录;mat skill path 可查看位置
```

`mat skill install` 装的是**你装的那个 mat-uitest 版本里自带的那份 skill**,所以先确认版本:

```bash
mat --version            # 低于下表版本时,包内 skill 也是旧的
```

WorkBuddy 用 `mat skill install --target workbuddy`(默认落点是 Claude Code 的 `~/.claude/skills/`),
其他助手先跑 `mat skill targets` 看已知落点,不确定就 `--target agents`(通用目录,国内多数助手都读它);
已存在且内容不同时默认不覆盖,要覆盖加 `--force`。

### 方式二:直接拷贝本仓库

把 `mobileapp-testing/` 整个目录复制到你的 agent skills 目录,例如:

| Agent | skills 目录 |
|---|---|
| Claude Code / CodeBuddy 系 | `~/.claude/skills/` |
| WorkBuddy | `~/.workbuddy/skills/` |
| Trae(国内版) | `~/.trae-cn/skills/` |
| QoderWork CN | `~/.qoderwork/skills/` |
| 通用(魔搭/npx 生态默认) | `~/.agents/skills/` |
| ZCode | 会话/插件 skill 目录(或打包为插件) |
| 其他 IDE | 用各自 IDE 的「技能上传」入口选 `SKILL.md`,或把 `mobileapp-testing/` 目录打包成 zip 再上传 |

**适用于助手不能执行终端命令的场合。**注意两点:手动拷的副本**不会自动更新**(MAT 升级后要重拷一次);
本仓与随包版本**以随包为准**(`mat skill path` 打印的目录就是当前 MAT 自带的那份)。

### 方式三:插件市场(一条命令订阅,自动更新)

CodeBuddy / WorkBuddy 这类支持插件市场的助手,可以直接把本仓当市场源订阅:

```
/plugin marketplace add vlmpilot/mat-skill
/plugin install mobileapp-testing@mat-skill
```

之后市场更新时插件跟着更新,不用手动重拷。仓库里两份清单内容相同,只是目录名跟各家的读取约定走:
`.claude-plugin/`(Claude Code)、`.codebuddy-plugin/`(CodeBuddy / WorkBuddy),
都由主仓 `scripts/sync_skill_repo.sh` 从模板生成并注入版本号——**不要在网页上直接改这两个 JSON**,
改了下次同步会被覆盖。

## 前置条件

- **Android 真机**:开启 USB 调试并连接(单设备零配置);`mat doctor` 自检环境
- **iOS**(可选):Xcode + 模拟器或 USB 真机,`mat ios-setup` 体检
- **AI 能力**(gen / diagnose 需要):官方代理 token 或自带 OpenAI 兼容 Key;
  无 Key 时 run / explore / index / heal / devices / summary 全部可用(本地执行与自愈不依赖 AI)

## 版本对应

| skill 内容 | 对应 mat-uitest |
|---|---|
| 本仓库当前 main | ≥ 0.7.1 |

- `mat --version` **低于上表**时,包内那份 skill 也是旧的(没有 `mat summary`、没有凭据段),
  这时用**方式二**取本仓版本,或先升级 mat-uitest 再 `mat skill install --force`。
- 本仓 `mobileapp-testing/` 是**随包版本的镜像**,由主仓的 `scripts/sync_skill_repo.sh` 单向同步;
  不要在 GitHub 网页上直接改这里的 `SKILL.md`,改了下次同步会被覆盖——改动请提到主仓。
- skill 的 Setup 段可让 agent 在**未安装 MAT 的机器上自举**(检测 → uv/pip 安装 → 验证)。

## 许可

AGPL-3.0-or-later,随 [MAT 开源核心](https://github.com/vlmpilot/mat-uitest)分发;完整许可文本见仓库根 LICENSE。
