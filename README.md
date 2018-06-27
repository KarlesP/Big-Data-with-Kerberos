# Hadoop vulnerability detection with Kerberos

## Usage
The entrance file of the program is main.py

You can use the following command to see the usage
```
$python main.py -h
usage: main.py [-h] {hadoop,spark} ...

This is a tool for detectiong the security problem of hadoop!

positional arguments:
  {hadoop,spark}  commands
    hadoop        check security of hadoop
    spark         check security of spark

optional arguments:
  -h, --help      show this help message and exit
```
There is mainly 2 functions which are Hadoop and Spark vuln check

## Hadoop

You could still use `-h` to see the usage
```
python main.py hadoop -h
usage: main.py hadoop [-h] confFolder

positional arguments:
  confFolder  the dir of hadoop configuration files

optional arguments:
  -h, --help  show this help message and exit
```

`confFolder`is the folder for config files of Hadoop. It could be the `conf` folder when installing(like `/usr/local/hadoop/conf/hadoop/`)，you can also copy these files to the specific folder but remind that filename should be the same as in hadoop.json，for example, the following content is system default file for checking known security factor's file in JSON.

```json
{
  "authentication": {
    "core-site": [
      {
        "hadoop.security.authentication": "kerberos",
        "reason": "Suggest to authenticate user by using kerberos!"
      }
    ]
  },
  "authorization": {
    "core-site": [
      {
        "fs.permissions.enabled": "true",
        "reason": "Suggest to enable the permission control for fs!"
      },
      {
        "hadoop.security.authorization": "true",
        "reason": "Suggest to enable authorization for every user!"
      }
    ]
  },
  "acl": {
    "hdfs-site": [
      {
        "dfs.namenode.acls.enabled": "true",
        "reason": "Suggest to enable acl for user!"
      }
    ]
  },
  "encry": {
    "hdfs-site": [
      {
        "dfs.encryption.key.provider.uri": "*",
        "reason": "Suggest to encrypt data!"
      }
    ]
  }
}
```
Core-site is the checking filename ，next level is the setting，it 's father level like `encry`means chacking encrypted security questions. 

This file users could add or delet by themselves

With this setting and run the script
```
$ python main.py hadoop ./hadoop
[Info]: Begining to check security: authentication
[Info]: >> Check file: core-site.xml
[Pass]: Your hadoop.security.authentication setting is safe!
[Info]: Begining to check security: encry
[Info]: >> Check file: hdfs-site.xml
[Warning]: Suggest to encrypt data! Set: dfs.encryption.key.provider.uri=*
[Info]: Begining to check security: authorization
[Info]: >> Check file: core-site.xml
[Warning]: Suggest to enable authorization for every user! Set: hadoop.security.authorization=true
[Info]: Begining to check security: acl
[Info]: >> Check file: hdfs-site.xml
[Warning]: Suggest to enable acl for user! Set: dfs.namenode.acls.enabled=true
```

## Spark
It's much as same as Hadoop，you need point to the setting folder，but difference is that Spark's defalt setting file has only one `spark-defaults.conf`,DO NOT copy to other folder。

As the same as Hadoop checking , it provides a setting that can be configured. It's in the root directory of Spark and named `security.ini`.

Example：
```
$ python main.py spark ./spark/
[Info]: Start to check the security of spark...
[Warning]: Suggest to add option spark.authenticate = true if your spark runs on standalone mode
[Warning]: Suggest to add option spark.authenticate.secret if your spark runs on yarn mode
[Warning]: Suggest to set option spark.ssl.enable = true
[Warning]: Suggest to set option spark.eventlog.enabled = true

```
