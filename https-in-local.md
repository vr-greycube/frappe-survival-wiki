# Https in Local

https://web.dev/how-to-use-local-https/
https://github.com/jsha/minica

- create self signed certificate
- change in frappe app.py

```
    ssl_context=""
    ssh_path = "/Users/vijay/.ssh/"
    ssl_context = load_ssl_context(ssh_path + "ssh.local.pem", ssh_path+"ssh.local-key.pem")

    run_simple(
        "0.0.0.0",
        int(port),
        application,
        exclude_patterns=["test_*"],
        use_reloader=False if in_test_env else not no_reload,
        use_debugger=not in_test_env,
        use_evalex=not in_test_env,
        threaded=not no_threading,
        ssl_context=ssl_context
    )

```
