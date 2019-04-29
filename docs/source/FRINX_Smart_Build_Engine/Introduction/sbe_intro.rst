
Introduction
============

*  `What is FRINX SBE? <#1-what-is-frinx-sbe>`__
*  `Components Overview <#2-components-overview>`__
*  `Components Description <#3-components-description>`__

What is FRINX SBE?
------------------

The Frinx Smart Build Engine is a continuous integration and continuous deployment system based on Docker containers. It leverages well-known open source projects and comes pre-integrated to get you up and running quicker. The system is container based and can run on one or multiple host VMs.

For an introduction, see our `SBE video <https://www.useloom.com/share/f4ce6cc0e96011e69309454fac1abeab>`__

**It Includes:**  


* Docker containers with open source CI/CD components  
* Scripts to configure, deploy and manage containers and applications  
* Scripts for building the FRINX ODL distribution and sample applications  
* Documentation

**Figure 1: SBE components installation**


.. image:: layers.png
   :target: layers.png
   :alt: Figure 1: Layers


**Figure 2: Providing SBE on multiple hosts**


.. image:: containers.png
   :target: containers.png
   :alt: Figure 2: Containers


**Legend:(G)errit, (J)enkins, (N)exus, (R)edmine, (A)pacheDS**

**Software Build** Building software is an end-to-end process that involves many distinct functions. Some of these functions are described below.

**Version Control** The version control function carries out activities such as workspace creation and updating, baselining and reporting. It creates an environment for the build process to run in and captures metadata about the inputs and outputs of the build process to ensure repeatability and reliability. Tools such as Git help with these tasks, for example by providing a means to tag specific points in history as being important.

**Code Quality** Also known as static program analysis/static code analysis, this function is responsible for checking that developers have adhered to the seven axes of code quality: comments, unit tests, duplication, complexity, coding rules, potential bugs and architecture & design. Ensuring a project has high-quality code results in fewer bugs and influences non-functional requirements such as maintainability, extensibility and readability, which have a direct impact on the ROI for your business.

**Compilation** This is only a small aspect of managing the build process. The compilation function turns source files into directly executable or intermediate objects. Not every project will require this function.While for simple programs the process consists of a single file being compiled, for complex software the source code may consist of many files which may be combined in different ways to produce many different versions.

**Build tools** The process of building a computer program is usually managed by a build tool; a program that coordinates and controls other programs. The build utility typically needs to compile the various files in the correct order. If the source code in a particular file has not changed then it may not need to be recompiled (though this may be necessary if it depends on other files that have changed). Sophisticated build utilities and linkers attempt to refrain from recompiling code unless necessary. This shortens the time required to complete the build. A more complex process may involve other programs producing code or data as part of the build process.

**Continuous Integration** Continuous Integration (CI) is a development practice that requires developers to integrate code into a shared repository several times a day. Each check-in is then verified by an automated build, allowing teams to detect problems early on. The automated build merges all developer working copies to a shared mainline several times a day. Most CI systems allow the running of scripts after a build finishes. In most situations it is possible to write a script to deploy the application to a live test server that everyone can view. A further advance in this approach is continuous deployment, which calls for the software to be deployed directly into production, often with additional automation to prevent defects or regressions.

**Figure 3: Continuous Integration** 
.. image:: ci_flow.png
   :target: ci_flow.png
   :alt: Figure 3: Continuous Integration


Components Overview
-------------------

**List of FRINX SBE components**

.. list-table::
   :header-rows: 1

   * - Component
     - Version
     - Container name
     - Image Name
   * - Apache Directory
     - 2.0.0
     - apacheds
     - frinx/apacheds
   * - SonaType Nexus3
     - 3.0.2
     - nexus
     - frinx/nexus
   * - Jenkins CI
     - 1.642
     - jenkins
     - frinx/jenkins
   * - Gerrit CodeReview
     - 2.13.1
     - gerrit
     - frinx/gerrit
   * - Redmine
     - 3.3.1
     - redmine
     - frinx/redmine
   * - PostgreSQL server
     - 9.5
     - postgresql
     - frinx/postgresql
   * - Nginx http server
     - 1.10.2
     - nginx
     - frinx/nginx
   * - SBE tools
     - 1.0
     - sbe
     - frinx/sbe
   * - SonarQube
     - 6.2
     - sonarqube
     - frinx/sonarqube


Components Description
----------------------

**Apache Directory Server** ApacheDS is an extensible and embeddable directory server entirely written in Java, which has been certified LDAPv3 compatible by the Open Group. Besides LDAP it supports Kerberos 5 and the Change Password Protocol. It has been designed to introduce triggers, stored procedures, queues and views to the world of LDAP which has lacked these rich constructs.

**Gerrit Code Review System** Gerrit is a Web-based code review tool built on top of the Git version control system. It is intended to provide a lightweight framework for reviewing every commit before it is accepted into the code base. Changes are uploaded to Gerrit but don’t actually become a part of the project until they’ve been reviewed and accepted. Gerrit supports the standard open source process of submitting patches which are then reviewed by project members before being applied to the code base. However Gerrit also makes it simple for all committers on a project to ensure that changes are checked over before they are applied. Gerrit captures notes and comments about code changes to enable discussion of the change.

Gerrit is a free, Web-based team code collaboration tool. Software developers in a team can review each other's modifications on their source code using a Web browser and approve or reject those changes. It integrates closely with Git, a distributed version control system. The Gerrit code review system with PostgreSQL and OpenLDAP integration are supported.

**Jenkins CI** Jenkins is an open source continuous integration tool written in Java. Jenkins provides continuous integration services for software development. It is a server-based system running in a servlet container such as Apache Tomcat. The official Jenkins Docker is used in conjunction with additional plugins and scripts in order to integrate with Gerrit. Additional components include:

**Sonatype Nexus** Nexus is a repository manager. It allows you to proxy, collect, and manage your dependencies so that you are not constantly juggling a collection of JARs. It makes it easy to distribute your software. Internally, you configure your build to publish artifacts to Nexus and they then become available to other developers.

**PostgreSQL** PostgreSQL is a powerful, open source object-relational database system. It has more than 15 years of active development and a proven architecture that has earned it a strong reputation for reliability, data integrity, and correctness. It runs on all major operating systems.

**Redmine** is a free and open source, Web-based project management and issue tracking tool. It allows users to manage multiple projects and associated subprojects. It features per project wikis and forums, time tracking, and flexible, role-based access control.

**NGINX** NGINX is a free, open-source, high-performance HTTP server and reverse proxy, as well as an IMAP/POP3 proxy server. NGINX is known for its high performance, stability, rich feature set, simple configuration, and low resource consumption.

**SBE** A useful container which includes all tools and utilities for working with SBE.

**SonarQube** Provides an overview of the overall health of your source code and even more importantly, it highlights issues found with new code. With a Quality Gate set on your project, you can simply fix the Leak and start mechanically improving.
