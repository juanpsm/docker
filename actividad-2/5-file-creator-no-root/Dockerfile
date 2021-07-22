FROM alpine:latest
ARG USER=default
ENV HOME /home/$USER
RUN adduser -D $USER
WORKDIR $HOME/tmp
RUN chown -R $USER:$USER $HOME
USER $USER
RUN whoami && ls -l $HOME
ENTRYPOINT ["/bin/dd", "if=/dev/zero", "count=1", "of=created-file"]
CMD ["bs=10M"]
