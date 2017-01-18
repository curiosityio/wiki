---
name: Request
---

# Get IP address where request came from

* Because Node can be behind a proxy, below works well:

```
request.headers['x-forwarded-for']
```

Node tags this onto the header before it gets to you. 
