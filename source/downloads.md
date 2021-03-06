---
title: Apache SIS downloads
---

Apache SIS 1.0 is now available.
See the [release notes](release-notes/1.0.html) for a list of changes since the previous version.

Apache SIS releases are available under the [Apache License, Version 2.0][license].
See the `NOTICE` file contained in each release artifact for applicable copyright attribution notices.

{{< toc >}}

# Download ZIP files    {#bundles}

Apache SIS is distributed in the form of Java source code in a multi-modules Apache Maven project.
For convenience, an aggregated Javadoc and a pre-compiled JAR file are available as separated downloads.
The precompiled JAR file contains most modules and dependencies in a single archive for easier inclusion
in a classpath.

* [Apache SIS 1.0 sources][src] \[[PGP][src-PGP]\] \[[MD5][src-MD5]\] \[[SHA][src-SHA]\]
* [Apache SIS 1.0 javadoc][doc] \[[PGP][doc-PGP]\] \[[MD5][doc-MD5]\] \[[SHA][doc-SHA]\]
* [Apache SIS 1.0 binary][bin]  \[[PGP][bin-PGP]\] \[[MD5][bin-MD5]\] \[[SHA][bin-SHA]\]

## Verify signatures    {#release-gpg}

All downloads can be verified using the Apache SIS code signing [KEYS][keys].
The PGP (_Pretty Good Privacy_) signatures can be verified using any OpenPGP implementation, for example GPG (_GNU Privacy Guard_).
First download the [KEYS][keys] file and the `.asc` signature files for the relevant release packages.
Make sure you get these files from the main distribution directory, rather than from a mirror.
Then verify the signatures using the following:

Using GNU Privacy Guard:

{{< highlight bash >}}
gpg --import KEYS
gpg --verify apache-sis-X.Y.Z.zip.asc
{{< / highlight >}}

Using PGP version 6:

{{< highlight bash >}}
pgp -ka KEYS
pgp apache-sis-X.Y.Z.zip.asc
{{< / highlight >}}

Using PGP version 5:

{{< highlight bash >}}
pgpk -a KEYS
pgpv apache-sis-X.Y.Z.zip.asc
{{< / highlight >}}

# Download as a Maven dependency    {#maven}

An easy approach to integrate Apache SIS into a Java project uses the [Apache Maven][maven]
dependency management tool to automatically obtain the required Java Archives (JAR) files from the network.
Below are examples of declarations in a `pom.xml` file for building a project with a SIS core module.
If running on Java 11 or higher, at least one of the two next dependencies is also required:

{{< highlight xml >}}
<properties>
  <sis.version>1.0</sis.version>
</properties>

<dependencies>
  <dependency>
    <groupId>org.apache.sis.core</groupId>
    <artifactId>sis-referencing</artifactId>
    <version>${sis.version}</version>
  </dependency>
</dependencies>

<!-- The following dependency can be omitted on Java 8 (unconditionally), or
     on Java 9 and 10 if the "--add-modules java.xml.bind" option is used. -->
<dependency>
  <groupId>org.glassfish.jaxb</groupId>
  <artifactId>jaxb-runtime</artifactId>
  <version>2.3.2</version>
  <scope>runtime</scope>
</dependency>

<!-- Above JAXB dependency can be replaced by the following dependency
     if no XML (un)marshalling is wanted. This dependency is lighter. -->
<dependency>
  <groupId>jakarta.xml.bind</groupId>
  <artifactId>jakarta.xml.bind-api</artifactId>
  <version>2.3.2</version>
  <scope>runtime</scope>
</dependency>
{{< / highlight >}}

## Include non-free resources    {#non-free}

The [EPSG geodetic dataset][EPSG] is optional but strongly recommended.
The EPSG dataset is a de-facto standard providing
[thousands of Coordinate Reference System (CRS) definitions](tables/CoordinateReferenceSystems.html)
together with information about how to perform coordinate operations, their accuracies and their domains of validity.
However usage of EPSG dataset requires acceptation of [EPSG terms of use][EPSG-ToU].
If you accept those terms of use, then the following dependency can be added:

{{< highlight xml >}}
<dependencies>
  <dependency>
    <groupId>org.apache.sis.non-free</groupId>
    <artifactId>sis-embedded-data</artifactId>
    <version>${sis.version}</version>
    <scope>runtime</scope>
  </dependency>
</dependencies>
{{< / highlight >}}

Above dependency uses a read-only embedded Derby database.
Note that the need to uncompress the `sis-embedded-data.jar` file
slows down `CRS​.forCode(…)` and `CRS​.findCoordinateOperation(…)` method executions.
For better flexibility and performance, it is also possible to use an uncompressed
and writable Derby database, or to install the EPSG dataset on HSQL or PostgreSQL.
See [How to use EPSG geodetic dataset](epsg.html) page for more information.

[maven]:    http://maven.apache.org/
[keys]:     https://www.apache.org/dist/sis/KEYS
[license]:  http://www.apache.org/licenses/LICENSE-2.0
[src]:      http://www.apache.org/dyn/closer.cgi/sis/1.0/apache-sis-1.0-src.zip
[doc]:      http://www.apache.org/dyn/closer.cgi/sis/1.0/apache-sis-1.0-doc.zip
[bin]:      http://www.apache.org/dyn/closer.cgi/sis/1.0/apache-sis-1.0-bin.zip
[src-PGP]:  https://www.apache.org/dist/sis/1.0/apache-sis-1.0-src.zip.asc
[doc-PGP]:  https://www.apache.org/dist/sis/1.0/apache-sis-1.0-doc.zip.asc
[bin-PGP]:  https://www.apache.org/dist/sis/1.0/apache-sis-1.0-bin.zip.asc
[src-MD5]:  https://www.apache.org/dist/sis/1.0/apache-sis-1.0-src.zip.md5
[doc-MD5]:  https://www.apache.org/dist/sis/1.0/apache-sis-1.0-doc.zip.md5
[bin-MD5]:  https://www.apache.org/dist/sis/1.0/apache-sis-1.0-bin.zip.md5
[src-SHA]:  https://www.apache.org/dist/sis/1.0/apache-sis-1.0-src.zip.sha
[doc-SHA]:  https://www.apache.org/dist/sis/1.0/apache-sis-1.0-doc.zip.sha
[bin-SHA]:  https://www.apache.org/dist/sis/1.0/apache-sis-1.0-bin.zip.sha
[EPSG]:     https://epsg.org/
[EPSG-ToU]: https://epsg.org/terms-of-use.html
