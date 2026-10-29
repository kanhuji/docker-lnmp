Example `ftp/users.env`:
```
USERS=alice|alice-password|dir bob|bob-password|dir
```

Set the required variables in `.env`:
```
FTP_USERS_FILE=./ftp/users.env
FTP_DATA_PATH=./data/ftp
FTP_ADDRESS=127.0.0.1
FTP_IP=172.19.0.5
FTP_HOST_PORT=21
FTP_PASSIVE_PORT_MIN=21000
FTP_PASSIVE_PORT_MAX=21010
```

The URL for remote acess of files on FTP server
```
ftp://username:password@ftp:21/dir
```