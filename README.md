Dynamic Extensions for Alfresco
===============================

Rapid development of Alfresco repository extensions in Java. Deploy your code in seconds, not minutes. Less overhead, more convenience, Life is too short for endless server restarts.

Dynamic Extensions adds an OSGi container to the Alfresco repository, enabling live deployment of Java code packages as OSGi bundles. Alfresco itself is not "OSGi-fied" in any way. The OSGi container runs completely separate from the core Alfresco platform.

Latest release: Milestone 4
---------------------------

* **New**: Control Panel web interface for managing Dynamic Extensions.
* **New**: Annotation-based Web Scripts now support Freemarker templates.

In progress: Milestone 5
------------------------

* Gradle plugin for building your own repository extensions. Your projects can be built both as an OSGi bundle and a standalone AMP.
* Spring AOP support. (The Gradle plugin configures the OSGi bundle to avoid classpath issues.)
* `@RunAs` and `@RunAsSystem` annotations for running code as a particular user.
* `@Transactional` annotation for running code within a transaction.
* Improvements for building custom user interfaces on top of annotation-based Web Scripts.
* Code review of annotation-based Actions and Behaviours.

Milestone 6 wraps up all the work that went into Dynamic Extensions over the past two years, improving documentation and test coverage.

Installing Dynamic Extensions in an Alfresco repository
-------------------------------------------------------

Dynamic Extensions is distributed as an Alfresco Module Package (AMP).

* Download the <a href="https://github.com/lfridael/dynamic-extensions-for-alfresco/raw/mvn-repo/nl/runnable/alfresco/dynamicextensions/alfresco-module/1.0.0.M4/alfresco-module-1.0.0.M4.amp">Dynamic Extensions Milestone 4 AMP</a>.
* Use the <a href="http://docs.alfresco.com/4.0/index.jsp?topic=%2Fcom.alfresco.enterprise.doc%2Ftasks%2Famp-install.html">Module Management Tool</a> to install the AMP in the Alfresco repository.
* After restarting Alfresco, open the Control Panel: <a href="http://localhost:8080/alfresco/service/dynamic-extensions/">http://localhost:8080/alfresco/service/dynamic-extensions/</a>.
* Accessing the Control Panel requires an admin account.

Support for Alfresco Community
------------------------------

Dynamic Extensions is developed and tested against:

* Alfresco Community 3.4
* Alfresco Community 4.0
* Alfresco Community 4.2

Support for Alfresco Enterprise
-------------------------------

* Alfresco Enterprise 4.0
* Alfresco Enterprise 4.1

The Enterprise editions are not part of the development and testing environment, but Dynamic Extensions is known to work on them.


Example extension code
----------------------

This example Web Scripts examines a node and passed information to a Freemarker template:
```java
@ManagedBean
@WebScript
public ExampleWebScript {

  @Inject
  private NodeService nodeService;
  
  @Uri("/show-node")
  // Example: http://localhost/alfresco/service/show-node?nodeRef=workspace://SpacesStore/12345
  public Map<String, Object> displayNodeName(@RequestParam NodeRef nodeRef) {
    Map<String, Object> model = new HashMap<String, Object>();
    model.put("properties", nodeService.getProperties(nodeRef));    
    return model; // Model is passed to Freemarker template.
  }
}
```

And here's the fragment from the Freemarker template:

```html
<table>
  <#list properties?keys as name>    
    <tr>
      <th>${name}</th>
      <td>${properties[name]!''}</td>
    </tr>
  </#list>
</table>
```

Essentially, this is all the code that is required. There's no need for Spring XML config or Web Script XML descriptors. While reducing XML config overhead through annotations is not a particularly novel concept in the Java world at large, Alfresco is still hampered by its legacy approach. In short: Dynamic Extensions brings the development of Alfresco repository extensions more in line with a modern Java approach. Life is too short.

This may be a trivial example, but the point is that, behind the scenes, services are still wired together through Spring and handled by the Web Script framework. Conceptually there is no difference between a Dynamic Extension and a regular Alfresco extension. There's just less overhead and more convenience.

Aside from annotation-based Web Scripts, Dynamic Extensions also offers annotations for Actions and Behaviours.

Creating your own Alfresco extensions
-------------------------------------

The <a href="https://github.com/lfridael/example-dynamic-extension">example Dynamic Extension</a> provides a good starting point for creating your own extensions.

Clone the example repo and explore it. Here are some pointers to get you going:

* The <a href="https://github.com/lfridael/example-dynamic-extension/blob/master/pom.xml">pom.xml</a> serves as a template for your own Maven-based projects.
* Annotation-based Web Scripts: <a href="https://github.com/lfridael/example-dynamic-extension/blob/master/src/main/java/nl/runnable/alfresco/examples/CategoriesWebScript.java">First example</a> and <a href="https://github.com/lfridael/example-dynamic-extension/blob/master/src/main/java/nl/runnable/alfresco/examples/HelloWebScript.java">another example</a>.
* <a href="https://github.com/lfridael/example-dynamic-extension/blob/master/src/main/java/nl/runnable/alfresco/examples/ExampleActions.java">Annotation-based Action example</a >  and a <a href="https://github.com/lfridael/example-dynamic-extension/blob/master/src/main/java/nl/runnable/alfresco/examples/SetDescriptionWebScript.java">Web Script</a> that invokes the action.
* <a href="https://github.com/lfridael/example-dynamic-extension/blob/master/src/main/java/nl/runnable/alfresco/examples/ExampleBehaviour.java">Annotation-based Behaviour example</a>.
* <a href="https://github.com/lfridael/example-dynamic-extension/tree/master/src/main/java/nl/runnable/alfresco/examples">All examples</a>.

More documentation on creating your own extensions will follow
