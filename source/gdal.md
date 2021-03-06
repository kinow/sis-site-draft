---
title: Bridge with GDAL
---

While Apache SIS provides its own map projection engine, some applications may want to also use the Proj.4 library.
One reason may be to use map projection methods not yet supported by Apache SIS,
or for getting the exact same numerical results than Proj.4.
The two libraries can coexist and can be used through the same API,
provided that prerequisites are meet.

**NOTE: this module has been temporarily retired in Apache SIS 1.0. It needs to be upgraded to Proj version 5 and 6 before new release.**

{{< toc >}}

# Prerequisites    {#prerequisites}

Proj.4 needs to be pre-installed; there is no copy embedded in Apache SIS.
Proj.4 can be installed by package managers on most Linux distributions,
or with tools like MacPorts on MacOS
(Windows is not yet supported – see [limitations](#limitations)).
In addition, the `sis-gdal` module must be present on the classpath.
Maven projects can use the following dependency in their `pom.xml` file:

{{< highlight xml >}}
<dependencies>
  <dependency>
    <groupId>org.apache.sis.storage</groupId>
    <artifactId>sis-gdal</artifactId>
    <version>0.8</version>
  </dependency>
</dependencies>
{{< / highlight >}}

# Usage    {#usage}

For instantiating a coordinate reference system (CRS) backed by Proj.4:

{{< highlight java >}}
CoordinateReferenceSystem crs = CRS.forCode("Proj4::+init=epsg:4326");
{{< / highlight >}}

Everything after `"PROJ4::"` is forwarded as-is to the Proj.4 library.
Note that despite the `"epsg"` part in above definition string, the CRS created by above method call is **not**
conform to EPSG:4326 authoritative definition. The string should rather be understood as a Proj.4-specific code.
Proj.4 definitions may differ from authoritative definitions in axis order, axis directions or units of measurement.
To get the authoritative definition, use `CRS.forCode("EPSG::4326")` instead.

For creating a coordinate operation backed by Proj.4, there is nothing special to do;
we can invoke the same method regardless if the CRS are backed by Proj.4 or Apache SIS.
The following code will create a transform backed by Proj.4 if *both* `sourceCRS` and `targetCRS`
were created with `CRS.forCode("Proj4::...")` calls.
Note however that `CoordinateOperation` backed by Proj.4 does not provide metadata about accuracy or domain of validity.

{{< highlight java >}}
CoordinateOperation  op = CRS.findOperation(sourceCRS, targetCRS, null);
MathTransform        mt = op.getMathTransform();
DirectPosition sourcePt = new DirectPosition2D(x, y);
DirectPosition targetPt = mt.transform(sourcePt, null);
{{< / highlight >}}

To verify if a `MathTransform` instance is implemented by Proj.4 or by Apache SIS,
one can look at the _Well Known Text_ (WKT) representation as given by `System.out.println(mt)`.
If the transform is backed by Apache SIS, the output may show any of the parameters listed
in the [coordinate operation methods table](tables/CoordinateOperationMethods.html).
But if the transform is backed by Proj.4, then the transform will always be described
by the `"pj_transform"` method with exactly two parameters: `"srcdefn"` and `"dstdefn"`:

{{< highlight text >}}
PARAM_MT["pj_transform",
  PARAMETER["srcdefn", "+proj=..."],
  PARAMETER["dstdefn", "+proj=..."]]
{{< / highlight >}}

See the [Javadoc](apidocs/org/apache/sis/storage/gdal/package-summary.html) for more information.

# Limitations    {#limitations}

Current Apache SIS version can create CRS backed by Proj.4 only when SIS can map Proj.4 parameters to EPSG parameters.
For example the Proj.4 `+x_0=` parameter is often (but not always) mapped to EPSG _False easting_ parameter.
Future SIS versions may relax the requirement for such mapping.

Current Apache SIS version supports only Linux and MacOS platforms.
A future version will add Windows support.
