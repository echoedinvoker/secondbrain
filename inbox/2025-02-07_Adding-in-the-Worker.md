---
date: 2025-02-07
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Adding in the Worker

After [[2025-02-07_Redis-Setup|Redis Setup]] to our application, we can now add in the worker to our application.

The worker is seperate program apart from the main application, so we need to launch it separately.

Before we can launch the worker, make sure the redis server is running.

To start the worker, run the following command:

```sh
$ inv devworker

 -------------- celery@arch v5.3.1 (emerald-rush)
--- ***** -----
-- ******* ---- Linux-6.12.10-arch1-1-x86_64-with-glibc2.40 2025-02-07 03:53:42
- *** --- * ---
- ** ---------- [config]
- ** ---------- .> app:         app.web:0x704598342d50
- ** ---------- .> transport:   redis://localhost:6379// # means using redis
- ** ---------- .> results:     disabled://
- *** --- * --- .> concurrency: 1 (solo)
-- ******* ---- .> task events: OFF (enable -E to monitor tasks in this worker)
--- ***** -----
 -------------- [queues]
                .> celery           exchange=celery(direct) key=celery


[tasks]
  . app.web.tasks.embeddings.process_document

[2025-02-07 03:53:42,265: INFO/MainProcess] Connected to redis://localhost:6379//
[2025-02-07 03:53:42,267: INFO/MainProcess] mingle: searching for neighbors
[2025-02-07 03:53:43,271: INFO/MainProcess] mingle: all alone
[2025-02-07 03:53:43,293: INFO/MainProcess] celery@arch ready.

```

Now the worker is running and ready to process the tasks.
