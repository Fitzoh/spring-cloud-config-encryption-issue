This repo demonstrates an issue in spring cloud config that can occur when disabling the automatic decryption feature.
According to the documentation, setting `spring.cloud.config.server.encrypt.enabled=false` should prevent spring cloud config from decrypting secrets, and instead pass them to the client.
However, if that property is set to `false` and a value in the remote application.properties is encrypted, the application will fail on startup.

The workaround I have found is to set `encrypt.fail-on-error=false` in bootstrap.properties.

```
java.lang.IllegalStateException: Cannot decrypt: key=encrypted
   	at org.springframework.cloud.bootstrap.encrypt.EnvironmentDecryptApplicationInitializer.decrypt(EnvironmentDecryptApplicationInitializer.java:201) ~[spring-cloud-context-1.1.6.RELEASE.jar:1.1.6.RELEASE]
   	at org.springframework.cloud.bootstrap.encrypt.EnvironmentDecryptApplicationInitializer.decrypt(EnvironmentDecryptApplicationInitializer.java:165) ~[spring-cloud-context-1.1.6.RELEASE.jar:1.1.6.RELEASE]
   	at org.springframework.cloud.bootstrap.encrypt.EnvironmentDecryptApplicationInitializer.initialize(EnvironmentDecryptApplicationInitializer.java:95) ~[spring-cloud-context-1.1.6.RELEASE.jar:1.1.6.RELEASE]
   	at org.springframework.cloud.bootstrap.BootstrapApplicationListener$DelegatingEnvironmentDecryptApplicationInitializer.initialize(BootstrapApplicationListener.java:333) ~[spring-cloud-context-1.1.6.RELEASE.jar:1.1.6.RELEASE]
   	at org.springframework.boot.SpringApplication.applyInitializers(SpringApplication.java:635) [spring-boot-1.4.2.RELEASE.jar:1.4.2.RELEASE]
   	at org.springframework.boot.SpringApplication.prepareContext(SpringApplication.java:349) [spring-boot-1.4.2.RELEASE.jar:1.4.2.RELEASE]
   	at org.springframework.boot.SpringApplication.run(SpringApplication.java:313) [spring-boot-1.4.2.RELEASE.jar:1.4.2.RELEASE]
   	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1186) [spring-boot-1.4.2.RELEASE.jar:1.4.2.RELEASE]
   	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1175) [spring-boot-1.4.2.RELEASE.jar:1.4.2.RELEASE]
   	at com.example.SpringCloudConfigEncryptionIssueApplication.main(SpringCloudConfigEncryptionIssueApplication.java:12) [classes/:na]
   	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_51]
   	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_51]
   	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_51]
   	at java.lang.reflect.Method.invoke(Method.java:497) ~[na:1.8.0_51]
   	at com.intellij.rt.execution.application.AppMain.main(AppMain.java:147) [idea_rt.jar:na]
   Caused by: java.lang.UnsupportedOperationException: No decryption for FailsafeTextEncryptor. Did you configure the keystore correctly?
   	at org.springframework.cloud.bootstrap.encrypt.EncryptionBootstrapConfiguration$FailsafeTextEncryptor.decrypt(EncryptionBootstrapConfiguration.java:152) ~[spring-cloud-context-1.1.6.RELEASE.jar:1.1.6.RELEASE]
   	at org.springframework.cloud.bootstrap.encrypt.EnvironmentDecryptApplicationInitializer.decrypt(EnvironmentDecryptApplicationInitializer.java:193) ~[spring-cloud-context-1.1.6.RELEASE.jar:1.1.6.RELEASE]
   	... 14 common frames omitted
```

For more details see https://github.com/spring-cloud/spring-cloud-config/issues/597