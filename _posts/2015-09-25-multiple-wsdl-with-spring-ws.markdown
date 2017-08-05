---
layout: post
title: "Multiple WSDL With spring-ws"
date: 2015-09-25T21:04:18+05:30
author: Swathi Venkatachala
comments: true
tags:
- org.springframework.oxm.UncategorizedMappingException
- javax.xml.bind.JAXBException
- maven plugin
- org.jvnet.jax-ws-commons
- jaxws-maven-plugin
- multiple wsdl
- spring-ws
- spring webservices
- tech
---
Three months ago, when I was working on webservices: SOAP and spring-ws, I encountered an issue.
The WSDLs were from a third party and the plugin I used was `org.jvnet.jax-ws-commons:jaxws-maven-plugin`.
The plugin was used with the goal `wsimport` which generates JAX-WS portable artifacts used in JAX-WS
clients and services. The tool reads a WSDL and generates all the required artifacts for web service 
development, deployment, and invocation.

Initially, I used a common namespace for all the WSDL, until I noticed that it was a culprit.
The following is a snippet of the plugin usage, I used initially:
{% highlight java%}
<!-- tag::wsdl[] -->
            <plugin>
                <groupId>org.jvnet.jax-ws-commons</groupId>
                <artifactId>jaxws-maven-plugin</artifactId>
                <version>2.3</version>
		<executions>
                    <execution>
                       <id>wsimport-services</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>wsimport</goal>
                        </goals>
		    </execution>
		</executions>
                <configuration>
                            <verbose>true</verbose>
                            <xdebug>true</xdebug>
                            <wsdlDirectory>src/main/wsdl</wsdlDirectory>
                            <wsdlFiles>
                                <wsdlFile>Service1</wsdlFile>
                                <wsdlFile>Service2</wsdlFile>
                                <wsdlFile>Service3</wsdlFile>
                            </wsdlFiles>
                            <!-- Package name -->
                            <packageName>com.geekz.anon.model</packageName>
                            <!-- generated source files destination-->
                            <sourceDestDir>target/generated-sources/src</sourceDestDir>
                 </configuration>
            </plugin>

<!-- tag::adding generated source -->
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>build-helper-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <id>add-source</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>add-source</goal>
                        </goals>
                        <configuration>
                            <sources>
                                <source>target/generated-code/src</source>
                            </sources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
{% endhighlight %}

So, after using the above plugin in a common namespace ie. `com.geekz.anon.model`, I encountered the following error.
`org.springframework.oxm.UncategorizedMappingException: Unknown JAXB exception; nested exception is javax.xml.bind.JAXBException: class com.geekz.anon.model.SomeClass nor any of its super class is known to this context.`

I discovered that the issue was sharing a common namespace, where the last WSDL ie. Service3 was rewriting on the already generated files by Service2 and Service1.
So, the best bet was to have multiple specifications of a set of goals to execute during the build lifecycle, each having a different configuration.

So, refactored it to the following, placing them into different namespaces.
{% highlight java%}
            <plugin>
                <groupId>org.jvnet.jax-ws-commons</groupId>
                <artifactId>jaxws-maven-plugin</artifactId>
                <version>2.3</version>
                <executions>
                    <execution>
                        <id>Service1</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>wsimport</goal>
                        </goals>
                        <configuration>
                            <verbose>true</verbose>
                            <xdebug>true</xdebug>
                            <wsdlDirectory>src/main/wsdl</wsdlDirectory>
                            <wsdlFiles>
                                <wsdlFile>Service1</wsdlFile>
                            </wsdlFiles>
                            <!-- Package name -->
                            <packageName>com.geekz.anon.model.Service1</packageName>
                            <!-- generated source files destination-->
                            <sourceDestDir>target/generated-sources/src</sourceDestDir>
                        </configuration>
                    </execution>
                    <execution>
                        <id>Service2</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>wsimport</goal>
                        </goals>
                        <configuration>
                            <verbose>true</verbose>
                            <xdebug>true</xdebug>
                            <wsdlDirectory>src/main/wsdl</wsdlDirectory>
                            <wsdlFiles>
                                <wsdlFile>Service2</wsdlFile>
                            </wsdlFiles>
                            <!-- Package name -->
                            <packageName>com.geekz.anon.model.Service2</packageName>
                            <!-- generated source files destination-->
                            <sourceDestDir>target/generated-sources/src</sourceDestDir>
                        </configuration>
                    </execution>
                    <execution>
                        <id>Service3</id>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>wsimport</goal>
                        </goals>
                        <configuration>
                            <verbose>true</verbose>
                            <xdebug>true</xdebug>
                            <wsdlDirectory>src/main/wsdl</wsdlDirectory>
                            <wsdlFiles>
                                <wsdlFile>Service3</wsdlFile>
                            </wsdlFiles>
                            <!-- Package name -->
                            <packageName>com.geekz.anon.model.Service3</packageName>
                            <!-- generated source files destination-->
                            <sourceDestDir>target/generated-sources/src</sourceDestDir>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
{% endhighlight %}

Now, all the classes were generated in their respective packages. However, there was an issue with the spring framework mapping. All I had to do was explicitly define the context path which earlier was just at the `com.geekz.anon.model` level, which perphaps is different now for each of the service.
I fixed it as below. ( Please follow the same for each of the services.)

{% highlight java%}
	<bean id="service1Marshaller" class="org.springframework.oxm.jaxb.Jaxb2Marshaller">
		<property name="contextPath"
			value="com.geekz.anon.model.Service1" />
	</bean>
	<bean id="service1"
		class="com.geekz.anon.client.Service1Client">
		<property name="defaultUri" value="${service1.endpoint}" />
		<property name="marshaller" ref="service1Marshaller"></property>
		<property name="unmarshaller" ref="service1Marshaller"></property>
	</bean>
{% endhighlight %}

This fixed all the errors and I was able to make all the webservice calls successfully!

Happy WebService Calling! :)

