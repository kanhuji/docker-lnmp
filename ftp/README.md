Note that user `root` cannot log in FTP directly. Other users may not have write access to the designated folder if two of them are assigned with the same folder in `users.env`.

Example `ftp/users.env`:
```
USERS=alice|alice-password|dir bob|bob-password|dir
```

Set the required variables in `.env`:
```
FTP_USERS_FILE=./ftp/users.env
FTP_DATA_PATH=./data/ftp
FTP_ADDRESS=127.0.0.1
FTP_IP=172.28.0.5
FTP_HOST_PORT=21
FTP_PASSIVE_PORT_MIN=21000
FTP_PASSIVE_PORT_MAX=21010
```

The URL for remote acess of files on FTP server
```shell
ftp://username:password@ftp:21/dir
```

Set `client_max_body_size` to 0 in Nginx config for development, otherwise uploading large files may fail with 413 error.

Set params in `php.ini` to remove the size and memory limits
```shell
upload_max_filesize = 0
post_max_size = 0
memory_limit = -1
```

Test file uploading to FTP server with username and password
```shell
curl -T ./LICENSE -u admin:123456 ftp://127.0.0.1/wwwroot/home
```

Check the files on FTP in docker container
```shell
docker exec -it ftp sh
ls /home
```