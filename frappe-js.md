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

#### rest api from web form
```
 function filterItemGroupAJAX() {
    $.ajax({
        url: "/api/resource/Item Group?filters=[[\"item_group_type\", \"=\", \"Super Category\"]]",
      type: "GET",
      success: function(result) {
        var options = []
        for (var i = 0; i < result.data.length; i++) {
          options.push({
            'label': result.data[i].name,
            'value': result.data[i].name
          })
  
        }
      },
      error: function(xhr, status, error) {
    console.log("AJAX request failed:", status, error);
      }
    });
  };
  ```