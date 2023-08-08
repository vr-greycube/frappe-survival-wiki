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

$(document).on('startup', function() {

$(document).on('app_ready', function() {

$(document).on('ready toolbar_setup', () =>

frappe.route_hooks.after_load = (frm) => {
 //
}

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

#### add standard filter in list view

```
# create a listview_settings file along with the doctype files.. doctype/some_custom_doctype/some_custom_doctype_list.js

frappe.listview_settings["Some Custom Doctype"] = {
  onload: function (listview) {
    listview.page.add_field({
      fieldtype: "Select",
      label: __("DocStatus"),
      fieldname: "docstatus",
      options: [
        { label: "Draft", value: 0 },
        { label: "Submitted", value: 1 },
        { label: "Cancelled", value: 2 },
      ],
      onchange: function () {
        listview.refresh();
      },
    });
  },
};
```

#### filter child table link field, based on row data
```
# e.g. journal entry > accounts > party_type based on row.account
frappe.ui.form.on("Sales Invoice", {
  setup: function (frm) {
    frm.set_query("uom", "items", function (doc, cdt, cdn) {
      let row = locals[cdt][cdn];
      return {
        query:
          "erpnext.accounts.doctype.pricing_rule.pricing_rule.get_item_uoms",
        filters: {
          value: row.item_code,
          apply_on: "Item Code",
        },
      };
    });
  },
});
# Filter child table item_code field
frappe.ui.form.on("Delivery Note", "refresh", function(frm) {
    cur_frm.fields_dict['items'].grid.get_field('item_code').get_query = function(doc, cdt, cdn) {
    	return {
    		filters:[
    			['Item', 'item_name', '=', 'test 1'],
    		]
    	}
    }
});

```