# Using **supervisord** in **Docker**
[Source](http://simonjbeaumont.com/posts/docker-dashboard)

[Supervisord](http://supervisord.org/) is a UNIX process manager that is meant to start like any other program. It can be used to manage multiple processes in a single Docker container.

```
# Dockerfile
# install programs to manage, such as influxdb, grafana, nginx and crond
# install supervisord
RUN apt-get -qy supervisor

# add configuration for programs to manage
# add configuration
ADD ./supervisord.conf /etc/supervisor.d/supervisord.conf

# run
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor.d/supervisord.conf"]
```

Supervisor configuration file is split into sections for each program to manage.
``` ini
[supervisord]
nodaemon = true

[program:influxdb]
priority = 10
command = /opt/influxdb/influxd -pidfile=/var/run/influxdb/influxd.pid -config=/etc/opt/influxdb/influxdb.conf
autorestart = true
stdout_logfile = /var/log/supervisor/%(program_name)s.log
stderr_logfile = /var/log/supervisor/%(program_name)s.log

[program:grafana]
priority = 20
command = /usr/sbin/grafana-server --homepath=/usr/share/grafana --config=/etc/grafana/grafana.ini cfg:default.paths.data=/var/lib/grafana cfg:default.paths.logs=/var/log/grafana
autorestart = true
stdout_logfile = /var/log/supervisor/%(program_name)s.log
stderr_logfile = /var/log/supervisor/%(program_name)s.log

[program:nginx]
command = /usr/sbin/nginx
autorestart = true
stdout_logfile = /var/log/supervisor/%(program_name)s.log
stderr_logfile = /var/log/supervisor/%(program_name)s.log

[program:crond]
command = /usr/sbin/crond -n -p -m off
autorestart = true
stdout_logfile = /var/log/supervisor/%(program_name)s.log
stderr_logfile = /var/log/supervisor/%(program_name)s.log
```
