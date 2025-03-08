---
date: 2025-02-07
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Queuing Up Jobs

After [[2025-02-07_Redis-Setup|redis server]] and [[2025-02-07_Adding-in-the-Worker|the worker]] are setup, we can now queue up jobs to be processed by the worker.

File: /home/matt/Projects/pdf/app/web/views/pdf_views.py
```python
# ...

@bp.route("/", methods=["POST"])
@login_required
@handle_file_upload
def upload_file(file_id, file_path, file_name):
    res, status_code = files.upload(file_path)
    if status_code >= 400:
        return res, status_code

    # Create a new Pdf record in the database
    pdf = Pdf.create(id=file_id, name=file_name, user_id=g.user.id)

    # Generate embeddings for that Pdf and store them in vector storage
    process_document.delay(pdf.id)
    #               ^^^^^^ means not execute immediately but
    #                      creating job and send to works

    return pdf.as_dict()

# ...

```

File: /home/matt/Projects/pdf/app/web/tasks/embeddings.py
```python
from celery import shared_task # import decorator @shared_task from `celery`

# ...

@shared_task() # <-- means this function can be handled by workers
def process_document(pdf_id: int):
    pdf = Pdf.find_by(id=pdf_id)
    with download(pdf.id) as pdf_path:
        create_embeddings_for_pdf(pdf.id, pdf_path)
       #^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
       #this is what we work about vector storage
```

Let's try to upload a PDF file and see if the worker processes it.

```sh
$ inv devworker

 -------------- celery@arch v5.3.1 (emerald-rush)
--- ***** -----
-- ******* ---- Linux-6.12.10-arch1-1-x86_64-with-glibc2.40 2025-02-07 05:23:12
- *** --- * ---
- ** ---------- [config]
- ** ---------- .> app:         app.web:0x78712bae8cd0
- ** ---------- .> transport:   redis://localhost:6379//
- ** ---------- .> results:     disabled://
- *** --- * --- .> concurrency: 1 (solo)
-- ******* ---- .> task events: OFF (enable -E to monitor tasks in this worker)
--- ***** -----
 -------------- [queues]
                .> celery           exchange=celery(direct) key=celery


[tasks]
  . app.web.tasks.embeddings.process_document

[2025-02-07 05:23:12,638: INFO/MainProcess] Connected to redis://localhost:6379//
[2025-02-07 05:23:12,639: INFO/MainProcess] mingle: searching for neighbors
[2025-02-07 05:23:13,645: INFO/MainProcess] mingle: all alone
[2025-02-07 05:23:13,660: INFO/MainProcess] celery@arch ready.
# upload a PDF file, then check the worker logs, we can see the following messages
[2025-02-07 05:23:24,124: INFO/MainProcess] Task app.web.tasks.embeddings.process_document[6676710d-f076-4cc5-afe3-313e855e9316] received
[2025-02-07 05:23:27,537: INFO/MainProcess] HTTP Request: POST https://api.openai.com/v1/embeddings "HTTP/1.1 200 OK"
[2025-02-07 05:23:39,108: INFO/MainProcess] Task app.web.tasks.embeddings.process_document[6676710d-f076-4cc5-afe3-313e855e9316] succeeded in 14.983726259999912s: None
```

Therefore, using `celery`, we can use `@shared_task` to mark a function as a task that can be executed by a worker, and then when calling this function, use `.delay()` to hand over the execution of this function to the worker to process. Very simple.







