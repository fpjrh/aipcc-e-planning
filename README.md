# AIPCC-E Planning Agent

Automated release planning report generator for AIPCC Ecosystems team. Queries Jira for issues by target version, sorts by Team → Priority → Due Date, and generates Google Doc + Sheet outputs.

## Features

- ✅ Query Jira by issue type and target version (with pattern matching)
- ✅ Filter by custom Team field
- ✅ Sort by Team → Priority → Due Date
- ✅ Exclude Done/Closed issues (configurable)
- ✅ Generate Google Doc (formatted report)
- ✅ Generate Google Sheet (data table with filters)
- ✅ Auto-share with configured users

## Setup

### 1. Install Dependencies

```bash
cd aipcc-e-planning
python -m venv venv
source venv/bin/activate  # macOS/Linux
pip install -r requirements.txt
```

### 2. Configure

**Find your Team custom field ID:**

```bash
curl -u $JIRA_EMAIL:$JIRA_API_TOKEN \
  https://redhat.atlassian.net/rest/api/3/field | \
  jq '.[] | select(.name | contains("Team"))'
```

**Edit `config/config.yaml`:**
- Set `custom_fields.team` to your Team field ID
- Set `target_versions` (e.g., `["RHEL AI 1.2"]` or `["RHEL AI 1.*"]`)
- Set `drive_folder_id` for report output
- Configure issue types, statuses to exclude, etc.

### 3. Authenticate

First run will open browser for Google OAuth:

```bash
python src/main.py --dry-run
```

## Usage

### Dry Run (No Document Creation)

```bash
python src/main.py --dry-run
```

Shows summary without creating documents.

### Full Run

```bash
python src/main.py
```

Creates both Google Doc and Google Sheet in configured folder.

### Custom Config

```bash
python src/main.py --config path/to/config.yaml
```

## Configuration

Key settings in `config/config.yaml`:

**Jira Query:**
- `project`: Jira project key
- `issue_types`: List of types to include (prompts if empty)
- `target_versions`: Versions to query (supports `*` wildcards)
- `custom_fields.team`: Team custom field ID
- `exclude_statuses`: Statuses to filter out

**Output:**
- `drive_folder_id`: Google Drive folder for reports
- `share_with`: Email addresses to share with
- `doc_title_format` / `sheet_title_format`: Title templates

## Output

**Google Doc:**
- Summary statistics
- Issues grouped by team
- Sub-sorted by priority, then due date

**Google Sheet:**
- All fields in table format
- Frozen headers
- Auto-filter enabled
- Auto-resized columns

## Fields Displayed

- Key
- Type
- Summary
- Team
- Priority
- Due Date
- Status
- Assignee
- Story Points
- Target Version
- Labels
- Parent
- URL

## Troubleshooting

**No issues found:**
- Check your `target_versions` configuration
- Verify JQL query in logs
- Ensure Team field ID is correct

**Authentication errors:**
- Delete `config/credentials/google_token.pickle`
- Re-run to trigger new OAuth flow

**Custom field not showing:**
- Find correct field ID with curl command above
- Update `config.yaml` with actual field ID

## Example Configuration

```yaml
jira:
  project: "AIPCC"
  issue_types: ["Initiative", "Feature", "Epic"]
  target_versions: ["RHEL AI 1.*"]
  custom_fields:
    team: "customfield_12345"
  exclude_statuses: ["Done", "Closed"]
```

## Development

Based on weekly-status-agent architecture. Reuses:
- Authentication (Google & Jira)
- Utility functions
- Configuration patterns
- Output generation patterns

## License

MIT
