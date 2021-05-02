1. ###### core-default.xml 主要配置



- 基础配置

| 配置项名称                      | 配置项默认值             | 解释 |
| ------------------------------- | ------------------------ | ---- |
| hadoop.tmp.dir                  | /tmp/hadoop-${user.name} |      |
| hadoop.http.filter.initializers |                          |      |
|                                 |                          |      |
|                                 |                          |      |



- 认证配置

| 配置项名称                                                   | 配置项默认值                                                 | 解释 |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ---- |
| hadoop.http.filter.initializers                              | org.apache.hadoop.http.lib.StaticUserWebFilter               |      |
| hadoop.security.authorization                                | false                                                        |      |
| hadoop.security.instrumentation.requires.admin               | false                                                        |      |
| hadoop.security.authentication                               | simple                                                       |      |
| hadoop.security.group.mapping                                | org.apache.hadoop.security.JniBasedUnixGroupsMappingWithFallback |      |
| hadoop.security.dns.log-slow-lookups.enabled                 | false                                                        |      |
| hadoop.security.dns.log-slow-lookups.threshold.ms            | 1000                                                         |      |
| hadoop.security.groups.cache.secs                            | 300                                                          |      |
| hadoop.security.groups.negative-cache.secs                   | 30                                                           |      |
| hadoop.security.groups.cache.warn.after.ms                   | 5000                                                         |      |
| hadoop.security.groups.cache.background.reload               | false                                                        |      |
| hadoop.security.groups.cache.background.reload.threads       | 3                                                            |      |
| hadoop.security.groups.shell.command.timeout                 | 0s                                                           |      |
| hadoop.security.credential.clear-text-fallback               | true                                                         |      |
| hadoop.security.credential.provider.path                     |                                                              |      |
| hadoop.security.credstore.java-keystore-provider.password-file |                                                              |      |
| hadoop.security.group.mapping.providers                      |                                                              |      |
| hadoop.security.group.mapping.providers.combined             |                                                              |      |
| hadoop.security.service.user.name.key                        |                                                              |      |
| hadoop.security.uid.cache.secs                               | 14400                                                        |      |
| hadoop.service.shutdown.timeout                              | 30s                                                          |      |
| hadoop.rpc.protection                                        | authentication                                               |      |
| hadoop.security.saslproperties.resolver.class                |                                                              |      |
| hadoop.security.sensitive-config-keys                        | secret$ password$ ssl.keystore.pass$ fs.s3a.server-side-encryption.key fs.s3a.*.server-side-encryption.key fs.s3a.secret.key fs.s3a.*.secret.key fs.s3a.session.key fs.s3a.*.session.key fs.s3a.session.token fs.s3a.*.session.token fs.azure.account.key.* fs.azure.oauth2.* fs.adl.oauth2.* credential$ oauth.*secret oauth.*password oauth.*token hadoop.security.sensitive-config-keys |      |
| hadoop.security.auth_to_local                                |                                                              |      |
| hadoop.security.auth_to_local.mechanism                      | hadoop                                                       |      |



- io 相关配置

| io.file.buffer.size                      | 4096                                                         |      |
| ---------------------------------------- | ------------------------------------------------------------ | ---- |
| io.bytes.per.checksum                    | 512                                                          |      |
| io.skip.checksum.errors                  | false                                                        |      |
| io.skip.checksum.errors                  | false                                                        |      |
| io.compression.codecs                    |                                                              |      |
| io.compression.codec.bzip2.library       | system-native                                                |      |
| io.serializations                        | org.apache.hadoop.io.serializer.WritableSerialization, org.apache.hadoop.io.serializer.avro.AvroSpecificSerialization, org.apache.hadoop.io.serializer.avro.AvroReflectSerialization |      |
| io.seqfile.local.dir                     | ${hadoop.tmp.dir}/io/local                                   |      |
| io.map.index.skip                        | 0                                                            |      |
| io.map.index.interval                    | 128                                                          |      |
| io.erasurecode.codec.rs.rawcoders        | rs_native,rs_java                                            |      |
| io.erasurecode.codec.rs-legacy.rawcoders | rs-legacy_java                                               |      |
| io.erasurecode.codec.xor.rawcoders       | xor_native,xor_java                                          |      |



- fs 相关配置

| fs.defaultFS                        | file:///                               |                                                              |
| ----------------------------------- | -------------------------------------- | ------------------------------------------------------------ |
| fs.default.name                     | file:///                               |                                                              |
| fs.trash.interval                   | 0                                      |                                                              |
| fs.trash.checkpoint.interval        | 0                                      |                                                              |
| fs.protected.directories            |                                        |                                                              |
| fs.AbstractFileSystem.file.impl     | org.apache.hadoop.fs.local.LocalFs     |                                                              |
| fs.AbstractFileSystem.har.impl      | org.apache.hadoop.fs.HarFs             |                                                              |
| fs.AbstractFileSystem.hdfs.impl     | org.apache.hadoop.fs.Hdfs              |                                                              |
| fs.AbstractFileSystem.viewfs.impl   | org.apache.hadoop.fs.viewfs.ViewFs     |                                                              |
| fs.viewfs.rename.strategy           | SAME_MOUNTPOINT                        | SAME_MOUNTPOINT,SAME_TARGET_URI_ACROSS_MOUNTPOINT and SAME_FILESYSTEM_ACROSS_MOUNTPOINT. |
| fs.AbstractFileSystem.ftp.impl      | org.apache.hadoop.fs.ftp.FtpFs         |                                                              |
| fs.ftp.impl                         | org.apache.hadoop.fs.ftp.FTPFileSystem |                                                              |
| fs.AbstractFileSystem.webhdfs.impl  | org.apache.hadoop.fs.WebHdfs           |                                                              |
| fs.AbstractFileSystem.swebhdfs.impl | org.apache.hadoop.fs.SWebHdfs          |                                                              |
| fs.ftp.host                         | 0.0.0.0                                |                                                              |
| fs.ftp.host.port                    | 21                                     |                                                              |
| fs.ftp.data.connection.mode         | ACTIVE_LOCAL_DATA_CONNECTION_MODE      | ACTIVE_LOCAL_DATA_CONNECTION_MODE, PASSIVE_LOCAL_DATA_CONNECTION_MODE PASSIVE_REMOTE_DATA_CONNECTION_MODE. |
| fs.ftp.transfer.mode                | BLOCK_TRANSFER_MODE                    |                                                              |
| fs.df.interval                      | 60000                                  |                                                              |
| fs.du.interval                      | 600000                                 |                                                              |
| fs.automatic.close                  | true                                   |                                                              |
| io.seqfile.compress.blocksize       | 1000000                                |                                                              |
| io.mapfile.bloom.size               | 1048576                                |                                                              |
| io.mapfile.bloom.error.rate         | 0.005                                  |                                                              |

