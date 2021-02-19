---
title: Bridge with GDAL
---

<p>While Apache SIS provides its own map projection engine, some applications may want to also use the Proj.4 library.
One reason may be to use map projection methods not yet supported by Apache SIS,
or for getting the exact same numerical results than Proj.4.
The two libraries can coexist and can be used through the same API,
provided that prerequisites are meet.</p>
<p><strong>NOTE: this module has been temporarily retired in Apache SIS 1.0. It needs to be upgraded to Proj version 5 and 6 before new release.</strong></p>
<div class="toc">
<ul>
<li><a href="#prerequisites">Prerequisites</a></li>
<li><a href="#usage">Usage</a></li>
<li><a href="#limitations">Limitations</a></li>
</ul>
</div>
<h1 id="prerequisites">Prerequisites<a class="headerlink" href="#prerequisites" title="Permanent link">&para;</a></h1>
<p>Proj.4 needs to be pre-installed; there is no copy embedded in Apache SIS.
Proj.4 can be installed by package managers on most Linux distributions,
or with tools like MacPorts on MacOS
(Windows is not yet supported – see <a href="#limitations">limitations</a>).
In addition, the <code>sis-gdal</code> module must be present on the classpath.
Maven projects can use the following dependency in their <code>pom.xml</code> file:</p>
<div class="codehilite"><pre><span class="nt">&lt;dependencies&gt;</span>
  <span class="nt">&lt;dependency&gt;</span>
    <span class="nt">&lt;groupId&gt;</span>org.apache.sis.storage<span class="nt">&lt;/groupId&gt;</span>
    <span class="nt">&lt;artifactId&gt;</span>sis-gdal<span class="nt">&lt;/artifactId&gt;</span>
    <span class="nt">&lt;version&gt;</span>0.8<span class="nt">&lt;/version&gt;</span>
  <span class="nt">&lt;/dependency&gt;</span>
<span class="nt">&lt;/dependencies&gt;</span>
</pre></div>


<h1 id="usage">Usage<a class="headerlink" href="#usage" title="Permanent link">&para;</a></h1>
<p>For instantiating a coordinate reference system (CRS) backed by Proj.4:</p>
<div class="codehilite"><pre><span class="n">CoordinateReferenceSystem</span> <span class="n">crs</span> <span class="o">=</span> <span class="n">CRS</span><span class="o">.</span><span class="na">forCode</span><span class="o">(</span><span class="s">&quot;Proj4::+init=epsg:4326&quot;</span><span class="o">);</span>
</pre></div>


<p>Everything after <code>"PROJ4::"</code> is forwarded as-is to the Proj.4 library.
Note that despite the <code>"epsg"</code> part in above definition string, the CRS created by above method call is <strong>not</strong>
conform to EPSG:4326 authoritative definition. The string should rather be understood as a Proj.4-specific code.
Proj.4 definitions may differ from authoritative definitions in axis order, axis directions or units of measurement.
To get the authoritative definition, use <code>CRS.forCode("EPSG::4326")</code> instead.</p>
<p>For creating a coordinate operation backed by Proj.4, there is nothing special to do;
we can invoke the same method regardless if the CRS are backed by Proj.4 or Apache SIS.
The following code will create a transform backed by Proj.4 if <em>both</em> <code>sourceCRS</code> and <code>targetCRS</code>
were created with <code>CRS.forCode("Proj4::…")</code> calls.
Note however that <code>CoordinateOperation</code> backed by Proj.4 does not provide metadata about accuracy or domain of validity.</p>
<div class="codehilite"><pre><span class="n">CoordinateOperation</span>  <span class="n">op</span> <span class="o">=</span> <span class="n">CRS</span><span class="o">.</span><span class="na">findOperation</span><span class="o">(</span><span class="n">sourceCRS</span><span class="o">,</span> <span class="n">targetCRS</span><span class="o">,</span> <span class="kc">null</span><span class="o">);</span>
<span class="n">MathTransform</span>        <span class="n">mt</span> <span class="o">=</span> <span class="n">op</span><span class="o">.</span><span class="na">getMathTransform</span><span class="o">();</span>
<span class="n">DirectPosition</span> <span class="n">sourcePt</span> <span class="o">=</span> <span class="k">new</span> <span class="n">DirectPosition2D</span><span class="o">(</span><span class="n">x</span><span class="o">,</span> <span class="n">y</span><span class="o">);</span>
<span class="n">DirectPosition</span> <span class="n">targetPt</span> <span class="o">=</span> <span class="n">mt</span><span class="o">.</span><span class="na">transform</span><span class="o">(</span><span class="n">sourcePt</span><span class="o">,</span> <span class="kc">null</span><span class="o">);</span>
</pre></div>


<p>To verify if a <code>MathTransform</code> instance is implemented by Proj.4 or by Apache SIS,
one can look at the <em>Well Known Text</em> (WKT) representation as given by <code>System.out.println(mt)</code>.
If the transform is backed by Apache SIS, the output may show any of the parameters listed
in the <a href="tables/CoordinateOperationMethods.html">coordinate operation methods table</a>.
But if the transform is backed by Proj.4, then the transform will always be described
by the <code>"pj_transform"</code> method with exactly two parameters: <code>"srcdefn"</code> and <code>"dstdefn"</code>:</p>
<div class="codehilite"><pre>PARAM_MT[&quot;pj_transform&quot;,
  PARAMETER[&quot;srcdefn&quot;, &quot;+proj=…&quot;],
  PARAMETER[&quot;dstdefn&quot;, &quot;+proj=…&quot;]]
</pre></div>


<p>See the <a href="apidocs/org/apache/sis/storage/gdal/package-summary.html">Javadoc</a> for more information.</p>
<h1 id="limitations">Limitations<a class="headerlink" href="#limitations" title="Permanent link">&para;</a></h1>
<p>Current Apache SIS version can create CRS backed by Proj.4 only when SIS can map Proj.4 parameters to EPSG parameters.
For example the Proj.4 <code>+x_0=</code> parameter is often (but not always) mapped to EPSG <em>False easting</em> parameter.
Future SIS versions may relax the requirement for such mapping.</p>
<p>Current Apache SIS version supports only Linux and MacOS platforms.
A future version will add Windows support.</p>
