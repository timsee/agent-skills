---
name: datadog-debug
description: Debug a deployed service with Datadog. Picks the Datadog skill for an exception, a slow endpoint, odd runtime values, a bad deploy, or an alert. Use when a bug shows up in production or staging that the code alone doesn't explain.
metadata:
  version: "0.2.0"
  author: datadog-labs
  repository: https://github.com/datadog-labs/agent-skills
  tags: datadog,debugging,live-debugger,error-tracking,apm,logs
  status: beta
---

# Debug with Datadog

Find the problem below, load the Datadog skill it names, and follow it.

**Loading a Datadog skill:** call `load_datadog_skill` with the name shown, such as `load_datadog_skill("datadog/error-tracking")`. The Datadog MCP server is starting to serve skills as MCP resources too; if your client lists `skill://` resources from it, read `skill://<resource name>/SKILL.md` instead. Use whichever your client has. If neither is there, the Datadog MCP server isn't connected: tell the user, and run the `dd-coding-agent-mcp` skill with `core,apm,error-tracking,live-debugger` if it's installed.

## Investigate

- **An exception or a new error.** `load_datadog_skill("datadog/error-tracking")` · resource `datadog/error-tracking/overview`
- **A slow or failing endpoint, or a request across services.** `load_datadog_skill("datadog/traces")` · resource `datadog/apm/traces`
- **Finding or reading the service's logs.** `load_datadog_skill("datadog/logs")` · resource `datadog/logs/search`
- **What a variable held at runtime, without a redeploy or print statements.** `load_datadog_skill("datadog/live-debugger")` · resource `datadog/live-debugger/overview`
- **Something broke after a deploy, flag flip, or config change.** `load_datadog_skill("datadog/change-tracking")` · resource `datadog/change-tracking/overview`
- **Which code emits a span or a log line.** `load_datadog_skill("datadog/code-search")` · resource `datadog/code/search`

## Watch

- **Alerts, monitors, or an open incident.** `load_datadog_skill("datadog/incidents-and-alerting")` · resource `datadog/alerting/incidents-and-alerting`
- **Request rates, latency, or custom metrics over time.** `load_datadog_skill("datadog/metrics")` · resource `datadog/metrics/overview`
