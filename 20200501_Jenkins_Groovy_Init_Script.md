{% include_relative menu.md %}

# 20200501 Jenkins Groovy Init Script

I have spent some hours on learning how to create an `init.groovy` script for Jenkins to

1. create a user
2. generate an API token for that user
3. create a Jenkins node
4. get the JnlpMac for that node

and I have tested, that I can

5. read environment variables
6. read/write files
7. parse/generate JSON

as outside of Jenkions.

This is my script :

        import hudson.slaves.SlaveComputer
        import jenkins.model.Jenkins
        import jenkins.security.ApiTokenProperty
        import hudson.model.User
        import hudson.model.Slave
        import hudson.model.Node.Mode;
        import hudson.slaves.EnvironmentVariablesNodeProperty.Entry
        import hudson.slaves.ComputerLauncher
        import hudson.slaves.JNLPLauncher
        import hudson.slaves.DumbSlave
        import hudson.slaves.RetentionStrategy
        import hudson.slaves.EnvironmentVariablesNodeProperty
        import hudson.security.HudsonPrivateSecurityRealm
        import java.util.logging.LogManager
        import groovy.json.JsonOutput
        import groovy.json.JsonSlurper

        def userId = "user6"
        def password = "password"
        def tokenName = "api-token6"
        def nodeName = "node6"
        def instance = Jenkins.getInstanceOrNull()
        def existingUser = User.getById(userId, false)
        def logger = LogManager.getLogManager().getLogger("")
        HashMap<String,String> resultMap = new HashMap<>()

        // to fake reading some JSON from a file which could be used as input
        File inputFile = new File(System.getenv("USERPROFILE") + File.separator + "jenkins-input.json")
        def jsonSlurper = new JsonSlurper()
        def object = jsonSlurper.parseText(inputFile.text)
        logger.info("input from file : " + object.toString())

        if (existingUser == null) {
          def hudsonRealm = new HudsonPrivateSecurityRealm(false)
          existingUser = hudsonRealm.createAccount(userId,password)
          instance.setSecurityRealm(hudsonRealm)
          instance.save()
          logger.info("created user : " + userId)
          resultMap.put(userId, password)
        } else {
          logger.info("user is already created : " + userId)
        }

        def apiTokenProperty = existingUser.getProperty(ApiTokenProperty.class)
        if (!apiTokenProperty.tokenStore.getTokenListSortedByName().contains(tokenName)) {
          def result = apiTokenProperty.tokenStore.generateNewToken(tokenName)
          existingUser.save()
          logger.info("API Token : " +tokenName + " has value " + result.plainValue)
          resultMap.put(tokenName, result.plainValue)
        } else {
          logger.info("API Token : " + tokenName + " already generated")
        }

        if (instance.getNode(nodeName) == null) {
          // Define a "Launch method": "Launch slave agents via SSH"
          ComputerLauncher launcher = new JNLPLauncher()

          // Define a "Permanent Agent"
          Slave agent = new DumbSlave(nodeName,
              "nodeDescription",
              "c:\\jenkinsSlave",
              "1",
              Mode.NORMAL,
              "labelString",
              launcher,
              new RetentionStrategy.Always())

          List<Entry> env = new ArrayList<Entry>()
          env.add(new Entry("key1","value1"))
          env.add(new Entry("key2","value2"))
          EnvironmentVariablesNodeProperty envPro = new EnvironmentVariablesNodeProperty(env)

          agent.getNodeProperties().add(envPro)

          // Create a "Permanent Agent"
          instance.addNode(agent)

          logger.info("Node has been created successfully :" + nodeName)
        } else {
          logger.info("Node is already created : " + nodeName)
        }

        SlaveComputer nodeComputer = instance.getComputer(nodeName)
        logger.info("JnlpMac of node " + nodeName + " : " + nodeComputer.getJnlpMac())
        resultMap.put(nodeName, nodeComputer.getJnlpMac())

        def json = JsonOutput.toJson(resultMap)
        String jsonString = JsonOutput.prettyPrint(json)
        logger.info(jsonString)

        File outputFile = new File(System.getenv("USERPROFILE") + File.separator + "jenkins-credentials.json")
        if (outputFile.exists()) {
          outputFile.delete()
        }
        outputFile << jsonString

and I have located all `.jar`files in my Jenkins home `%USERPROFILE%\.jenkins` needed in IntelliJ as libraries,
so that I was able to develop this script in `Intellij Idea` with code completion working :

* jenkins-core-2.234.jar
* stapler-1.259.jar
* stapler-adjunct-codemirror-1.3.jar
* stapler-adjunct-timeline-1.5.jar
* stapler-adjunct-zeroclipboard-1.3.5-1.jar
* stapler-groovy-1.259.jar
* stapler-jelly-1.259.jar
* stapler-jrebel-1.259.jar

For `stapler` I simply took all of them as one was needed by one of the classes from `jenkins-core`.

I have used [https://javadoc.jenkins.io/](https://javadoc.jenkins.io/) to cross check the code I have found in various blogs linked by google.

My biggest problem was to find out that `instance.getComputer(nodeName)` returns a `SlaveComputer` object. 
The interface of `instance.getComputer(nodeName)` tells me a `Computer`object is returned and that is abstract and does not declare or define
the function `getJnlpMac()`.
