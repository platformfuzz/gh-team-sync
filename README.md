# GitHub Team Sync

Sync your GitHub organization teams from a declarative YAML configuration using the GitHub API.

## Features

- Syncs teams by name, description, and privacy
- Assigns maintainers and members
- Manages repository access and permissions
- Supports dry-run mode for safe testing
- Automated daily synchronization
- Manual trigger support

## Configuration

The team configuration is defined in `teams.yaml`. Here's a detailed example:

```yaml
teams:
  - name: platform-team                # Required: Team name (will be converted to slug)
    description: Platform engineers    # Optional: Team description
    privacy: closed                   # Optional: Team privacy ('closed' or 'secret', defaults to 'closed')
    parent_team_id: 123              # Optional: ID of parent team for nested teams
    create_default_maintainer: false  # Optional: Whether to create default maintainer (defaults to false)
    roles:                           # Optional: List of team members and their roles
      - username: john            # Required: GitHub username
        role: maintainer             # Required: Role ('member' or 'maintainer')
      - username: alice
        role: member
    repositories:                    # Optional: List of repositories and their permissions
      - name: platform-core         # Required: Repository name
        permission: admin           # Required: Permission level
      - name: platform-tools
        permission: push
```

### Team Configuration Parameters

| Parameter | Required | Description | Valid Values | Default |
|-----------|----------|-------------|--------------|---------|
| `name` | Yes | Team name | Any string | - |
| `description` | No | Team description | Any string | - |
| `privacy` | No | Team visibility | `closed` or `secret` | `closed` |
| `parent_team_id` | No | ID of parent team | Number | - |
| `create_default_maintainer` | No | Create default maintainer | `true` or `false` | `false` |
| `roles` | No | Team members and roles | Array of role objects | - |
| `repositories` | No | Repository access and permissions | Array of repository objects | - |

### Role Configuration Parameters

| Parameter | Required | Description | Valid Values |
|-----------|----------|-------------|--------------|
| `username` | Yes | GitHub username | Valid GitHub username |
| `role` | Yes | Team role | `member` or `maintainer` |

### Repository Configuration Parameters

| Parameter | Required | Description | Valid Values |
|-----------|----------|-------------|--------------|
| `name` | Yes | Repository name | Valid repository name in the organization |
| `permission` | Yes | Access level | `pull`, `triage`, `push`, `maintain`, or `admin` |

## Workflow

The synchronization workflow (`sync-teams.yml`) runs:

- Daily at 00:00 UTC
- On pushes to the main branch
- Manually via workflow dispatch

```yaml
- uses: actionsforge/actions-gh-teams-sync@v1
  with:
    config-path: teams.yaml
    dry-run: false
  env:
    GITHUB_TOKEN: ${{ secrets.GH_ORG_TOKEN }}
```

## Setup

1. Create a GitHub organization token with the `admin:org` scope
2. Add the token as a secret named `GH_ORG_TOKEN` in your repository settings
3. Configure your teams in `teams.yaml`
4. Push to main or manually trigger the workflow

## Permissions

To manage organization teams, use a token with the `admin:org` scope.
The default `GITHUB_TOKEN` only works in **organization-owned** repositories and must have the `admin:org` scope enabled via workflow permissions.

## License

This project is licensed under the MIT License.
