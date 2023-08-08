# Frappe Db

#### values clause


```
def results_to_values(iterable):
    return ",".join(str(d).replace(',)',')') for d in iterable)


# usage
from ec_dash.ec_dashboard.report.item_price_update_gc.item_price_update_gc import execute
columns, data = execute({"item_code":"E2012282"})

results_to_values((d['item_code'],d['supplier'].lower()) for d in data)


```
