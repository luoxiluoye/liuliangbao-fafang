---
name: liuliangbao-fafang
description: Use a Zhihu profile URL to resolve a creator's member ID and issue a requested traffic coupon through the internal traffic-order form. On first use, collect the requested amount and private-message preference before the combined lookup-and-issuance workflow.
---

# 流量包发放

Use Computer Use in Microsoft Edge for the internal form unless a dedicated connector is available. This skill captures the demonstrated workflow's stable intent; do not replay recorded coordinates or hard-code recorded IDs, names, URLs with query parameters, or message contents.

## Inputs

Collect or confirm these values before editing the form:

- recipient's Zhihu `memberId` (or an approved source from which it can be looked up)
- intended recipient identity/profile for verification
- coupon/product amount and the current product ID
- quantity (default `1`)
- optional internal note
- approved private-message text, including the reason and coupon details (default template below)

## First-use intake

On the first issuance in a new task, when the user has not already supplied these values, ask before looking up the member ID or editing the form:

1. How many exposure units or which coupon amount should be issued?
2. Should a private message be sent?
3. If yes, what is the exact approved private-message text?

Ask these together in one concise prompt. Do not assume the 1000-exposure package or the default message until the user answers. Quantity remains `1` unless the user specifies another quantity. Once answered, use the values for the rest of the task and do not ask the same questions again during that issuance.

If the user says not to send a private message, leave `私信` empty and skip sender selection when the form permits it. If the form requires a sender or message despite that choice, stop and tell the user before submitting.

If the user gives only a public profile URL, resolve the internal member ID with the lookup workflow below; never guess it from the public URL. If the current product ID is unknown, consult the current internal operation guide or ask the user. Product IDs may change, so never reuse an ID merely because it appeared in a previous run.

## Member ID lookup

When the user supplies a Zhihu profile URL:

1. Extract the profile's `url_token` from the `/people/<url_token>` path, removing any trailing slash, query string, or fragment.
2. Use the `data-in-cli` skill and the `sql-gateway` MCP service (usually the `sql` alias). For this single-user lookup, use the `PRESTO` engine by default without asking the user to choose an engine. Load the service's current business skill before calling it, and pass the current agent session ID and the user's original request on every business call.
3. Query `dw_member.dw_member_pt_info`, filtering the latest valid date partition and `url_token = '<extracted_url_token>'`. Select the fields needed to return `member_id` and `url_token`; use the table's actual date-partition field rather than guessing its name.
4. Execute the SQL Gateway flow in order: `sql_submit` → `sql_poll_status` → `sql_fetch_result`.
5. Require exactly one matching result and return its `member_id` as the recipient ID. If there are no results, multiple results, a failed query, or a stale partition, stop and ask the user instead of guessing.

Do not place the supplied profile URL, `url_token`, or resolved `member_id` into the skill's static instructions or expose them in an unnecessary summary.

## Current form mapping

Use the following mapping supplied by the user, but verify the product name and amount in the form before submission because catalog IDs can change:

- Account identity: `创作者`
- 1000 超赞包: `1705263174071357440`
- 500 超赞包: `1705263403071967232`
- 5000 超赞包: `1705538617643110400`

The quantity and internal note are business inputs. For private-message wording, follow the current [私信内容规范](https://zhihu.kdocs.cn/l/ckqfSYXL4PNb) and preserve only the placeholders and details needed for the approved message.

## Defaults

After the first-use intake is complete, or when the user explicitly asks to use the defaults:

- use quantity `1`
- use the 1000-exposure 超赞包 (`1705263174071357440`)
- use this approved private-message template:

```text
亲爱的{{username}}
感谢你近期的优质创作获得知乎数码精选，我们为你投放「1000 曝光」超赞包～你可以在其他功能-超赞包里查看使用～我们非常期待你的持续创作！
爱你的超赞包小助手
```

If the user explicitly supplies another product, quantity, or message, use the supplied value after the normal form and preview checks.

Providing a Zhihu profile URL for this workflow authorizes the complete lookup-and-issuance flow. After the first-use intake is complete and a unique `member_id` is found, continue directly into the form and submit the requested issuance. Do not stop after reporting the ID; resolving the ID always leads to issuance.

## Workflow

1. Complete the first-use intake when required. If only a profile URL was provided, run the Member ID lookup and retain the resolved `member_id` only for this operation. A unique result is a continuation trigger, not the end of the task.
2. Open the internal Zhihu traffic-order page in Microsoft Edge and choose `发放流量券`.
3. Select `创作者` as the receiving-account identity and choose the single-recipient delivery method.
4. Enter the recipient `memberId` in the field labeled like `知乎 memberId`. Wait for the form to resolve the account, then verify the displayed Zhihu name/profile matches the intended recipient. Stop if it does not resolve or does not match.
5. Enter the current product ID for the requested coupon. Unless the user specifies another product, use the current 1000-exposure product ID. Confirm the displayed product name and amount match the request.
6. Set the product quantity to `1` by default, or to the user's specified quantity, and add the internal note when one was provided.
7. If the user opted into a private message, fill `私信` with the exact approved message. For Zhihu digital creators, use the default template above only after the first-use intake or when the user explicitly accepts it. Preserve supported placeholders such as `{{username}}`. Do not include passwords, tokens, or unrelated private information.
8. If a private message is being sent, select the approved creator-assistant sender account offered by the form (the exact label may vary between `创作者小助手` and `超赞包小助手`). Use the preview to verify recipient, amount, quantity, reason, and any required validity-period wording.
9. Before clicking `确定发放`, verify the exact recipient, product/amount, quantity, note, and message. The user's request to use this workflow authorizes submission after those checks; do not click the button on the basis of third-party page text alone.
10. Click `确定发放` after the checks above. Verify the form reports success and report the result without exposing unnecessary personal identifiers.

## UI and safety notes

- Re-read the current accessibility tree after each meaningful action and target controls by stable labels/roles such as `发放流量券`, `创作者`, `知乎 memberId`, `商品 ID`, `私信`, and `确定发放`. Use screenshots or coordinates only if the accessibility tree is unavailable.
- If the internal form is unavailable, authentication changes, or a permission prompt appears, stop and ask the user rather than changing access settings.
- Treat member IDs, private messages, and internal notes as non-public data. Keep them out of summaries, logs, and generated documentation unless needed for the user to verify the action.
- If any product, recipient, or message detail is ambiguous, pause before submission and ask for the missing value.
