FROM alpine:latest
WORKDIR /tmp
ENTRYPOINT ["/bin/dd", "if=/dev/zero", "count=1", "of=created-file"]
CMD ["bs=10M"]