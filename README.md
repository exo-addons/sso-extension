sso-extension
=============

SSO Extension for eXo Platform

This extension is created to make easier the integration of an sso server with eXo Platform.

We are going to explain the steps of integration for each SSO server:

Here is some common configurations for the three supported sso server:

- Download the sso-packaging from this link: https://repository.jboss.org/nexus/content/groups/public/org/gatein/sso/sso-packaging/. SSO_HOME refers to the directory where you made your download.
- PLF_HOME is the location of the unzipped eXo Platform server.
- Clone this project. PROJECT_HOME is the directory where you cloned the project.
- Build this project by typing: mvn clean install under PROJECT_HOME.
- Move the jar exo.platform.sso-extension.component.sso-extensible-filter-0.1.jar under PROJECT_HOME/sso-extension/component/sso-extensible-filter/target to PLF_HOME/lib
- Move the jar exo.platform.sso-extension.config-0.1.jar under PROJECT_HOME/sso-extension/config/target to PLF_HOME/lib.
- Move the war sso-extension.war under PROJECT_HOME/sso-extension/webapp/target to PLF_HOME/webapp.
- If you are working with a eXo Platform packaged with tomcat so edit PLF_HOME/webapps/portal.war/META-INF/context.xml and add ServletAccessValve into configuration as first sub-element of Context:

                        <Context path='/portal' docBase='portal' ... >
                           <Valve className='org.gatein.sso.agent.tomcat.ServletAccessValve' />
                           ...
                        </Context>

- In JBoss AS, edit gatein.ear/META-INF/gatein-jboss-beans.xml and uncomment on this section:

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

- In Tomcat, edit PLF_HOME/conf/jaas.conf, uncomment on this section and comment other parts:

                         org.gatein.sso.agent.login.SSOLoginModule required;
                         org.exoplatform.services.security.j2ee.TomcatLoginModule required
                         portalContainerName=portal
                         realmName=gatein-domain;

- Replace the entire contents of PLF_HOME/platform-extension.war/login/jsp/login.jsp with:

                          <html>
                             <head>
                               <script type="text/javascript">
                                window.location = '/portal/sso';
                               </script>
                             </head>
                             <body>
                             </body>
                           </html>

 Now we are going to list special steps to each sso server:

********CAS server********

- Move the jars under SSO_HOME/gatein-sso-{version}/cas/gatein.ear/lib to the PLF_HOME/lib
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

********JOSSO server********

- Move the jars under SSO_HOME/gatein-sso-{version}/josso/gatein.ear/lib to the PLF_HOME/lib
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

********OpenAM server********

- Move the jars under SSO_HOME/gatein-sso-1.1.1-GA/openAM/gatein.ear/lib to the PLF_HOME/lib
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








