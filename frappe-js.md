# Frappe Js

#### report grid formatter

```
frappe.query_reports["Lead Followup"] = {
    filters: [],
    formatter: function (value, row, column, data, default_formatter) {
        value = default_formatter(value, row, column, data);
        if (column.id == "lead") {
            console.log(data["lead_category_cf"]);
            value = `<a style='color:${
                lead_category[data["lead_category_cf"]]
            }' href="/app/lead/${data["lead"]}" data-doctype="Lead">${
                data["lead"]
            }</a>`;
        }
        return value;
    },
};
```