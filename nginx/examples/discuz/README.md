Discuz X3.5 also needs write access to these:
```shell
chmod -R 777 config data uc_client uc_server/data
```

Set database server ip to `mysql`, which is translated to `172.28.0.3` defined in `.env`

Run the following command to let user `root` to be accessed from everywhere rather than only `localhost` by default.
```shell
docker exec -it mysql mysql -uroot -p -e "CREATE USER 'root'@'%' IDENTIFIED BY '5gSu8BtnEA8GJ7v3Gzh4MyjcW6n7cofv'; GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION; FLUSH PRIVILEGES;"
```

Or if the user already exists
```shell
docker exec -it mysql mysql -uroot -p -e "ALTER USER 'root'@'%' IDENTIFIED BY '5gSu8BtnEA8GJ7v3Gzh4MyjcW6n7cofv'; GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION; FLUSH PRIVILEGES;"
```

Open one session and run everything in it without entering password every time
```shell
docker exec -it mysql mysql -uroot -p
```

Check existing users and hosts
```shell
docker exec -it mysql mysql -uroot -p -e "SELECT user,host FROM mysql.user;"
```