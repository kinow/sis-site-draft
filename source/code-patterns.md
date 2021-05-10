---
title: Recommended code patterns
---

This page lists some recommended code pattern for developing or using Apache SIS.

{{< toc >}}

# Referencing    {#referencing}

Recommended code pattern when using the `sis-referencing` module.

## Never explicitely swap ordinates for axis order    {#axisOrder}

The [axis order issue](faq.html#axisOrder) causes lot of confusion,
and developers are sometime tempted to swap their ordinate values in order to comply with some expected axis ordering.
It should never be necessary, since the Apache SIS referencing engine manages axis order transparently — provided that
the Coordinate Reference System (CRS) definition is accurate. If a code needs to swap ordinates, this is probably an
indication that the CRS has not been properly defined. Instead than patching the coordinate values, try to make sure
that the _Source CRS_ (associated to the original data) and the _Target CRS_ (the coordinate space where to perform the
work) are properly defined, and let the referencing engine performs the conversion from the source to the target CRS.

# Coverages    {#coverage}

Recommended code pattern when using the `sis-coverage` module.

## Georeference images with affine transforms, _not_ bounding boxes    {#gridToCRS}

Many users define the geographic extent of an image by its corner locations.
This approach may be ambiguous as it does not specify whether the (<var>x</var>,<var>y</var>) axes are interchanged
(see the [axis order issue](faq.html#axisOrder)) or if the <var>y</var> axis is oriented downward.
All images in SIS shall be georeferenced by at least an affine transform (more complex transforms are also possible),
never by a rectangle or bounding box.
In the two-dimensional case, the standard `java.awt.geom.AffineTransform` class can be used.

# International    {#international}

Recommended code pattern for internationalization.

## Specify timezone    {#timezone}

Geospatial data often cover a wide geographic area, spanning many time zones.
Timezone are sometime specified as metadata in the header of data files to be read,
or is sometime fixed to UTC by applications managing world-wide data.
Some Apache SIS objects have `Locale` and `TimeZone` information.
Such locale and timezone are given to `java.text.DateFormat` or `java.util.Calendar` constructors among others.

When reading dates or timestamps from a JDBC database, always use the `ResultSet` method
accepting a `Calendar` argument, when such method is available.
For example prefer the `getTimestamp(int, Calendar)` method instead than `getTimestamp(int)`.
The `Calendar` object should has been created with the appropriate timezone.

## Replace underscores by spaces before sorting    {#sort}

Before to sort programmatic names for human reading, consider replacing all occurrences of the underscore character
(`'_'`) by the space character (`' '`). The ASCII value of the underscore character is greater than `'Z'` but lower
than `'a'`, which sometime produce unexpected sort results.
For example `"Foo_bar"` is sorted _between_ `"FooBar"` and `"Foobar"`.
The space character produces more consistent sort results because its ASCII value is less than any printable character,
so `"Foo bar"` is sorted before both `"FooBar"` and `"Foobar"`.

## Loop over character sequences using code points    {#unicode-loop}

Since Java 1.5, characters are no longer restricted to 16 bits.
Some "characters" are actually represented using two consecutive `char` elements.
Those "characters" are called <cite>code points</cite>.
Consequently, when iterating over characters in a string, the following pattern should be used:

{{< highlight java >}}
for (int i=0; i<string.length();) {
    final int c = string.codePointAt(i);
    // ... do some stuff ...
    i += Character.charCount(c);
}
{{< / highlight >}}

# Logging    {#logging}

Apache SIS uses the `java.util.logging` framework, but with a [mechanism allowing users to redirect
to another framework](http://sis.apache.org/apidocs/org/apache/sis/util/logging/LoggerFactory.html).
The logger names are usually the package name of the class emitting log messages, but not necessarily.
In particular, we do not follow this convention if the class is located in an internal package
(`org.apache.sis.internal.*`) since those packages are considered privates.
In such cases, the logger name should be the package name of the public class invoking the internal methods.
The reason for that rule is that logger names are considered part of the public API,
since developers use them for configuring their logging (verbosity, destination, <i>etc.</i>).

All logging at `Level.INFO` or above shall be targeted to users or administrators, not to developers.
In particular `Level.SEVERE` shall be reserved for critical errors that compromise the application stability —
it shall not be used for exceptions thrown while parsing user data (file or database).

*[CRS]:  Coordinate Reference System
*[JDBC]: Java DataBase Connectivity
*[OGC]:  Open Geospatial Consortium
*[UTC]:  Universal Time Coordinated