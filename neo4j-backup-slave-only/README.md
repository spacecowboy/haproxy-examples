## Start cluster

```
docker-compose up
```

## Run backup

HA-Proxy will delegate to a slave

```
NEO4J_DEBUG=1 neo4j-admin backup --backup-dir=./backups --name=test
```

## Important

You must configure haproxy to use `mode tcp` for backup because it's not HTTP. If you don't, the debug output will show a stack trace similar to this:

```
org.neo4j.commandline.admin.CommandFailed: Backup failed: Channel has been closed
	at org.neo4j.backup.OnlineBackupCommand.execute(OnlineBackupCommand.java:234)
	at org.neo4j.commandline.admin.AdminTool.execute(AdminTool.java:110)
	at org.neo4j.commandline.admin.AdminTool.main(AdminTool.java:48)
Caused by: org.neo4j.com.ComException: Channel has been closed
	at org.neo4j.com.DechunkingChannelBuffer.readNext(DechunkingChannelBuffer.java:69)
	at org.neo4j.com.DechunkingChannelBuffer.readNextChunk(DechunkingChannelBuffer.java:89)
	at org.neo4j.com.DechunkingChannelBuffer.<init>(DechunkingChannelBuffer.java:59)
	at org.neo4j.com.Protocol.deserializeResponse(Protocol.java:196)
	at org.neo4j.com.Client.sendRequest(Client.java:306)
	at org.neo4j.com.Client.sendRequest(Client.java:288)
	at org.neo4j.backup.BackupClient.fullBackup(BackupClient.java:68)
	at org.neo4j.backup.BackupService$FullBackupStoreCopyRequester.copyStore(BackupService.java:480)
	at org.neo4j.com.storecopy.StoreCopyClient.copyStore(StoreCopyClient.java:191)
	at org.neo4j.backup.BackupService.fullBackup(BackupService.java:174)
	at org.neo4j.backup.BackupService.doFullBackup(BackupService.java:148)
	at org.neo4j.backup.OnlineBackupCommand.execute(OnlineBackupCommand.java:229)
	... 2 more
command failed: Backup failed: Channel has been closed
```

This indicates either that no slave is available, or that you misconfigured haproxy to attempt to use `mode http` for backup, which won't work.
