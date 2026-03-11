# Feishu Task Management

Feishu Task skill plus companion toolkit for managing Feishu tasks with mixed auth:

- contact sync uses app auth
- task APIs prefer user auth when OAuth user tokens are available
- task APIs fall back to app auth when no user token is configured

## Recommended Path

Default to the toolkit configuration guide:

```bash
python3 feishu-task-management/toolkit/scripts/feishu_config.py guide
```

The guide now acts as an interactive terminal wizard. It asks for:

- `App ID`
- `App Secret`
- whether to continue with user OAuth setup
- optional `scope`
- a fixed default `redirect_uri`
- it prints an authorization URL
- you paste back the full callback URL
- optional default member `Open ID`
- confirmation before writing `runtime.json`
- whether to sync the member mapping immediately

After the wizard finishes, validate the effective config:

```bash
python3 feishu-task-management/toolkit/scripts/feishu_config.py validate
```

## If You Only Consume The Skill

If you plan to use only `feishu-task-management` and not run the toolkit setup interactively, prepare the toolkit inputs first. The skill assumes these files or values already exist.

### Required Configuration

Recommended mixed configuration:

1. Environment variables

```bash
export FEISHU_APP_ID=cli_xxx
export FEISHU_APP_SECRET=xxx
export FEISHU_USER_ACCESS_TOKEN=u-xxx
export FEISHU_REFRESH_TOKEN=refresh-xxx
export FEISHU_USER_SCOPE="task:task:read task:task:write"
export FEISHU_BASE_URL=https://open.feishu.cn/open-apis
export FEISHU_TIMEZONE=Asia/Shanghai
```

2. Local runtime config at `feishu-task-management/toolkit/config/runtime.json`

```json
{
  "app_id": "cli_xxx",
  "app_secret": "xxx",
  "user_access_token": "u-xxx",
  "refresh_token": "refresh-xxx",
  "user_token_expires_at": "2026-03-10T18:00:00+08:00",
  "user_scope": "task:task:read task:task:write",
  "base_url": "https://open.feishu.cn/open-apis",
  "timezone": "Asia/Shanghai",
  "default_member_open_id": "ou_xxx"
}
```

App-only configuration is still valid when you do not have OAuth user tokens yet:

```bash
export FEISHU_APP_ID=cli_xxx
export FEISHU_APP_SECRET=xxx
```

### OAuth User Notes

- the preferred path is generating an authorization URL and exchanging the returned code into user tokens
- the guide uses a fixed default callback URL: `https://example.com/feishu/oauth/callback`
- the toolkit stores `refresh_token` only when `offline_access` is granted
- the toolkit does not implement browser OAuth login end-to-end yet
- the toolkit does not auto-refresh tokens yet

### Recommended Member Data

Before member-based task operations, generate the local member table:

```bash
python3 feishu-task-management/toolkit/scripts/feishu_members.py sync
```

Optional manual alias file at `feishu-task-management/toolkit/data/member_aliases.json`:

```json
{
  "张三": "ou_xxx",
  "haojun": "ou_yyy"
}
```

## Main Paths

- Skill: `feishu-task-management/SKILL.md`
- Toolkit config: `feishu-task-management/toolkit/scripts/feishu_config.py`
- Toolkit members: `feishu-task-management/toolkit/scripts/feishu_members.py`
- Toolkit tasks: `feishu-task-management/toolkit/scripts/feishu_task.py`
