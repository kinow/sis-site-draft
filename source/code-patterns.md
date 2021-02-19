---
title: Recommended code patterns
---

<p>This page lists some recommended code pattern for developing or using Apache SIS.</p>
<div class="toc">
<ul>
<li><a href="#referencing">Referencing</a><ul>
<li><a href="#axisOrder">Never explicitely swap ordinates for axis order</a></li>
</ul>
</li>
<li><a href="#coverage">Coverages</a><ul>
<li><a href="#gridToCRS">Georeference images with affine transforms, not bounding boxes</a></li>
</ul>
</li>
<li><a href="#international">International</a><ul>
<li><a href="#timezone">Specify timezone</a></li>
<li><a href="#sort">Replace underscores by spaces before sorting</a></li>
<li><a href="#unicode-loop">Loop over character sequences using code points</a></li>
</ul>
</li>
<li><a href="#logging">Logging</a></li>
</ul>
</div>
<h1 id="referencing">Referencing<a class="headerlink" href="#referencing" title="Permanent link">&para;</a></h1>
<p>Recommended code pattern when using the <code>sis-referencing</code> module.</p>
<h2 id="axisOrder">Never explicitely swap ordinates for axis order<a class="headerlink" href="#axisOrder" title="Permanent link">&para;</a></h2>
<p>The <a href="faq.html#axisOrder">axis order issue</a> causes lot of confusion,
and developers are sometime tempted to swap their ordinate values in order to comply with some expected axis ordering.
It should never be necessary, since the Apache SIS referencing engine manages axis order transparently — provided that
the Coordinate Reference System (<abbr title="Coordinate Reference System">CRS</abbr>) definition is accurate. If a code needs to swap ordinates, this is probably an
indication that the <abbr title="Coordinate Reference System">CRS</abbr> has not been properly defined. Instead than patching the coordinate values, try to make sure
that the <em>Source <abbr title="Coordinate Reference System">CRS</abbr></em> (associated to the original data) and the <em>Target <abbr title="Coordinate Reference System">CRS</abbr></em> (the coordinate space where to perform the
work) are properly defined, and let the referencing engine performs the conversion from the source to the target <abbr title="Coordinate Reference System">CRS</abbr>.</p>
<h1 id="coverage">Coverages<a class="headerlink" href="#coverage" title="Permanent link">&para;</a></h1>
<p>Recommended code pattern when using the <code>sis-coverage</code> module.</p>
<h2 id="gridToCRS">Georeference images with affine transforms, <em>not</em> bounding boxes<a class="headerlink" href="#gridToCRS" title="Permanent link">&para;</a></h2>
<p>Many users define the geographic extent of an image by its corner locations.
This approach may be ambiguous as it does not specify whether the (<var>x</var>,<var>y</var>) axes are interchanged
(see the <a href="faq.html#axisOrder">axis order issue</a>) or if the <var>y</var> axis is oriented downward.
All images in SIS shall be georeferenced by at least an affine transform (more complex transforms are also possible),
never by a rectangle or bounding box.
In the two-dimensional case, the standard <code>java.awt.geom.AffineTransform</code> class can be used.</p>
<h1 id="international">International<a class="headerlink" href="#international" title="Permanent link">&para;</a></h1>
<p>Recommended code pattern for internationalization.</p>
<h2 id="timezone">Specify timezone<a class="headerlink" href="#timezone" title="Permanent link">&para;</a></h2>
<p>Geospatial data often cover a wide geographic area, spanning many time zones.
Timezone are sometime specified as metadata in the header of data files to be read,
or is sometime fixed to <abbr title="Universal Time Coordinated">UTC</abbr> by applications managing world-wide data.
Some Apache SIS objects have <code>Locale</code> and <code>TimeZone</code> information.
Such locale and timezone are given to <code>java.text.DateFormat</code> or <code>java.util.Calendar</code> constructors among others.</p>
<p>When reading dates or timestamps from a <abbr title="Java DataBase Connectivity">JDBC</abbr> database, always use the <code>ResultSet</code> method
accepting a <code>Calendar</code> argument, when such method is available.
For example prefer the <code>getTimestamp(int, Calendar)</code> method instead than <code>getTimestamp(int)</code>.
The <code>Calendar</code> object should has been created with the appropriate timezone.</p>
<h2 id="sort">Replace underscores by spaces before sorting<a class="headerlink" href="#sort" title="Permanent link">&para;</a></h2>
<p>Before to sort programmatic names for human reading, consider replacing all occurrences of the underscore character
(<code>'_'</code>) by the space character (<code>' '</code>). The ASCII value of the underscore character is greater than <code>'Z'</code> but lower
than <code>'a'</code>, which sometime produce unexpected sort results.
For example <code>"Foo_bar"</code> is sorted <em>between</em> <code>"FooBar"</code> and <code>"Foobar"</code>.
The space character produces more consistent sort results because its ASCII value is less than any printable character,
so <code>"Foo bar"</code> is sorted before both <code>"FooBar"</code> and <code>"Foobar"</code>.</p>
<h2 id="unicode-loop">Loop over character sequences using code points<a class="headerlink" href="#unicode-loop" title="Permanent link">&para;</a></h2>
<p>Since Java 1.5, characters are no longer restricted to 16 bits.
Some "characters" are actually represented using two consecutive <code>char</code> elements.
Those "characters" are called <cite>code points</cite>.
Consequently, when iterating over characters in a string, the following pattern should be used:</p>
<div class="codehilite"><pre><span class="k">for</span> <span class="o">(</span><span class="kt">int</span> <span class="n">i</span><span class="o">=</span><span class="mi">0</span><span class="o">;</span> <span class="n">i</span><span class="o">&lt;</span><span class="n">string</span><span class="o">.</span><span class="na">length</span><span class="o">();)</span> <span class="o">{</span>
    <span class="kd">final</span> <span class="kt">int</span> <span class="n">c</span> <span class="o">=</span> <span class="n">string</span><span class="o">.</span><span class="na">codePointAt</span><span class="o">(</span><span class="n">i</span><span class="o">);</span>
    <span class="c1">// ... do some stuff ...</span>
    <span class="n">i</span> <span class="o">+=</span> <span class="n">Character</span><span class="o">.</span><span class="na">charCount</span><span class="o">(</span><span class="n">c</span><span class="o">);</span>
<span class="o">}</span>
</pre></div>


<h1 id="logging">Logging<a class="headerlink" href="#logging" title="Permanent link">&para;</a></h1>
<p>Apache SIS uses the <code>java.util.logging</code> framework, but with a <a href="http://sis.apache.org/apidocs/org/apache/sis/util/logging/LoggerFactory.html">mechanism allowing users to redirect
to another framework</a>.
The logger names are usually the package name of the class emitting log messages, but not necessarily.
In particular, we do not follow this convention if the class is located in an internal package
(<code>org.apache.sis.internal.*</code>) since those packages are considered privates.
In such cases, the logger name should be the package name of the public class invoking the internal methods.
The reason for that rule is that logger names are considered part of the public API,
since developers use them for configuring their logging (verbosity, destination, <i>etc.</i>).</p>
<p>All logging at <code>Level.INFO</code> or above shall be targeted to users or administrators, not to developers.
In particular <code>Level.SEVERE</code> shall be reserved for critical errors that compromise the application stability —
it shall not be used for exceptions thrown while parsing user data (file or database).</p>
