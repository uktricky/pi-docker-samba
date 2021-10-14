# Samba file server for Raspberry Pi

Docker container that creates a SMB share.

## Dockerfile
FROM resin/rpi-raspbian:stretch

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends samba-common-bin samba

ADD run.sh /run.sh
RUN chmod u+x /run.sh

EXPOSE 445

ENTRYPOINT ["/run.sh"]
CMD ["-h"]

## Running

```
docker run -d -p <docker_host_ip>:445:445 \
  -v /mnt/data:/share/data \
  -v /mnt/backups:/share/backups \
  --name <container name> trnape/rpi-samba \
  -u "alice:abc123" \
  -u "bob:secret" \
  -u "guest:guest" \
  -s "Backup directory:/share/backups:rw:alice,bob" \
  -s "Alice (private):/share/data/alice:rw:alice" \
  -s "Bob (private):/share/data/bob:rw:bob" \
  -s "Documents (readonly):/share/data/documents:ro:guest,alice,bob"
```

This example will bind `smbd` to docker host ip address
and mount two directories on docker host to container.
Three users will be created and given various access to four shares.


## Connecting

To keep things simple, TCP port 445 is the only exposed port.

Open Finder, then press âŒ˜K. Enter `smb://<docker_host_ip>`
and press `Connect`.
Enter login and password you supplied at the run stage.
