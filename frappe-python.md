# Frappe Python

#### dedupe child table
```
deduped = []
for k, g in itertools.groupby(doc.timeline_links, key=lambda x: (x.link_doctype,x.link_name)):
    deduped.append(next(g))
doc.timeline_links = deduped
```