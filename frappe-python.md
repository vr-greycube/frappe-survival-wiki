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

#### frappe logger

```
frappe.logger().debug("some debug message")
```

#### chunkerize

```
def chunks(lst, n):
    """Yield successive n-sized chunks from lst."""
    for i in range(0, len(lst), n):
        yield lst[i:i + n]

```


def add_images(data, workbook, worksheet=""):
    ws = workbook.get_sheet_by_name(worksheet)
    image_col = "S"  # get_column_letter(len(data[0]) - 2)
    for row, image_url in enumerate(data):
        if image_url:
            _filename, extension = os.path.splitext(image_url)
            if extension in [".png", ".jpg", ".jpeg"]:
                try:
                    content = None

                    if image_url.startswith("http"):
                        content = requests.get(image_url).content
                    else:
                        item_file = frappe.get_doc("File", {"file_url": image_url})
                        content = item_file.get_content()
                    if content:
                        image = openpyxl.drawing.image.Image(io.BytesIO(content))
                        image.height = 100
                        image.width = 100
                        ws.add_image(image, f"{image_col}{cstr(row+1)}")
                        ws.row_dimensions[row + 1].height = 90
                except Exception as e:
                    print(e)
                    pass