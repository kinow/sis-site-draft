---
title: Build from source
---

<p>Apache SIS is built by Maven.
For installing the JAR files in the local Maven repository, execute the following command
from the SIS project root:</p>
<div class="codehilite"><pre>mvn install
</pre></div>


<p>For signing the artifacts and producing distribution files (<code>apache-sis-bin.zip</code> and <code>apache-sis.oxt</code>),
execute the following command.
Note that it requires an OpenPGP (<em>Open Pretty Good Privacy</em>) software for cryptography signatures
(more information on the <a href="release-management-setup.html#generate-key">release management setup</a> page):</p>
<div class="codehilite"><pre>mvn install --activate-profiles apache-release
</pre></div>


<p>The remaining of this page provides more advanced tips for SIS developers.</p>
<div class="toc">
<ul>
<li><a href="#dist">Distribution file</a><ul>
<li><a href="#limitations">Known limitations</a></li>
</ul>
</li>
<li><a href="#build-helper">SIS-specific Maven plugin</a><ul>
<li><a href="#resources">Localized resources compiler</a></li>
<li><a href="#jar-collect">JAR files collector</a></li>
</ul>
</li>
</ul>
</div>
<h1 id="dist">Distribution file<a class="headerlink" href="#dist" title="Permanent link">&para;</a></h1>
<p>The distribution archive is a file with the <code>.zip</code> extension containing most SIS modules except <code>sis-webapp</code>
(because Web applications use an other packaging) together with their dependencies.
However for users convenience, we provide a shell script launching the SIS command line tool.
That shell script, together with other files (<code>README</code>, <code>LICENSE</code>, <i>etc.</i>) are bundled in a ZIP file created as below:</p>
<div class="codehilite"><pre><span class="nb">cd </span>application/sis-console
mvn package org.apache.sis.core:sis-build-helper:dist
</pre></div>


<p>This task is executed automatically if the <code>apache-release</code> profile is activated at build time.
Above command is for the cases where the developer wants the distribution file without rebuilding the whole project.
Optionally, the Apache SIS version can be inserted as a 4th element between <code>sis-build-helper:</code> and <code>:dist</code>
if there is many versions of the plugin in the local repository.</p>
<p>The result will be created in the <code>target/distribution/apache-sis-&lt;version&gt;.zip</code> file.
To test, uncompress in any directory and execute <code>apache-sis-&lt;version&gt;/bin/sis</code>.</p>
<h2 id="limitations">Known limitations<a class="headerlink" href="#limitations" title="Permanent link">&para;</a></h2>
<p>The current plugin implementation has some hard-coded values, especially:</p>
<ul>
<li>The ZIP file content is copied from the <code>application/sis-console/src/main/artifact</code> directory.</li>
<li>The final filename is hard-coded to <code>apache-sis-&lt;version&gt;.zip</code>.</li>
</ul>
<h1 id="build-helper">SIS-specific Maven plugin<a class="headerlink" href="#build-helper" title="Permanent link">&para;</a></h1>
<p>Apache SIS uses a <code>sis-build-helper</code> plugin for SIS-specific tasks and Javadoc customization.
This plugin is used automatically by <code>mvn install</code>. Consequently the remaining of this page
can be safely ignored. This page is provided only as a reference for developers wanting to
take a closer look to SIS <code>pom.xml</code> file.</p>
<h2 id="resources">Localized resources compiler<a class="headerlink" href="#resources" title="Permanent link">&para;</a></h2>
<p>Localized resources are provided in <code>*.properties</code> files as specified by the <code>java.util.PropertyResourceBundle</code> standard class.
However SIS does not use those resources files directly. Instead <code>*.properties</code> files are transformed into binary files having
the same filename but the <code>.utf</code> extension. This conversion is done for efficiency, for convenience (the compiler applies the
<code>java.text.MessageFormat</code> <em>doubled single quotes</em> rule itself), and for compile-time safety.</p>
<p>In addition to generating the <code>*.utf</code> files, the resource compiler may modify the <code>*.java</code> files having the same name than the
resource files. For example given a set of <code>Vocabulary*.properties</code> files (one for each supported language), the compiler will
generate the corresponding <code>Vocabulary*.utf</code> files, then look for a <code>Vocabulary.java</code> source file. If such source file is found
and contains a public static inner class named <code>Keys</code>, then the compiler will rewrite the constants declared in that inner class
with the list of keys found in the <code>Vocabulary*.properties</code> files.</p>
<p>The resource compiler is executed at Maven build time if the <code>pom.xml</code> file contains the following declaration. Note that current
implementation looks only for resources in any package ending with the <code>resources</code> name; all other packages are ignored.</p>
<div class="codehilite"><pre><span class="nt">&lt;build&gt;</span>
  <span class="nt">&lt;plugins&gt;</span>
    <span class="nt">&lt;plugin&gt;</span>
      <span class="nt">&lt;groupId&gt;</span>org.apache.sis.core<span class="nt">&lt;/groupId&gt;</span>
      <span class="nt">&lt;artifactId&gt;</span>sis-build-helper<span class="nt">&lt;/artifactId&gt;</span>
      <span class="nt">&lt;version&gt;</span>${sis.plugin.version}<span class="nt">&lt;/version&gt;</span>
      <span class="nt">&lt;executions&gt;</span>
        <span class="nt">&lt;execution&gt;</span>
          <span class="nt">&lt;goals&gt;</span>
            <span class="nt">&lt;goal&gt;</span>compile-resources<span class="nt">&lt;/goal&gt;</span>
          <span class="nt">&lt;/goals&gt;</span>
        <span class="nt">&lt;/execution&gt;</span>
      <span class="nt">&lt;/executions&gt;</span>
    <span class="nt">&lt;/plugin&gt;</span>
  <span class="nt">&lt;/plugins&gt;</span>
<span class="nt">&lt;/build&gt;</span>
</pre></div>


<p>The resources compilation is part of the build process and does not need to be run explicitly.
However, if necessary the resources compiler can be invoked alone by the following command line
in the module containing the resources to recompile. This is sometime useful for regenerating
the constants in the <code>Keys</code> inner class in a feaster way than building the project.</p>
<div class="codehilite"><pre>mvn org.apache.sis.core:sis-build-helper:compile-resources
</pre></div>


<h2 id="jar-collect">JAR files collector<a class="headerlink" href="#jar-collect" title="Permanent link">&para;</a></h2>
<p>Links or lists all JAR files (including dependencies) in the <code>target/binaries</code> directory of the parent project.
This plugin performs a work similar to the standard Maven assembly plugin work, with the following differences:</p>
<ul>
<li>In multi-modules projects, this plugin does not create anything in the <code>target</code> directory of sub-modules.
     Instead, this plugin groups everything in the <code>target/binaries</code> directory of the parent module.</li>
<li>This plugin does not create any ZIP file. It only links or lists JAR files.
     This plugin uses hard links on platforms that support them,
     so execution of this plugin should be very cheap and consume few disk space.</li>
<li>Dependencies already present in the <code>target/binaries</code> directory are presumed stables and
     are not overwritten. Only artifacts produced by the Maven build are unconditionally overwritten.</li>
</ul>
<p>This plugin can be activated by the following fragment in the parent <code>pom.xml</code> file:</p>
<div class="codehilite"><pre><span class="nt">&lt;build&gt;</span>
  <span class="nt">&lt;plugins&gt;</span>
    <span class="nt">&lt;plugin&gt;</span>
      <span class="nt">&lt;groupId&gt;</span>org.apache.sis.core<span class="nt">&lt;/groupId&gt;</span>
      <span class="nt">&lt;artifactId&gt;</span>sis-build-helper<span class="nt">&lt;/artifactId&gt;</span>
      <span class="nt">&lt;version&gt;</span>${sis.plugin.version}<span class="nt">&lt;/version&gt;</span>
      <span class="nt">&lt;executions&gt;</span>
        <span class="nt">&lt;execution&gt;</span>
          <span class="nt">&lt;goals&gt;</span>
            <span class="nt">&lt;goal&gt;</span>collect-jars<span class="nt">&lt;/goal&gt;</span>
          <span class="nt">&lt;/goals&gt;</span>
        <span class="nt">&lt;/execution&gt;</span>
      <span class="nt">&lt;/executions&gt;</span>
    <span class="nt">&lt;/plugin&gt;</span>
  <span class="nt">&lt;/plugins&gt;</span>
<span class="nt">&lt;/build&gt;</span>
</pre></div>
