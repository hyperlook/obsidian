---
category: 技术
draft: true
---
> [!important] 目录
> 
> - [[#]]
> - [[#how to install filebrowser as a service]]

### how to install filebrowser as a service

To install Filebrowser as a service on Alpine Linux, follow these steps:

1. Create a new user and group for Filebrowser:

```Plain
addgroup -S filebrowser
adduser -S -D -H -G filebrowser filebrowser
```

1. Create a configuration directory and set proper permissions:

```Plain
mkdir -p /etc/filebrowser
chown filebrowser:filebrowser /etc/filebrowser
```

1. Create a new service file `/etc/init.d/filebrowser` with the following content:

3. Add the following content to the `/etc/init.d/filebrowser` file:

```Plain
#!/sbin/openrc-run
supervisorctl -c /etc/supervisor/supervisord.conf start filebrowser
```

1. Save and close the file.
2. Set the correct permissions for the service file:

```Plain
chmod +x /etc/init.d/filebrowser
```

1. Start the Filebrowser service:

```Plain
rc-service filebrowser start
```

1. Verify that the service is running:

```Plain
rc-service filebrowser status
```