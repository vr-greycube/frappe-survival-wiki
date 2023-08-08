# Frappe Python

#### dedupe child table

```
deduped = []
for k, g in itertools.groupby(doc.timeline_links, key=lambda x: (x.link_doctype,x.link_name)):
    deduped.append(next(g))
doc.timeline_links = deduped


```

````
conversion_rate = frappe.db.get_value("Currency Exchange",
                                      filters={'from_currency':"EUR", 'to_currency' : "USD"},
                                      fieldname=['exchange_rate'],
                                      order_by="creation desc")
                                      ```
````

#### make excel file
```
# array of arrays
data_rows=[["sample","data"]]

from frappe.utils.xlsxutils import make_xlsx
xlsx_file = make_xlsx(data_rows, "Sample Data")
file_data = xlsx_file.getvalue()

_file = frappe.get_doc({
"doctype": "File",
"file_name": "Sample Data.xlsx,
"folder": "Home/Attachments",
"content": file_data})
_file.save()
```

#### 
```
frappe.logger().debug("some debug message")
```