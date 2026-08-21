# 流量包发放

这是一个可安装的 Codex skill：根据知乎用户主页链接查询 `member_id`，并在知乎内部流量订单页面完成流量包发放。

## 安装

使用 Codex 的技能安装器，指定仓库根目录：

```bash
python ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --url https://github.com/luoxiluoye/liuliangbao-fafang/tree/main/liuliangbao-fafang
```

也可以直接将此仓库克隆到 Codex skills 目录：

```bash
git clone https://github.com/luoxiluoye/liuliangbao-fafang.git \
  ~/.codex/skills/liuliangbao-fafang
```

## 使用说明

第一次发放时，skill 会先询问：

1. 发放多少曝光或选择哪个流量包；
2. 是否发送私信；
3. 如果发送，完整的私信文案是什么。

用户主页链接解析出唯一 `member_id` 后，会继续完成发放，不会只返回查询结果。默认数量为 1；用户明确采用默认配置时，默认使用 1000 曝光超赞包和知乎数码文案。

## 前置条件

- 可使用 `data-in-cli` 和 SQL Gateway 查询 `dw_member.dw_member_pt_info`；
- 已登录并有权限访问知乎内部流量订单页面；
- 可使用 Microsoft Edge 或可用的浏览器控制能力。

详细操作规则见 [SKILL.md](SKILL.md)。
