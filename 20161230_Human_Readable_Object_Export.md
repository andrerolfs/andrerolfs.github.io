{% include_relative menu.md %}

# 20161230 Human Readable Object Export

Now and then it happens you need to extract the data of an object to transfer
or store it.

You could use Java's serialize functionality, but for deserialization the
class must have the same structure as while serialization.

I was looking for a way to do this where

1. there is only a minimum effort to implement the export
2. I am not restricted when loading the data into new structures
3. the data is represented human readable

My first idea was to solve this problem by implementing an export to XML.

The XML would be human readable and I could read the XML and write the data
into any new structure, but this would cost me more effort than I wanted to spend.

My next idea was to use JSON, because in comparison to my experience with
reading XML in Java I favour reading JSON in Groovy much more, its simply easier.

I gave it a try and started googling for how to write data into a JSON structure
using Groovy.

What I found was `groovy.json.JsonBuilder()` creates a JSON builder, where you
just have to pass objects to the `call()` function to export them to JSON.

This is the [JsonBuilder API](http://docs.groovy-lang.org/latest/html/gapi/groovy/json/JsonBuilder.html).

I have implemented a demonstrator expample to check it works for me. This example
uses a pre-version of the JPA model class I want to use for [logging](https://andrerolfs.github.io/genericdatabase.html)
into the **H2 database**, it contains these types of members

* Long
* String
* enum
* ArrayList of objects holding Long and String

This class is complex enoudh to show how good the `JsonBuilder` performs for my problem and how
much effort I have to spend for workarounds.

Let me first show you how `JsonBuilder` is used, then I show you my results and
at the end let's have a look at the classes and scripts I have implemented for this example.

This is the code where I create my object and feed it to the `JsonBuilder` :

    CILog log = new CILog()

    log.setLogId(1234567)
    log.setSummary("This is a summary of this log entry")
    log.setFulltext("This is the fulltext explaining what happened")
    log.setNodeId("The nodeId tells us which node has logged this information")
    log.setTimestamp( Calendar.getInstance())
    log.setLogEntryType(CIEnumerations.CILogEntryType.Exception)

    log.addEntry(new CIEntryData(1234, "content 1234"))
    log.addEntry(new CIEntryData(2234, "content 2234"))
    log.addEntry(new CIEntryData(3234, "content 3234"))
    log.addEntry(new CIEntryData(4234, "content 4234"))

    def json = new groovy.json.JsonBuilder()
    def result = json log

    println json.toPrettyString()

and this is the output :

		[
				{
						"summary": "This is a summary of this log entry",
						"nodeId": "The nodeId tells us which node has logged this information",
						"entryList": [
								{
										"index": 1234,
										"content": "content 1234"
								},
								{
										"index": 2234,
										"content": "content 2234"
								},
								{
										"index": 3234,
										"content": "content 3234"
								},
								{
										"index": 4234,
										"content": "content 4234"
								}
						],
						"logEntryType": "Exception",
						"fulltext": "This is the fulltext explaining what happened",
						"logId": 1234567,
						"timestamp": "2016-12-30T09:37:13+0000"
				}
		]

You see, you only need one line of code to feed all data of an object and
all other objects connected to it via a list to a `JsonBuilder`, in this case
`json log`, which is a short form of `json.call(log)`.

For me this is better than all my expectations.

The only requirement seems to be, you need to provide *getters* for all members
you want to see in the JSON configuration, but IDEs like [IntelliJ IDEA](https://www.jetbrains.com/idea/download/)
provide functions to generate *getters and setters*.

This could be interesting using [JPA](https://en.wikipedia.org/wiki/Java_Persistence_API)
when you need to export your database, one reason why I have chosen an `Entity` class.

How to read the data from a JSON configuration is documented in my blog post
about using the [JsonSlurper](java.html#calling-groovy-from-java-to-read-json).

This is the class the `JsonBuilder` exports :

		package com.wartbar.db.model;

		import javax.persistence.Temporal;
		import javax.persistence.TemporalType;
		import javax.persistence.Column;
		import javax.persistence.Enumerated;
		import javax.persistence.EnumType;
		import javax.persistence.Entity;
		import javax.persistence.GeneratedValue;
		import javax.persistence.GenerationType;
		import javax.persistence.Id;
		import javax.persistence.Table;

		import java.util.ArrayList;

		import com.wartbar.util.CIEnumerations.CILogEntryType;

		@Entity
		@Table(name = "LOG")
		public class CILog implements java.io.Serializable {

			public CILog() {
				entryList = new ArrayList<>();
			}

			public void setLogId(long logId) {
				this.logId = logId;
			}

			public long getLogId() {
				return logId;
			}

			@Id
			@GeneratedValue(strategy = GenerationType.AUTO)
			@Column(name = "LOGID")
			private long logId;

			public java.util.Calendar getTimestamp() {
				return timestamp;
			}

			public void setTimestamp(java.util.Calendar timestamp) {
				this.timestamp = timestamp;
			}

			@Temporal(TemporalType.TIMESTAMP)
			java.util.Calendar timestamp;

			public String getSummary() {
				return summary;
			}

			public void setSummary(String summary) {
				this.summary = summary;
			}

			@Column(name = "SUMMARY")
			private String summary;

			public String getFulltext() {
				return fulltext;
			}

			public void setFulltext(String fulltext) {
				this.fulltext = fulltext;
			}

			@Column(name = "FULLTEXT")
			private String fulltext;

			public String getNodeId() {
				return nodeId;
			}

			public void setNodeId(String nodeId) {
				this.nodeId = nodeId;
			}

			@Column(name = "NODEID")
			private String nodeId;

			public CILogEntryType getLogEntryType() {
				return logEntryType;
			}

			public void setLogEntryType(CILogEntryType logEntryType) {
				this.logEntryType = logEntryType;
			}

			@Enumerated(EnumType.ORDINAL)
			private CILogEntryType logEntryType;

			public void addEntry(CIEntryData entryData) {
				entryList.add(entryData);
			}

			public ArrayList<CIEntryData> getEntryList() {
				return entryList;
			}

			public void setEntryList(ArrayList<CIEntryData> entryList) {
				this.entryList = entryList;
			}

			private ArrayList<CIEntryData> entryList;
		}

This is the 'enum' used in the example :

		package com.wartbar.util;

		public class CIEnumerations {

			public enum CILogEntryType {
				Info (1), Problem (2), Exception (3);

				private final int value;
				private CILogEntryType(int v) { value = v; }
				public int getVal() { return value; }
			};
		}

This is the class used in the 'ArrayList' :

		package com.wartbar.db.model;

		public class CIEntryData {

			public Long getIndex() {
				return index;
			}

			public void setIndex(Long index) {
				this.index = index;
			}

			private Long index;

			public String getContent() {
				return content;
			}

			public void setContent(String content) {
				this.content = content;
			}

			private String content;

			public CIEntryData(Long index, String content) {
				this.index = index;
				this.content = content;
			}

		}

This is the 'buildSrc/build.gradle' to provide the 'javax.persistence' dependencies :

		apply plugin: 'groovy'
		apply plugin: 'java'

		repositories {
			mavenLocal()
			jcenter()
			//mavenCentral()
		}

		dependencies {
			compile (
							'javax.persistence:persistence-api:1.0.2',
							'org.codehaus.groovy:groovy-all:2.4.6',
							'com.h2database:h2:1.4.193',
							'org.eclipse.persistence:eclipselink:2.6.4',
							'org.eclipse.persistence:org.eclipse.persistence.jpars:2.6.4',
							'org.eclipse.persistence:org.eclipse.persistence.jpa.modelgen.processor:2.6.4',
							'org.eclipse.persistence:org.eclipse.persistence.jpa:2.6.4',
			)

			testCompile(
							'junit:junit:4.12',
							'org.codehaus.groovy:groovy-all:2.4.6',
							'org.spockframework:spock-core:1.0-groovy-2.4',
			)

			testRuntime(
							'com.athaydes:spock-reports:1.2.7'
			)
		}

