# Frappe Dev Ops

```

# install app from requirements
$ frappe-14/env/bin/python -m pip install --quiet --upgrade -e frappe-14/apps/erpnext


```

#### email configuration in site_config.json


```
# can be used to quickly setup a default email account. Disable the exisiting default sending email account if any (ususally Notifications).


 "mail_server": "smtp.gmail.com",
  "mail_port": 587,
  "use_tls": 1,
  "mail_login": "<user>@gmail.com",
  "mail_password": "app password",
  "auto_email_id": "<user>@gmail.com",
  "email_sender_name": "Notifications",
  "always_use_account_email_id_as_sender": 0,
  "always_use_account_name_as_sender_name": 0
  ```