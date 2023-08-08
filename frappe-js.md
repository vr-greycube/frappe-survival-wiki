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

  formatter(value, row, column, data, format_cell) {
    if (column.fieldname == "reference_name" && data.reference_name) {
      let form_link = frappe.utils.get_form_link(
        data.reference_doctype,
        data.reference_name
      );
      return `<a class="text-muted grey" href="${form_link}">${value}</a>`;
    }
    return format_cell(value, row, column, data);
  },

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

```
color = frappe.ui.color.get('blue', 'extra-light');
```

#### #### global events

```
$(document).on("startup", function () {
  // custom logic
  frappe.set_route("#custom_page");
});

```

```
$(document).on("startup", function () {
// custom logic
frappe.set_route("#custom_page");
});

```

#### child table

```
# add row event
frappe.ui.form.on(
  "table_field_fieldname",
  "table_field_fieldname_add",
  function (frm) {
      // let items =  frm.doc.table_field_fieldname;
});

# set df properties
frm.fields_dict.items.grid.grid_rows[0].toggle_editable("rate", false);
frm.fields_dict.items.grid.update_docfield_property("rate", "read_only", 0);
frm.fields_dict.items.grid.toggle_reqd("item_code", frm.doc.update_stock? true: false);
frm.fields_dict.items.grid.update_docfield_property("remarks","hidden",1)

#  make rate read_only for returns..(note: works only if done in grid-row-render )

frappe.ui.form.on("Sales Invoice", {
  setup: function (frm) {
    $(frm.wrapper).on("grid-row-render", function (e, grid_row) {
      if (frm.doc.is_return && grid_row.grid.df.fieldname == "items") {
        grid_row.activate();
        grid_row.toggle_editable("rate", false);
      }
    });
  },
});


```



#### add filter in list view