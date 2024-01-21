---
title: "Pod Resource Utilization"
date: 2024-01-21T14:03:56+05:30
---


### Getting pod memory

First get into the pod using `kubectl exec`

```bash
cat /sys/fs/cgroup/memory/memory.usage_in_bytes | awk '{ foo = $1 / 1024 / 1024 ; print foo "MB" }'
```