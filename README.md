Introduction
============

This extension is created to make easier the integration of an sso server within eXo Platform.
We are going to explain the steps of integration for each SSO server: CAS, JOSSO, openAM.

System requirements
===================
   
* Recent Git client
* Java Development Kit 1.6
* Recent Maven 3
* eXo Platform server 3.5.4. {PLF_HOME}: The location of the unzipped eXo server.
* The eXo server will run on port 8080, make sure this port is not currently in use
* Prepare an sso server (cas, openAM, JOSSO). The SSO server will run on port 8888, make sure this port is not currently in use
* Download the sso-packaging from this link: https://repository.jboss.org/nexus/content/groups/public/org/gatein/sso/sso-packaging/1.1.1-GA/. {SSO_HOME}: refers to the directory where you made your download.

Build instructions
==================

1) Clone this project
-----------------------

* git clone git@github.com:exo-addons/sso-extension.git
* {PROJECT_HOME}: refers to the directory where you cloned the project.
* cd sso-extension

2) Build the project
-----------------------

mvn clean install

After a build success of the project you will have under the target folder:

* PROJECT_HOME/sso-extension/webapp/target/sso-extension.war -> Contains the configuration files of the integration with eXo platform on tomcat.
* PROJECT_HOME/sso-extension/component/sso-extensible-filter/target/exo.platform.sso-extension.component.sso-extensible-filter-0.1.jar -> Contains the filters to add.
* PROJECT_HOME/sso-extension/config/target/exo.platform.sso-extension.config-0.1.jar -> it's the activation jar of our extension.
* PROJECT_HOME/sso-extension/ear/target/sso-extension.ear -> Contains the configuration files of the integration with eXo platform on JBoss AS.

SSO integration steps:
=======================

1) CAS server
-------------
1.1) eXo Platform with Tomcat
-----------------------------

- Move the jar PROJECT_HOME/sso-extension/component/sso-extensible-filter/target/exo.platform.sso-extension.component.sso-extensible-filter-0.1.jar to PLF_HOME/lib
- Move the jar PROJECT_HOME/sso-extension/config/target/exo.platform.sso-extension.config-0.1.jar to PLF_HOME/lib.
- Move the jars under SSO_HOME/gatein-sso-1.1.1-GA/cas/gatein.ear/lib to the PLF_HOME/lib
- Move the war PROJECT_HOME/sso-extension/webapp/target/sso-extension.war to PLF_HOME/webapp.
- Edit PLF_HOME/conf/jaas.conf, uncomment on this section and comment other parts:

                        org.gatein.sso.agent.login.SSOLoginModule required;
                        org.exoplatform.services.security.j2ee.TomcatLoginModule required
                        portalContainerName=portal
                        realmName=gatein-domain;

- Edit PLF_HOME/webapps/portal.war/META-INF/context.xml and add ServletAccessValve into configuration as first sub-element of Context:

                        <Context path='/portal' docBase='portal' ... >
                            <Valve className='org.gatein.sso.agent.tomcat.ServletAccessValve' />
                             ...
                        </Context>

- Edit PLF_HOME/gatein/conf/configuration.properties and add the following properties:

                         ########################
                         # SSO-EXTENSION
                         ########################

                         #Single Sign On server address
                         sso.server.url=http://localhost:8888/cas
                         #Portal Server domain name
                         sso.platform.url=http://localhost:8080/portal
                         #SSO Server Type
                         sso.server.type=cas
                         #Cas parameter to renew the ticket or not on each authentication
                         sso.cas.renew.ticket=false

1.2) eXo Platform with JBoss AS
-------------------------------

- Move the jar PROJECT_HOME/sso-extension/component/sso-extensible-filter/target/exo.platform.sso-extension.component.sso-extensible-filter-0.1.jar to PLF_HOME/gatein.ear/lib
- Move the jar PROJECT_HOME/sso-extension/config/target/exo.platform.sso-extension.config-0.1.jar to PLF_HOME/gatein.ear/lib.
- Move the jars under SSO_HOME/gatein-sso-1.1.1-GA/cas/gatein.ear/lib to the PLF_HOME/gatein.ear/lib
- Move the ear PROJECT_HOME/sso-extension/ear/target/so-extension.ear to PLF_HOME/server/default/deploy.
- edit gatein.ear/META-INF/gatein-jboss-beans.xml and uncomment on this section:

                         <authentication>
                             <login-module code="org.gatein.sso.agent.login.SSOLoginModule" flag="required">
                               <module-option name="portalContainerName">portal</module-option>
                               <module-option name="realmName">gatein-domain</module-option>
                             </login-module>
                             <login-module code="org.exoplatform.services.security.j2ee.JbossLoginModule" flag="required">
                               <module-option name="portalContainerName">portal</module-option>
                               <module-option name="realmName">gatein-domain</module-option>
                             </login-module>
                           </authentication>

- Edit PLF_HOME/gatein/conf/configuration.properties and add the following properties:

                        ########################
                        # SSO-EXTENSION
                        ########################

                        #Single Sign On server address
                        sso.server.url=http://localhost:8888/cas
                        #Portal Server domain name
                        sso.platform.url=http://localhost:8080/portal
                        #SSO Server Type
                        sso.server.type=cas
                        #Cas parameter to renew the ticket or not on each authentication
                        sso.cas.renew.ticket=false

2) JOSSO server
---------------

1.1) eXo Platform with Tomcat
-----------------------------

- Move the jar PROJECT_HOME/sso-extension/component/sso-extensible-filter/target/exo.platform.sso-extension.component.sso-extensible-filter-0.1.jar to PLF_HOME/lib
- Move the jar PROJECT_HOME/sso-extension/config/target/exo.platform.sso-extension.config-0.1.jar to PLF_HOME/lib.
- Move the jars under SSO_HOME/gatein-sso-1.1.1-GA/josso/josso-18X/gatein.ear/lib to the PLF_HOME/lib
- Copy the file SSO_HOME/josso/josso-18X/gatein.ear/portal.war/WEB-INF/classes/josso-agent-config.xml into PLF_HOME/webapps/portal.war/WEB-INF/classes.
- Move the war PROJECT_HOME/sso-extension/webapp/target/sso-extension.war to PLF_HOME/webapp.
- Edit PLF_HOME/conf/jaas.conf, uncomment on this section and comment other parts:

                        org.gatein.sso.agent.login.SSOLoginModule required;
                        org.exoplatform.services.security.j2ee.TomcatLoginModule required
                        portalContainerName=portal
                        realmName=gatein-domain;

- Edit PLF_HOME/webapps/portal.war/META-INF/context.xml and add ServletAccessValve into configuration as first sub-element of Context:

                        <Context path='/portal' docBase='portal' ... >
                            <Valve className='org.gatein.sso.agent.tomcat.ServletAccessValve' />
                             ...
                        </Context>

- Edit PLF_HOME/gatein/conf/configuration.properties and add the following properties:

                        ########################
                        # SSO-EXTENSION
                        ########################

                        #Single Sign On server address
                        sso.server.url=http://localhost:8888/josso/signon/login.do
                        #Portal Server domain name
                        sso.platform.url=http://localhost:8080/portal
                        #SSO Server Type
                        sso.josso.server.type=josso

1.2) eXo Platform with JBoss AS
-------------------------------

- Move the jar PROJECT_HOME/sso-extension/component/sso-extensible-filter/target/exo.platform.sso-extension.component.sso-extensible-filter-0.1.jar to PLF_HOME/gatein.ear/lib
- Move the jar PROJECT_HOME/sso-extension/config/target/exo.platform.sso-extension.config-0.1.jar to PLF_HOME/gatein.ear/lib.
- Move the jars under SSO_HOME/gatein-sso-1.1.1-GA/josso/josso-18X/gatein.ear/lib to the PLF_HOME/gatein.ear/lib.
- Copy the file SSO_HOME/josso/josso-18X/gatein.ear/portal.war/WEB-INF/classes/josso-agent-config.xml into PLF_HOME/gatein.ear/02portal.war/WEB-INF/classes
- Move the ear PROJECT_HOME/sso-extension/ear/target/so-extension.ear to PLF_HOME/server/default/deploy.
- edit PLF_HOME/gatein.ear/META-INF/gatein-jboss-beans.xml and uncomment on this section:

                         <authentication>
                             <login-module code="org.gatein.sso.agent.login.SSOLoginModule" flag="required">
                               <module-option name="portalContainerName">portal</module-option>
                               <module-option name="realmName">gatein-domain</module-option>
                             </login-module>
                             <login-module code="org.exoplatform.services.security.j2ee.JbossLoginModule" flag="required">
                               <module-option name="portalContainerName">portal</module-option>
                               <module-option name="realmName">gatein-domain</module-option>
                             </login-module>
                           </authentication>

- Edit PLF_HOME/gatein/conf/configuration.properties and add the following properties:

                         ########################
                         # SSO-EXTENSION
                         ########################

                         #Single Sign On server address
                         sso.server.url=http://localhost:8888/josso/signon/login.do
                         #Portal Server domain name
                         sso.platform.url=http://localhost:8080/portal
                         #SSO Server Type
                         sso.josso.server.type=josso

3) OpenAM server
----------------

1.1) eXo Platform with Tomcat
-----------------------------

- Move the jar PROJECT_HOME/sso-extension/component/sso-extensible-filter/target/exo.platform.sso-extension.component.sso-extensible-filter-0.1.jar to PLF_HOME/lib
- Move the jar PROJECT_HOME/sso-extension/config/target/exo.platform.sso-extension.config-0.1.jar to PLF_HOME/lib.
- Move the jars under SSO_HOME/gatein-sso-1.1.1-GA/openam/gatein.ear/lib to the PLF_HOME/lib
- Move the war PROJECT_HOME/sso-extension/webapp/target/sso-extension.war to PLF_HOME/webapp.
- Edit PLF_HOME/conf/jaas.conf, uncomment on this section and comment other parts:

                        org.gatein.sso.agent.login.SSOLoginModule required;
                        org.exoplatform.services.security.j2ee.TomcatLoginModule required
                        portalContainerName=portal
                        realmName=gatein-domain;

- Edit PLF_HOME/webapps/portal.war/META-INF/context.xml and add ServletAccessValve into configuration as first sub-element of Context:

                        <Context path='/portal' docBase='portal' ... >
                            <Valve className='org.gatein.sso.agent.tomcat.ServletAccessValve' />
                             ...
                        </Context>

- Edit PLF_HOME/gatein/conf/configuration.properties and add the following properties:

                        ########################
                        # SSO-EXTENSION
                        ########################

                        #Single Sign On server address
                        sso.server.url=http://localhost:8888/opensso
                        #Portal Server domain name
                        sso.platform.url=http://localhost:8080/portal
                        #SSO Server Type
                        sso.server.type=openAM
                        #The name of the registred openAM cookie where openAM SSO ticket is stored
                        sso.openam.cookie.name=iPlanetDirectoryPro

1.2) eXo Platform with JBoss AS
-------------------------------

- Move the jar PROJECT_HOME/sso-extension/component/sso-extensible-filter/target/exo.platform.sso-extension.component.sso-extensible-filter-0.1.jar to PLF_HOME/gatein.ear/lib
- Move the jar PROJECT_HOME/sso-extension/config/target/exo.platform.sso-extension.config-0.1.jar to PLF_HOME/gatein.ear/lib.
- Move the jars under SSO_HOME/gatein-sso-1.1.1-GA/openam/gatein.ear/lib to the PLF_HOME/gatein.ear/lib.
- Move the ear PROJECT_HOME/sso-extension/ear/target/so-extension.ear to PLF_HOME/server/default/deploy.
- edit gatein.ear/META-INF/gatein-jboss-beans.xml and uncomment on this section:

                         <authentication>
                             <login-module code="org.gatein.sso.agent.login.SSOLoginModule" flag="required">
                               <module-option name="portalContainerName">portal</module-option>
                               <module-option name="realmName">gatein-domain</module-option>
                             </login-module>
                             <login-module code="org.exoplatform.services.security.j2ee.JbossLoginModule" flag="required">
                               <module-option name="portalContainerName">portal</module-option>
                               <module-option name="realmName">gatein-domain</module-option>
                             </login-module>
                           </authentication>

- Edit PLF_HOME/gatein/conf/configuration.properties and add the following properties:

                          ########################
                          # SSO-EXTENSION
                          ########################

                          #Single Sign On server address
                          sso.server.url=http://localhost:8888/opensso
                          #Portal Server domain name
                          sso.platform.url=http://localhost:8080/portal
                          #SSO Server Type
                          sso.server.type=openAM
                          #The name of the registred openAM cookie where openAM SSO ticket is stored
                          sso.openam.cookie.name=iPlanetDirectoryPro

Troubleshooting
================

Maven dependencies issues
-------------------------

While Platform should build without any extra maven repository configuration it may happen that the build complains about missing artifacts.

If you encounter this situation, please let us know via our forums (http://forum.exoplatform.org).

As a quick workaround you may try setting up maven repositories as follows.

Create file settings.xml in $HOME/.m2  (%HOMEPATH%\.m2 on Windows) with the following content:

                             <settings>
                               <profiles>
                                 <profile>
                                   <id>jboss-public-repository</id>
                                   <repositories>
                                     <repository>
                                       <id>jboss-public-repository-group</id>
                                       <name>JBoss Public Maven Repository Group</name>
                                       <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
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
                                       <id>jboss-public-repository-group</id>
                                       <name>JBoss Public Maven Repository Group</name>
                                       <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
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
                                 <activeProfile>jboss-public-repository</activeProfile>
                                 <activeProfile>exo-public-repository</activeProfile>
                               </activeProfiles>
                             </settings>


Going Further
=============
* Developers: learn how to build your own portal, gadgets, REST services or eXo-based applications in the Developer Guide [http://docs.exoplatform.com/PLF35/topic/org.exoplatform.doc.35/DeveloperGuide.html] and the Reference Documentation [http://docs.exoplatform.com/PLF35/topic/org.exoplatform.doc.35/GateInReferenceGuide.html]
* Administrators: learn how to install eXo Platform on a server in the Administrator Guide: http://docs.exoplatform.com/PLF35/topic/org.exoplatform.doc.35/AdministratorGuide.html
* End Users: learn more about using the features in the User Manuals: http://docs.exoplatform.com/PLF35/topic/org.exoplatform.doc.35/UserGuide.html
