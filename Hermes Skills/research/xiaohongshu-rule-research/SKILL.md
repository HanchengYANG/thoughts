---
name: xiaohongshu-rule-research
description: Research XiaoHongShu/Xiaohongshu official rules and compliance topics when normal search results are noisy and school.xiaohongshu.com pages are JS-rendered. Uses 360 search to discover rule URLs, then browser_console(document.body.innerText) to extract article text from rendered pages.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [xiaohongshu, xhs, rednote, compliance, policy, research]
---

# Xiaohongshu Rule Research

Use this when a user asks what XiaoHongShu officially allows, restricts, or may limit/reduce distribution for, especially around marketing,导流, food claims, local business promotion, or merchant rules.

## Why this exists

Normal search results for 小红书 queries are often noisy, dominated by forums and SEO spam. `school.xiaohongshu.com` pages are JS-rendered, so raw `requests.get()` output usually contains almost no useful article text even though the browser can render the content.

## Workflow

### 1. Prefer public web research
If the question is about public platform policy, use web/browser research first. Do not inspect local repos/files unless the user explicitly wants project-specific integration.

### 2. Discover official rule/help URLs
Use a general search engine, preferably 360 search (`so.com`), with queries like:
- `site:school.xiaohongshu.com 小红书 导流`
- `site:school.xiaohongshu.com 小红书 违规 营销`
- `site:school.xiaohongshu.com 小红书 食品 功效`
- `site:school.xiaohongshu.com 小红书 社区 规范`
- `site:school.xiaohongshu.com 评论 广告 小红书 规则`

Reason: 360 often surfaces direct `school.xiaohongshu.com/rule/detail/...` URLs better than Bing for Chinese policy queries.

### 3. Open the discovered URL in the browser tool
Use `browser_navigate(url)` even if the snapshot says `Empty page`.

### 4. Extract rendered text via browser console
The important trick:
- Call `browser_console(expression='document.body ? document.body.innerText.slice(0,4000) : ""')`
- For keyword checks, use JSON output, e.g.:
  ```js
  const t=document.body?document.body.innerText:'';
  JSON.stringify({
    has导流:t.includes('导流'),
    has站外:t.includes('站外'),
    has微信:t.includes('微信'),
    has二维码:t.includes('二维码'),
    has功效:t.includes('功效'),
    sample:t.slice(0,2200)
  })
  ```

This works even when raw HTML fetched by terminal/requests is just the JS shell.

### 5. Distinguish source tiers
When answering, separate:

#### Official / high-confidence
Only claims grounded in the rendered text from `school.xiaohongshu.com` rule/help pages.

#### Secondary / lower-confidence
Creator guides, agency posts, community consensus, and SEO articles. Mark these as经验/行业共识, not official platform confirmation.

## Common findings to verify from official text
For merchant/local-business questions, check whether the official page explicitly mentions:
- 站外导流 / 引导站外交易
- 微信号 / 手机号 / 联系方式 / 二维码 / 第三方平台信息
- 虚假交易 / 编造用户评价
- 过度营销信息 / 垃圾信息
- 夸大 / 虚假宣传 / 绝对化表述
- 食品功效、保健功能、医疗/治疗暗示
- 广告、评论区广告、平台社区资源滥用

## Reliable interpretation pattern
Avoid saying “限流词库” or exact suppression thresholds unless the official page explicitly says so.
Instead use language like:
- “官方规则明确禁止/限制……”
- “这类内容存在被删除、屏蔽、限制展示、降权或违规处罚风险”
- “民间常说的‘限流’很多时候未必是官方术语，也可能是推荐弱化或内容质量判断”

## Pitfalls
- Do not rely on raw `requests.get()` page text for `school.xiaohongshu.com` detail pages; it usually misses the real article content.
- Do not overstate community folklore as official policy.
- Do not tell users every low-view post is definitely being punished; separate algorithmic weak distribution from explicit policy enforcement.

## Output format
For practical advice, structure the answer as:
1. 我查到的官方高风险点
2. 对用户场景最相关的红线
3. 哪些只是行业经验，不是官方明文
4. 更稳妥的替代发法

This is especially useful for shops that want to combine XiaoHongShu with an external website or independent ordering flow.
