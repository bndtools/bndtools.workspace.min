# Minimal bnd Workspace

Java 17 / OSGi R8 workspace with curated Maven Central build and runtime catalogs.
There are no application projects in this repository. Catalog entries are available
alternatives, not a list of bundles that should all be installed together.

## Build and Verification

Use a JDK 17 installation:

```sh
./gradlew build
./gradlew dependencyUpdates
```

On Windows, use `gradlew.bat` with the same arguments.

`build` runs `verifyDependencies`, which checks:

- Catalog syntax, duplicate artifacts, and conflicting versions across catalogs.
- Alignment of bnd libraries with the Gradle plugin, and of SLF4J, Byte Buddy, and ASM.
- Availability of all 170 catalog artifacts through the configured bnd repositories.
- OSGi bundle metadata, Java execution-environment requirements, and base class files
  no newer than Java 17. Higher-version multi-release entries are excluded.
- Five combined OSGi resolution profiles: core services, HTTP/Web Console, Gogo,
  test libraries, and ECF. Resolution includes `effective:=active` requirements.
- Individual resolution of all 76 non-framework runtime identities.
- Actual Mockito and EasyMock class mocking in an isolated class loader.

ECF and Eclipse runtime checks use Equinox; other profiles use the default Felix
framework. Consumers of the updated ECF stack should set `-runfw: org.eclipse.osgi`.

`dependencyUpdates` queries Maven Central XML metadata for every selected artifact,
checks that the selected release exists, excludes prereleases, and writes the full
report to `generated/dependency-updates.md`. It does not modify the catalogs, and
does not assume that the highest published version is compatible.

Resolution does not start OSGi applications or test application behavior. Consumers
must still test their own applications, especially when migrating from Mockito 2
to Mockito 5 or opening an existing H2 database with a newer H2 release. No CVE
database scan is included in these checks.

## Dependency Audit: 2026-10-21

All 170 selected Maven coordinates were checked against Maven Central. The original
catalogs contained 157 distinct coordinates. This update changes 33 existing
versions, adds 15 coordinates, removes two obsolete coordinates, and removes a
duplicate User Admin entry. Unlisted existing coordinates, including the OSGi R8
APIs, were already at their latest stable Maven Central versions.

### Build Toolchain

- bnd Gradle plugin and all five `biz.aQute.bnd` catalog artifacts: 7.0.0 to 7.4.0.
- Gradle: 8.1.1 to 8.14.6. Gradle 8 remains compatible with Java 17 and retains
  the existing toolchain major version. A Gradle 9 migration was not tested.
- Regenerated both wrapper scripts and the wrapper JAR at the standard tracked
  path. Removed the old override that generated a JAR in `.gradle-wrapper` instead.
- Distribution SHA-256:
  `7988ed071b2a07900e2ec715fca15c6ab72bce6db433af301fa7fa7e9407bd24`.
- Wrapper JAR SHA-256:
  `ad16bca46cb71bb5887c546860d93992aeee35c1fdaf4a242b0a612375d937e0`.
- Removed unused Sonatype and JFrog snapshot endpoints. All selected versions are
  stable releases; the catalogs use Maven Central.

Previously, `./gradlew build` selected `buildEnvironment` by abbreviation because
the root project did not have a real `build` task. The base plugin now provides a
build lifecycle, and its `check` task runs the dependency verification.

### Updated Coordinates

| Maven Coordinate | Previous | Selected |
| --- | --- | --- |
| biz.aQute.bnd:aQute.libg | 7.0.0 | 7.4.0 |
| biz.aQute.bnd:biz.aQute.bnd.runtime.gogo | 7.0.0 | 7.4.0 |
| biz.aQute.bnd:biz.aQute.bnd.runtime.snapshot | 7.0.0 | 7.4.0 |
| biz.aQute.bnd:biz.aQute.bndlib | 7.0.0 | 7.4.0 |
| biz.aQute.bnd:biz.aQute.launchpad | 7.0.0 | 7.4.0 |
| com.h2database:h2 | 2.2.224 | 2.5.252 |
| commons-fileupload:commons-fileupload | 1.5 | 1.6.0 |
| org.apache.felix:org.apache.felix.http.jetty | 5.1.4 | 5.2.6 |
| org.apache.felix:org.apache.felix.log | 1.3.0 | 1.3.2 |
| org.apache.felix:org.apache.felix.scr | 2.2.6 | 2.2.18 |
| org.apache.felix:org.apache.felix.webconsole | 4.9.6 | 4.9.10 |
| org.apache.servicemix.bundles:org.apache.servicemix.bundles.junit | 4.12_1 | 4.13.2_1 |
| org.assertj:assertj-core | 3.24.2 | 3.27.7 |
| org.easymock:easymock | 5.2.0 | 5.7.0 |
| org.eclipse.ecf:org.eclipse.ecf | 3.11.0 | 3.13.0 |
| org.eclipse.ecf:org.eclipse.ecf.console | 1.4.0 | 1.4.100 |
| org.eclipse.ecf:org.eclipse.ecf.discovery | 5.1.2 | 5.3.0 |
| org.eclipse.ecf:org.eclipse.ecf.osgi.services.distribution | 2.1.700 | 2.2.0 |
| org.eclipse.ecf:org.eclipse.ecf.osgi.services.remoteserviceadmin.console | 1.3.1 | 1.3.200 |
| org.eclipse.ecf:org.eclipse.ecf.provider.jmdns | 4.3.302 | 4.4.0 |
| org.eclipse.ecf:org.eclipse.ecf.remoteservice | 8.14.0 | 8.15.0 |
| org.eclipse.ecf:org.eclipse.ecf.sharedobject | 2.6.200 | 2.7.0 |
| org.eclipse.ecf:org.eclipse.osgi.services.remoteserviceadmin | 1.6.301 | 1.7.0 |
| org.eclipse.platform:org.eclipse.core.jobs | 3.15.100 | 3.15.900 |
| org.eclipse.platform:org.eclipse.equinox.common | 3.15.100 | 3.21.0 |
| org.eclipse.platform:org.eclipse.equinox.concurrent | 1.2.200 | 1.3.500 |
| org.eclipse.platform:org.eclipse.equinox.log.stream | 1.1.100 | 1.2.100 |
| org.eclipse.platform:org.eclipse.equinox.supplement | 1.10.700 | 1.12.300 |
| org.mockito:mockito-core | 2.13.0 | 5.24.0 |
| org.objenesis:objenesis | 2.6 | 3.6 |
| org.slf4j:slf4j-api | 2.0.9 | 2.0.20 |
| org.slf4j:slf4j-nop | 2.0.9 | 2.0.20 |
| org.slf4j:slf4j-simple | 2.0.9 | 2.0.20 |

### Dependency Closure Repairs

| Added Coordinate(s) | Version | Reason |
| --- | --- | --- |
| commons-io:commons-io | 2.22.0 | Replaces the obsolete ServiceMix Commons IO 1.4 wrapper; still exports legacy-compatible package versions required by Web Console. |
| net.bytebuddy:byte-buddy, net.bytebuddy:byte-buddy-agent | 1.18.15 | Mockito 5 prerequisites; the `mockito` build-path macro now includes both. Also satisfies EasyMock's Byte Buddy range. |
| org.apache.aries.spifly:org.apache.aries.spifly.dynamic.bundle | 1.3.8 | Supplies SLF4J 2.0.20's mandatory Service Loader Mediator extender. |
| org.ow2.asm:asm, asm-analysis, asm-commons, asm-tree, asm-util | 9.11.1 | Aligned SPI Fly dependencies. |
| org.apache.felix:org.apache.felix.cm.json | 2.0.8 | Supplies Configurator's previously missing `org.apache.felix.cm.json.io` package. |
| jakarta.json:jakarta.json-api | 2.1.3 | JSON API needed by CM JSON 2.0.8. |
| org.eclipse.parsson:parsson | 1.1.9 | Jakarta JSON implementation. The existing `javax.json` implementation uses a distinct namespace and remains available. |
| org.eclipse.platform:org.eclipse.osgi | 3.24.300 | Equinox framework required by the updated Eclipse/ECF stack. |
| org.owasp.encoder:encoder | 1.6.0 | Previously missing Web Console dependency. |
| org.json:json | 20260814 | Previously missing dependency of the legacy Script Console and User Admin console plugins. |

Removed `org.mockito:mockito-all:1.10.19`: the abandoned monolithic artifact
duplicates Mockito and Objenesis packages and is not a companion to Mockito 5.
Old EnRoute JUnit/Hamcrest wrappers remain available as alternatives, but should
not be installed alongside other providers of the same packages without a reason.

### Deliberate Compatibility Holds

| Artifact | Selected | Latest Stable | Reason |
| --- | --- | --- | --- |
| org.apache.felix.http.servlet-api | 3.0.0 | 6.1.0 | Jetty 5.2.6 requires the Jakarta Servlet 5 contract and packages below 6, plus legacy `javax.servlet` packages. The existing dual API bundle resolves this stack; 6.1.0 does not replace it compatibly. |
| org.apache.felix.webconsole | 4.9.10 | 5.0.18 | Version 5 requires SLF4J packages below 2.0 and the Servlet Whiteboard 2 implementation; incompatible with the selected SLF4J 2 / Jetty 5 stack. Version 4.9.10 resolves with the existing console plugins. |
| org.jline:jline | 3.24.1 | 4.4.8 | Preserve the Gogo JLine 3 API family and a conservative whole-artifact Java 17 baseline. Checked 3.25.1 contains Java 21 base classes; checked 3.26.3 through 3.30.17 candidates contain Java 22 FFM classes outside multi-release directories. Their declared lower Java baseline does not mean every contained class is Java 17-loadable. |

The SLF4J 2.1 alpha and AssertJ 4 milestone advertised as Maven `release` values
are excluded. Selection uses numeric stable versions, not that field alone.

## Sources

- [Maven Central](https://repo.maven.apache.org/maven2/): artifact metadata, POMs,
  and bundle manifests.
- [Gradle release metadata](https://services.gradle.org/versions/all).
- [Gradle distribution checksum](https://services.gradle.org/distributions/gradle-8.14.6-bin.zip.sha256).
- [Gradle wrapper checksum](https://services.gradle.org/distributions/gradle-8.14.6-wrapper.jar.sha256).

The project does not install or upgrade the machine's JDK. Use a current patched
JDK 17 for development and CI; the local validation JVM was Temurin 17.0.8.1.