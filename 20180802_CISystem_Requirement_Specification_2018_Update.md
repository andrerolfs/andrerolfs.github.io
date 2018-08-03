# CISystem Requirement Specification 2018 Update

It is two years back, when I wrote on the 3rd of July 2016 my first requirement specification for
CISystem.

Now 2 years later, there is still no implementation for it. Some minor parts like the H2 logging have
evolved to a generic framework, but I always felt that my concept of a multi-layer implementation
based on own protocols communicating via sockets is somehow old-school and will not help me
 much in the future.

At that time I did not really know how to utilize Jenkins to do the major part of the job.
Now I have gained some experience for how it could work and changed my opinion.
Jenkins will be the centerpiece of CISystem.

This requirement specification update connects the original requirements with using Jenkins.

This is no architecture requirement specification like the version two years ago,
this is an implementation requirement specification.

## Nodes

**Implementation Requirement :**

A node is a Jenkins master node.

The network of nodes shall be independent of a single master node to reduce the problem that
when the master goes down the whole network stops working.

When a node goes down an other node can take over its work.

### R1 Node States

R1-1 Every node can be removed at any time without destroying the network between the other nodes.

R1-2 There is always a master node responsible for the scheduling.

R1-3 If a master goes down, there is always a node taking over its responsibilities.

R1-4 There is a priority list over all nodes defining when a node becomes the new master.

R1-5 All nodes are always updated about all state changes of all nodes in the network.

R1-6 A node is working as its own server till it gets connected again to the network.

R1-7 All nodes can report about the status of joblists and jobs.

### R2 Joblists And Jobs

R2-1 A joblist is a list of jobs.

R2-2 A joblist has a number of execution stages.

R2-3 The joblist defines the order of its execution stages.

R2-4 All jobs of an execution stage can be executed in parallel.

R2-5 The next execution stage is entered when all jobs of the current stage are finished.

R2-6 A joblist or job has a name.

**Implementation Requirement :**

A joblist is implemented like the Composite pattern.

An element of a joblist can be a job or a joblist.

### R3 Resources And Scheduling

R3-1 A resource has a type and an identifier, to tell which resource type you need to get an ID returned which is a concrete instance of the type.

R3-2 Nodes provide resources.

R3-3 Jobs need to have resources to be executed.

R3-4 Joblists are started in the order they are added.

R3-5 A joblist excution is started when there is no other active joblist requiring the currently available resources.

**Implementation Requirement :**

A joblist is executed by starting a Jenkins project build.

A job is executed by executing a Jenkins project build.

A resource is a Jenkins project.

### R4 Controlling Joblists And Jobs

R4-1 A joblist can be started via any node.

R4-2 A running joblist or job can be stopped via any node.

R4-3 The ID of a joblist or job is defined by the master, so you can ask with the name for the joblists and jobs IDs currently running.

R4-4 All finished joblists and their jobs are written to a log at every node.

R4-5 Every minute every node writes to the log which nodes are online.

**Implementation Requirement :**

Jenkins project builds have paramaters with the IDs,
these parameters can be queried with the Jenkins REST API.

Jenkins jobs can be stopped via Jenkins REST API.

### R5 Execution Of Jobs

R5-1 A job is always executed via script execution, like calling a batch file or a bash script.

**Implementation Requirement :**

Do exactly this in a Jenkins project.

### R6 Configuration And Templates

R6-1 A job can have any number of configuration files.

R6-2 A configuration file can be a template, where patterns are replaced before execution.

R6-3 A job can have a property file of replacement rules where a pattern is replaced with a value in all attached files.

R6-4 Every single file of a job can have its own replacement property file, which is only used for replacing patterns with values in this particular file.

R6-5 A pattern is a string which is replaced as is without interpretation with a values of a replacement map.

**Implementation Requirement :**

The configuration files are provided as parameters in the Jenkins projects.

### R7 A Plugin API Shall Not Be Required

It took me some days to understand I want to have such a requirement like this one.

This requirement does not mean CISystem will be the greatest software and simply supports anything you need.

It means when you need to change the behaviour of CISystem, then you shall be able to achieve this only with executing your scripts, providing them your configuration files and adapting your scripts and configuration files on-the-fly using the replacement maps.

That would mean you don't need to learn my API, use the language I have decided for such plugins and don't have to deal with the problem, that it takes a lot of time to solve your problem with my API.

It does not mean you don't have to write code, but I think it is always better to have the option to choose which code you want to write in which language.

**Implementation Requirement :**

Whatever you want to execute and in which order, you can write it into joblists of joblists.

It is your decision to to write specific Jenkins projects or scripts executed by Jenkins projects.

It is your decision in which language you write your scripts.
