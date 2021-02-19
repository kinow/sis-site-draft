---
title: Site management
---

<!-- TODO: remove? -->

<p>This page explains how the Apache SIS web site is created and how to update the site.
The intended audiences are SIS release managers and SIS web site maintainers.</p>
<p>General documentation about Apache Content Management System (CMS) can be found in the <a href="http://www.apache.org/dev/cmsref.html">CMS reference</a> page.
The remaining of this page is specific to the SIS project.</p>
<div class="toc">
<ul>
<li><a href="#directory-layout">Directory layout</a></li>
<li><a href="#content">Content</a></li>
<li><a href="#style-sheets">Style sheets</a><ul>
<li><a href="#bootstrap">Updating bootstrap</a></li>
</ul>
</li>
</ul>
</div>
<h1 id="directory-layout">Directory layout<a class="headerlink" href="#directory-layout" title="Permanent link">&para;</a></h1>
<p>The source files for the Apache SIS web site can be fetched from Subversion using the following command:</p>
<div class="codehilite"><pre>svn checkout https://svn.apache.org/repos/asf/sis/site/trunk site
</pre></div>


<p>The directory layout is as below, omitting version numbers in file names.
The <code>*</code> character stands for an arbitrary amount of files having the given extension.</p>
<div class="codehilite"><pre>site
├─ css
│  ├─ bootstrap.min.css
│  └─ sis.css
├─ img
│  └─ *.png
├─ js
│  ├─ bootstrap.js
│  └─ jquery.js
├─ content
│  └─ *.mdtext
└─ templates
   ├─ single_narrative.html
   └─ skeleton.html
</pre></div>


<p>All files with the <code>.mdtext</code> extension use the Markdown format, which is described there:</p>
<ul>
<li><a href="http://daringfireball.net/projects/markdown/syntax">General markup syntax</a></li>
<li><a href="http://michelf.ca/projects/php-markdown/extra">Extension to the syntax</a></li>
</ul>
<p>New <code>.mdtext</code> files can be created using the <a href="http://svn.apache.org/repos/asf/sis/site/trunk/content/site-management.mdtext">site-management.mdtext</a> file as a template.
Those files can be modified in any text editor and committed with the usual Subversion commands.
Each commit will trig a new site build, which will be visible in the <em>staging</em> area at
<a href="http://sis.staging.apache.org">http://sis.staging.apache.org</a>.
The build progress can be monitored on the <a href="http://ci.apache.org/builders/sis-site-staging">Buildbot</a> page, but they are usually very fast.
Once a staging site has been approved, it can be published to
<a href="http://sis.apache.org">http://sis.apache.org</a> as below:</p>
<ul>
<li>Login to the <a href="https://cms.apache.org/sis/">ASF Content Management System</a>.</li>
<li>Click on <em>Publish sis site</em>.</li>
</ul>
<h1 id="content">Content<a class="headerlink" href="#content" title="Permanent link">&para;</a></h1>
<p>All <code>.mdtext</code> files should start as below (replace the <code>&lt;...&gt;</code> block by the corresponding content):</p>
<div class="codehilite"><pre><span class="n">Title</span><span class="p">:</span>  <span class="o">&lt;</span><span class="n">put</span> <span class="n">the</span> <span class="n">page</span> <span class="n">title</span> <span class="n">here</span><span class="o">&gt;</span>
<span class="n">Notice</span><span class="p">:</span> <span class="o">&lt;</span><span class="n">copy</span> <span class="n">the</span> <span class="n">notice</span> <span class="n">from</span> <span class="n">an</span> <span class="n">existing</span> <span class="n">page</span><span class="o">&gt;</span>
</pre></div>


<p>The title will appear both in the browser window title bar, and as a header at the beginning of the generated HTML page.
The notice should be followed by a short abstract of the page content (typically a single paragraph),
then a <em>Table Of Content</em> to be automatically generated:</p>
<div class="codehilite"><pre><span class="p">[</span><span class="n">TOC</span><span class="p">]</span>
</pre></div>


<p>The first header appears after the table of content.
For each header, and anchor should be specified using the <code>{#...}</code> syntax.
Do not rely on automatically generated anchors, since they may not be stable if some text in the header is modified.
Example:</p>
<div class="codehilite"><pre><span class="n">My</span> <span class="n">first</span> <span class="n">header</span> <span class="n">in</span> <span class="n">my</span> <span class="n">page</span>    <span class="p">{</span>#<span class="n">firstHeader</span><span class="p">}</span>
<span class="o">============================================</span>
</pre></div>


<h1 id="style-sheets">Style sheets<a class="headerlink" href="#style-sheets" title="Permanent link">&para;</a></h1>
<p>The following table lists the style sheets used by Apache SIS.
The Maven and Javadoc style sheets are mentioned for completeness, but are not located on the web site repository.
They are rather located together with the SIS library source code.</p>
<table class="table">
<thead>
<tr>
<th>Page set</th>
<th>File</th>
<th>Purpose</th>
<th>Remark</th>
</tr>
</thead>
<tbody>
<tr>
<td>Web site</td>
<td><code>content/css/bootstrap.min.css</code></td>
<td>Main appearance</td>
<td>Generated from the <a href="http://twitter.github.io/bootstrap/customize.html">Twitter's Bootstrap project</a>.</td>
</tr>
<tr>
<td>Web site</td>
<td><code>http://www.apache.org/css/code.css</code></td>
<td>Code highlighting</td>
<td>Located on the foundation-wide Apache server.</td>
</tr>
<tr>
<td>Web site</td>
<td><code>content/css/sis.css</code></td>
<td>SIS-specific style</td>
<td>Overwrite <code>bootstrap.min.css</code>, edited manually.</td>
</tr>
<tr>
<td>Maven site</td>
<td><code>src/site/resources/css/site.css</code></td>
<td>Maven site</td>
<td>Overwrite <code>maven-base.css</code>, edited manually.</td>
</tr>
<tr>
<td>Javadoc</td>
<td><code>src/main/javadoc/stylesheet.css</code></td>
<td>Javadoc appearance</td>
<td>Copy of javadoc output, edited manually.</td>
</tr>
</tbody>
</table>
<h2 id="bootstrap">Updating bootstrap<a class="headerlink" href="#bootstrap" title="Permanent link">&para;</a></h2>
<p>If the <code>bootstrap.min.css</code> file needs to be updated, then visit the <a href="http://twitter.github.io/bootstrap/customize.html">Bootstrap</a> web site
and select the following options:</p>
<ul>
<li>Scaffolding<ul>
<li>Body type and links</li>
<li>Grid system</li>
<li>Layouts</li>
</ul>
</li>
<li>Components<ul>
<li>Navs, tabs, and pills</li>
<li>Navbar</li>
</ul>
</li>
<li>Miscellaneous<ul>
<li>Wells</li>
</ul>
</li>
<li>Base CSS<ul>
<li>Code and pre</li>
</ul>
</li>
<li>JS Components<ul>
<li>Dropdowns</li>
</ul>
</li>
</ul>
<p>If additional options are needed, then select the <strong>minimal</strong> amount of new options.
Do not select an option that <em>may</em> be used; select only the options that are actually going to be used.
Then:</p>
<ul>
<li>Update this page for listing the new options.</li>
<li>Download the <code>bootstrap.zip</code> file and unzip in the <code>content</code> directory, overwriting the existing files.</li>
<li>Delete <code>css/bootstrap.css</code>, since it is not used.</li>
<li>Add the following comment in <code>css/bootstrap.min.css</code>, just below the copyright header:</li>
</ul>
<p><code>bootstrap.min.css</code>:</p>
<div class="codehilite"><pre><span class="c">/*</span>
<span class="c"> * This file is automatically generated - DO NOT EDIT.</span>
<span class="c"> * See http://sis.apache.org/site-management.html</span>
<span class="c"> */</span>
</pre></div>
