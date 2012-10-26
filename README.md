How to Build the SSO Extension for eXo Platform
============

Before you start to build, visit our intro on [wiki](https://github.com/exo-addons/sso-extension/wiki).


To build, make sure you have the following properly installed  on your system :   
* Recent Git client
* Java Development Kit 1.6
* Recent Maven 3


Clone this project and build it with maven : 

```
    git clone git@github.com:exo-addons/sso-extension.git
    cd sso-extension
    mvn clean install
```

The following artefacts are produced :

* ```sso-extension.war``` : configuration files of the integration with eXo platform on tomcat.
* ```sso-extension.component.sso-extensible-filter-0.1-SNAPSHOT.jar```: the filters to intercept http requests.
* ```sso-extension.config-0.1-SNAPSHOT.jar```: the activation jar of our extension.
* ```sso-extension.ear```: configuration files of the integration with eXo platform on JBoss AS.


Troubleshooting
----

If the build complains about missing artifacts, please let us know [via our forums](http://forum.exoplatform.org).

As a quick workaround, you may try setting up eXo maven repositories as follows.

Edit your```$HOME/.m2/settings.xml```  (```%HOMEPATH%\.m2\settings.xml``` on Windows) as follows :

 ```xml
                             <settings>
                               <profiles>
                                 <profile>
                                   <id>exo-public-repository</id>
                                   <repositories>
                                     <repository>
                                       <id>exo-public-repository-group</id>
                                       <name>eXo Public Maven Repository Group</name>
                                       <url>http://repository.exoplatform.org/content/groups/public</url>
                                       <layout>default</layout>
                                       <releases>
                                         <enabled>true</enabled>
                                         <updatePolicy>never</updatePolicy>
                                       </releases>
                                       <snapshots>
                                         <enabled>true</enabled>
                                         <updatePolicy>never</updatePolicy>
                                       </snapshots>
                                     </repository>
                                   </repositories>
                                   <pluginRepositories>
                                     <pluginRepository>
                                       <id>exo-public-repository-group</id>
                                       <name>eXo Public Maven Repository Group</name>
                                       <url>http://repository.exoplatform.org/content/groups/public</url>
                                       <layout>default</layout>
                                       <releases>
                                         <enabled>true</enabled>
                                         <updatePolicy>never</updatePolicy>
                                       </releases>
                                       <snapshots>
                                         <enabled>true</enabled>
                                         <updatePolicy>never</updatePolicy>
                                       </snapshots>
                                     </pluginRepository>
                                   </pluginRepositories>
                                 </profile>
                               </profiles>
                               <activeProfiles>
                                 <activeProfile>exo-public-repository</activeProfile>
                               </activeProfiles>
                             </settings>
```

> Checkout [install instructions](https://github.com/exo-addons/sso-extension/wiki/) for your SSO server.