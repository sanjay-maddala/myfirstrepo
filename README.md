WizToWar - Have your cake and eat it too
========================================

WizToWar is a simple library that enables a [Dropwizard](http://dropwizard.io) service to also be deployable in a WAR container such as Tomcat.

By following the steps in the usage section below you will be able to create
both a Dropwizard jar and a WAR of the same service.


Caveat emptor:
--------------

* Only tested on Tomcat 7
* No support for bundles
* Many features untested
* Goes against the whole philosophy of Dropwizard...

Usage
------

Include the wiztowar jar as a dependency:

```xml
<dependency>
    <groupId>com.twilio</groupId>
    <artifactId>wiztowar</artifactId>
    <version>1.3</version>
</dependency>
```

Create a new class for your application like this:

```java
package com.twilio.mixerstate;

import com.google.common.io.Resources;
import com.twilio.wiztowar.DWAdapter;
import com.yammer.dropwizard.Service;

import java.io.File;
import java.net.URISyntaxException;
import java.net.URL;


public class MixerStateDWApplication extends DWAdapter<MixerStateServiceConfiguration> {
    final static MixerStateService service = new MixerStateService();

    /**
    * Return the Dropwizard service you want to run.
    */
    public Service getSingletonService(){
         return service;
    }

    /**
    * Return the File where the configuration lives.
    */
    @Override
    public File getConfigurationFile() {

        URL url = Resources.getResource("mixer-state-server.yml");
        try {
            return new File(url.toURI());
        } catch (URISyntaxException e) {
            throw new IllegalStateException(e);
        }
    }
}
```


Create a main/webapp/WEB-INF/web.xml file:
------------------------------------------

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app>
    <!--- This listener is required to hook in to the lifecycle of the WAR -->
    <listener>
        <listener-class>com.twilio.mixerstate.MixerStateDWApplication</listener-class>
    </listener>
</web-app>
```

Make sure you also build a WAR artifact
---------------------------------------------

There are two alternatives to building a war:

### a. Add instructions to also build a WAR

This goes in `<build><plugins>` section:

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <version>2.4</version>
    <executions>
        <execution>
            <id>default-war</id>
            <phase>package</phase>
            <goals>
                <goal>war</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <webappDirectory>target/webapp</webappDirectory>
    </configuration>
</plugin>
```

### b. Change packaging of your Dropwizard service

If you do not intend to run the Dropwizard service standalone, you can simply
change the "packaging" element in pom.xml to be "war" instead of "jar".


