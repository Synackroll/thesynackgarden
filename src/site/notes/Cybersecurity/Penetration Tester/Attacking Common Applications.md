---
{"dg-publish":true,"permalink":"/cybersecurity/penetration-tester/attacking-common-applications/"}
---


# Setting the Stage

## Introduction to Attacking Common Applications

### Applications

<table class="table table-striped text-left">
<thead>
<tr>
<th><strong>Category</strong></th>
<th><strong>Applications</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="https://enlyft.com/tech/web-content-management" target="_blank" rel="noopener nofollow">Web Content Management</a></td>
<td>Joomla, Drupal, WordPress, DotNetNuke, etc.</td>
</tr>
<tr>
<td><a href="https://enlyft.com/tech/application-servers" target="_blank" rel="noopener nofollow">Application Servers</a></td>
<td>Apache Tomcat, Phusion Passenger, Oracle WebLogic, IBM WebSphere, etc.</td>
</tr>
<tr>
<td><a href="https://enlyft.com/tech/security-information-and-event-management-siem" target="_blank" rel="noopener nofollow">Security Information and Event Management (SIEM)</a></td>
<td>Splunk, Trustwave, LogRhythm, etc.</td>
</tr>
<tr>
<td><a href="https://enlyft.com/tech/network-management" target="_blank" rel="noopener nofollow">Network Management</a></td>
<td>PRTG Network Monitor, ManageEngine Opmanger, etc.</td>
</tr>
<tr>
<td><a href="https://enlyft.com/tech/it-management-software" target="_blank" rel="noopener nofollow">IT Management</a></td>
<td>Nagios, Puppet, Zabbix, ManageEngine ServiceDesk Plus, etc.</td>
</tr>
<tr>
<td><a href="https://enlyft.com/tech/software-frameworks" target="_blank" rel="noopener nofollow">Software Frameworks</a></td>
<td>JBoss, Axis2, etc.</td>
</tr>
<tr>
<td><a href="https://enlyft.com/tech/customer-service-management" target="_blank" rel="noopener nofollow">Customer Service Management</a></td>
<td>osTicket, Zendesk, etc.</td>
</tr>
<tr>
<td><a href="https://enlyft.com/tech/search-engines" target="_blank" rel="noopener nofollow">Search Engines</a></td>
<td>Elasticsearch, Apache Solr, etc.</td>
</tr>
<tr>
<td><a href="https://enlyft.com/tech/software-configuration-management" target="_blank" rel="noopener nofollow">Software Configuration Management</a></td>
<td>Atlassian JIRA, GitHub, GitLab, Bugzilla, Bugsnag, Bitbucket, etc.</td>
</tr>
<tr>
<td><a href="https://enlyft.com/tech/software-development-tools" target="_blank" rel="noopener nofollow">Software Development Tools</a></td>
<td>Jenkins, Atlassian Confluence, phpMyAdmin, etc.</td>
</tr>
<tr>
<td><a href="https://enlyft.com/tech/enterprise-application-integration" target="_blank" rel="noopener nofollow">Enterprise Application Integration</a></td>
<td>Oracle Fusion Middleware, BizTalk Server, Apache ActiveMQ, etc.</td>
</tr>
</tbody>
</table>

### Common Applications

<table class="table table-striped text-left">
<thead>
<tr>
<th>Application</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>WordPress</td>
<td><a href="https://wordpress.org/" target="_blank" rel="noopener nofollow">WordPress</a> is an open-source Content Management System (CMS) that can be used for multiple purposes. It's often used to host blogs and forums. WordPress is highly customizable as well as SEO friendly, which makes it popular among companies. However, its customizability and extensible nature make it prone to vulnerabilities through third-party themes and plugins. WordPress is written in PHP and usually runs on Apache with MySQL as the backend.</td>
</tr>
<tr>
<td>Drupal</td>
<td><a href="https://www.drupal.org/" target="_blank" rel="noopener nofollow">Drupal</a> is another open-source CMS that is popular among companies and developers. Drupal is written in PHP and supports using MySQL or PostgreSQL for the backend. Additionally, SQLite can be used if there's no DBMS installed. Like WordPress, Drupal allows users to enhance their websites through the use of themes and modules.</td>
</tr>
<tr>
<td>Joomla</td>
<td><a href="https://www.joomla.org/" target="_blank" rel="noopener nofollow">Joomla</a> is yet another open-source CMS written in PHP that typically uses MySQL but can be made to run with PostgreSQL or SQLite. Joomla can be used for blogs, discussion forums, e-commerce, and more. Joomla can be customized heavily with themes and extensions and is estimated to be the third most used CMS on the internet after WordPress and Shopify.</td>
</tr>
<tr>
<td>Tomcat</td>
<td><a href="https://tomcat.apache.org/" target="_blank" rel="noopener nofollow">Apache Tomcat</a> is an open-source web server that hosts applications written in Java. Tomcat was initially designed to run Java Servlets and Java Server Pages (JSP) scripts. However, its popularity increased with Java-based frameworks and is now widely used by frameworks such as Spring and tools such as Gradle.</td>
</tr>
<tr>
<td>Jenkins</td>
<td><a href="https://jenkins.io/" target="_blank" rel="noopener nofollow">Jenkins</a> is an open-source automation server written in Java that helps developers build and test their software projects continuously. It is a server-based system that runs in servlet containers such as Tomcat. Over the years, researchers have uncovered various vulnerabilities in Jenkins, including some that allow for remote code execution without requiring authentication.</td>
</tr>
<tr>
<td>Splunk</td>
<td>Splunk is a log analytics tool used to gather, analyze and visualize data. Though not originally intended to be a SIEM tool, Splunk is often used for security monitoring and business analytics. Splunk deployments are often used to house sensitive data and could provide a wealth of information for an attacker if compromised. Historically, Splunk has not suffered from a considerable amount of known vulnerabilities aside from an information disclosure vulnerability (<a href="https://nvd.nist.gov/vuln/detail/CVE-2018-11409" target="_blank" rel="noopener nofollow">CVE-2018-11409</a>), and an authenticated remote code execution vulnerability in very old versions (<a href="https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-4642" target="_blank" rel="noopener nofollow">CVE-2011-4642</a>).</td>
</tr>
<tr>
<td>PRTG Network Monitor</td>
<td><a href="https://www.paessler.com/prtg" target="_blank" rel="noopener nofollow">PRTG Network Monitor</a> is an agentless network monitoring system that can be used to monitor metrics such as uptime, bandwidth usage, and more from a variety of devices such as routers, switches, servers, etc. It utilizes an auto-discovery mode to scan a network and then leverages protocols such as ICMP, WMI, SNMP, and NetFlow to communicate with and gather data from discovered devices. PRTG is written in <a href="https://en.wikipedia.org/wiki/Delphi_(software)" target="_blank" rel="noopener nofollow">Delphi</a>.</td>
</tr>
<tr>
<td>osTicket</td>
<td><a href="https://osticket.com/" target="_blank" rel="noopener nofollow">osTicket</a> is a widely-used open-source support ticketing system. It can be used to manage customer service tickets received via email, phone, and the web interface. osTicket is written in PHP and can run on Apache or IIS with MySQL as the backend.</td>
</tr>
<tr>
<td>GitLab</td>
<td><a href="https://about.gitlab.com/" target="_blank" rel="noopener nofollow">GitLab</a> is an open-source software development platform with a Git repository manager, version control, issue tracking, code review, continuous integration and deployment, and more. It was originally written in Ruby but now utilizes Ruby on Rails, Go, and Vue.js. GitLab offers both community (free) and enterprises versions of the software.</td>
</tr>
</tbody>
</table>

### Module Targets

Quick two lines to add to `/etc/hosts`
```shell-session
$ IP=10.129.42.195
$ printf "%s\t%s\n\n" "$IP" "app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local" | sudo tee -a /etc/hosts
```


## Application Discovery and Enumeration

### Nmap - Web Discovery

```shell-session
$ nmap -p 80,443,8000,8080,8180,8888,1000 --open -oA web_discovery -iL scope_list
```