# operation-systems-work
SSH key-only connection
ssh-keygen - Generate keys for both server and client.

ssh-copy-id server@192.168.32.129 - Copy key files from client to server, knowing server's password (example).

ssh server@192.168.32.129 - Connect with keys only (example).

Creating daemon which runs shell script using timer
To run script each 10 second, for example, we need to create service and timer for it.

/etc/systemd/system/logger-exam.service:

[Unit]
Description=Logger
Wants=logger-exam.timer

[Service]
Type=oneshot
ExecStart=/bin/bash /home/server/exam/shell-script.sh

[Install]
WantedBy=multi-user.target
/etc/systemd/system/logger-exam.timer:

[Unit]
Requires=logger-exam.service

[Timer]
Unit=logger-exam.service
OnCalendar=*-*-* *:*:0/10

[Install]
WantedBy=timers.target
shell-script.sh:

#!/bin/bash

echo "Here I am" >> /home/server/logs
To enable executing system daemon, we need to execute: systemctl start logger-exam.timer (in this example).

Modification in Java app to enable healthcheck endpoint
We need to add separate controller class to represent our /healthcheck endpoint:

package com.pazyniuk.controller;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
public class HealthController {
  
  @RequestMapping(value = "/healthcheck", method = RequestMethod.GET)
  public ResponseEntity returnHealthCheckResponse() {
      return new ResponseEntity<>("Everything is OK", HttpStatus.OK);
  }
}
Reference here.

Include healthcheck option to docker-compose
We need to add healthcheck position to our docker-compose.yml file:

healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/healthcheck"]
      interval: 30s
      timeout: 15s
      retries: 5
      start_period: 60s
Later, we are able to run docker inspect <container-id> to check health of our container.

Docker container
To run Java application and MySQL service in one container I used Ubuntu 18.04 image and modified my Dockerfile.

Also, you can find my wrapper script and docker-compose files here.

To run a container: docker-compose build --no-cache and docker-compose up.

To connect to shell of the container: docker exec -it <container-id> /bin/bash.

To copy file from container to host: docker cp <containerId>:/file/path/within/container /host/path/target.

Helpful articles
Systemd timer usage

Systemd timer time spans

Copying container files thread

Connecting to container via shell

Docker healthcheck article (RU)

SSH key-only connection (RU)

Dockerfile healthcheck docs

Docker-compose docs
