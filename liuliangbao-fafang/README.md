# 流量包发放

这是一个可安装的 Codex skill：根据知乎用户主页链接查询 `member_id`，或接收已有的 `member_id` 表格，并在知乎内部流量订单页面完成单个或批量流量包发放。

## 安装

使用 Codex 的技能安装器，指定仓库中的技能目录：

```bash
python ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --url https://github.com/luoxiluoye/liuliangbao-fafang/tree/main/liuliangbao-fafang
```

也可以使用仓库和目录参数安装：

```bash
python ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo luoxiluoye/liuliangbao-fafang \
  --path liuliangbao-fafang
```

## 使用说明

第一次发放时，skill 会先询问：

1. 发放多少曝光或选择哪个流量包；
2. 是否发送私信；
3. 如果发送，完整的私信文案是什么。

用户主页链接解析出唯一 `member_id` 后，会继续完成发放，不会只返回查询结果。默认数量为 1；用户明确采用默认配置时，默认使用 1000 曝光超赞包和知乎数码文案。

## 批量模式

批量发放时，准备一个 Excel 文件，只有一列 `member_id`，每行一个用户 ID；保存后在发放弹窗中选择 `批量发放`，点击上传并选择该文件。上传成功且用户数量核对无误后，商品 ID、商品个数、备注和私信文案与单发模式相同，其中商品个数默认为每位用户 1 个。不同商品或文案需要拆成不同批次。

skill 会校验空行和异常 ID，并按当前页面显示的上传上限处理批次；示例页面显示单个文件最多 1000 个用户 ID，但实际操作以页面实时提示为准。

## 商品包换算

按目标曝光量选择能整除目标的最大商品包：优先 5000 包，其次 1000 包，最后 500 包；商品个数为“目标曝光量 ÷ 单包曝光量”。例如：2000 曝光使用 1000 包、数量 2；1500 曝光使用 500 包、数量 3。目标曝光量不是 500 的整倍数时，不自动凑数或向上取整，会先暂停确认。

## 创作者昵称查询

仓库中的 `data/creator-directory.tsv` 保存了用户提供的创作者目录，字段为 `member_id`、`user_name`、`remark`、`profile_url`。之后直接提供目录中的唯一昵称时，skill 会读取对应 ID 后继续发放；同名昵称不会猜测，会要求补充主页链接确认。该目录位于公开仓库中，不应继续加入密码、Token、手机号等敏感信息。

## 前置条件

- 可使用 `data-in-cli` 和 SQL Gateway 查询 `dw_member.dw_member_pt_info`；
- 已登录并有权限访问知乎内部流量订单页面；
- 可使用 Microsoft Edge 或可用的浏览器控制能力。

详细操作规则见 [SKILL.md](SKILL.md)。
