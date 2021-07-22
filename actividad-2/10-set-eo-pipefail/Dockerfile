FROM busybox
COPY script.sh /tmp/
RUN chmod +x /tmp/script.sh 
ENTRYPOINT ["/bin/sh", "-c", "./tmp/script.sh"]
