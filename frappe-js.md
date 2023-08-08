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

#### global events

```
$(document).on("startup", function () {
  // custom logic
  frappe.set_route("#custom_page");
});

$(document).on('startup', function() { })

$(document).on('app_ready', function() {})

$(document).on('ready toolbar_setup', () =>{})

frappe.route_hooks.after_load = (frm) => { ;}

# web form
frappe.web_form.events.on("after_load", function () {})

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


#### fetch api

```
 fetch_data(args) {
    let me = this;
    return fetch(
      "/api/method/my_app.get_report",
      {
        method: "POST",
        headers: {
          "X-Frappe-CSRF-Token": frappe.csrf_token,
          "Content-Type": "application/json;charset=utf-8",
        },
        body: JSON.stringify(args),
      }
    )
      .then((response) => {
        if (!response.ok) {
          throw new Error("Network response was not OK");
        }
        return response.json();
      })
      .then((r) => {
        if (r.message && r.message.total_segments) {
          // console.log(r.message)
          frappe.show_alert(`Fetching ${r.message.record_count} records`, 20);
          // this method is used to load several requests in parallel.
          me.segment_count = 0;
          me.total_segments = r.message.total_segments;
          me.record_count = r.message.record_count;

          let promises = [];
          for (let idx = 1; idx <= r.message.total_segments; idx++) {
            let _args = Object.assign({ segment: idx, }, args)
            promises.push(me.papa_fetch(_args));
          }
          Promise.all(promises).then(() => {
            // 
          })
        }
      })
      .catch((error) => {
        console.error(
          "There has been a problem with your fetch operation:",
          error
        );
      });
  }
  ```



  #### Hack to add links in document Connections/Dashboard
```
	refresh: function (frm) {
		$('div').find('.document-link[data-doctype="Purchase Order"]').remove();

		let link = $(`
		<div class="document-link" data-doctype="Purchase Order">
			<div class="document-link-badge" data-doctype="Purchase Order"> <span class="count">1</span> <a
				class="badge-link">Purchase Order</a> </div> <span class="open-notification hidden"
			title="Open Purchase Order"> </span> <button class="btn btn-new btn-secondary btn-xs icon-btn"
			data-doctype="Purchase Order"> <svg class="icon icon-sm">
				<use href="#icon-add"></use>
			</svg> </button>
		</div>
		`);

		link.on('click', function () {
			// frm.dashboard.open_document_list($(this).closest('.document-link'));
			frappe.route_options = { 'name': ['in', 'PUR-ORD-2022-00042'] };
			frappe.set_route("List", "Purchase Order", "List");

		})

		$('div').find('.document-link[data-doctype="Supplier Packing List Art"]').after(link);
	}

  ```