{% include_relative  menu.md %}

# My Own Nexus

To be able to work offline with Maven, I have set up my own Nexus server to proxy Maven Central.

This is what you need to do :

1. download Nexus from here : [https://help.sonatype.com/repomanager3/product-information/download/download-archives---repository-manager-3](https://help.sonatype.com/repomanager3/product-information/download/download-archives---repository-manager-3)
2. unzip and start with <b>bin/nexus run</b>
3. the proxy repository is already set up
4. you can configure the path to where you want to have the Blob Stores in Repository/Blob Stores 
5. my .m2/settings.xml looks like this :    
        <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">

            <mirrors>
                <mirror>
                    <id>central</id>
                    <name>central</name>
                    <url>http://localhost:8081/repository/maven-central</url>
                    <mirrorOf>*</mirrorOf>
                </mirror>
            </mirrors>        

        </settings>

  
