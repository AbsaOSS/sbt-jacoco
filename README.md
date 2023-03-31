# sbt-jacoco - Code Coverage via JaCoCo in sbt

[![Build Status](https://github.com/sbt/sbt-jacoco/workflows/CI/badge.svg)](https://github.com/sbt/sbt-jacoco/actions?workflow=CI)
[![Release Status](https://github.com/sbt/sbt-jacoco/workflows/Release/badge.svg)](https://github.com/sbt/sbt-jacoco/actions?workflow=Release)
[![SBT 1.0 version](https://maven-badges.herokuapp.com/maven-central/com.github.sbt/sbt-jacoco/badge.svg)](https://repo1.maven.org/maven2/com/github/sbt/sbt-jacoco_2.12_1.0/)

This is an [sbt](http://scala-sbt.org/) plugin for code coverage analysis via [JaCoCo](http://www.eclemma.org/jacoco/).
Supports uploading results to [Coveralls](https://coveralls.io), [Codecov](https://codecov.io) and [Codacy](https://www.codacy.com/).

Install the plugin by adding the following to `project/plugins.sbt`:

```scala
addSbtPlugin("com.github.sbt" % "sbt-jacoco" % "<version>")
```

And then execute the plugin with `sbt jacoco`. This will instrument and run the unit tests and output the coverage
metrics:

```
[info] ------- Jacoco Coverage Report --------
[info]
[info] Lines: 66.67% (>= required 0.0%) covered, 2 of 6 missed, OK
[info] Instructions: 83.54% (>= required 0.0%) covered, 13 of 79 missed, OK
[info] Branches: 0% (>= required 0.0%) covered, 0 of 0 missed, OK
[info] Methods: 57.14% (>= required 0.0%) covered, 3 of 7 missed, OK
[info] Complexity: 57.14% (>= required 0.0%) covered, 3 of 7 missed, OK
[info] Class: 50% (>= required 0.0%) covered, 2 of 4 missed, OK
[info]
[info] Check /home/example/jacoco-test/target/scala-2.11/jacoco/report for detailed report
```

A detailed HTML report will also be generated in the directory shown that includes line level details of coverage.

See the [docs](http://scala-sbt.org/sbt-jacoco) for details on configuration options.
-------------------------------------------------------------------------


# Fork differences
## Features
* This repository contains only minimal changes to be able to use this feature.
* **Due to expected usage of this repository is a filtering feature already activated for easier usage!!!**

### Method filtration for `*.scala` files
Feature has been implemented in another repository. See [AbsaOSS JaCoCo fork](https://github.com/AbsaOSS/jacoco).

## How to use locally
Until Scala method filtering solution will be available in official `JaCoCo` library. You can use this workaround.

### How to get library with filtering logic
- Checkout [AbsaOSS JaCoCo fork](https://github.com/AbsaOSS/jacoco) repository.
- Call `mvn install` in repository root dir.
- New snapshots should be available on path `~/.m2/repository/za/co/absa/jacoco/*`
- In this repository root dir call `sbt publishLocal`
- New snapshots should be available on path `~/.ivy2/local/com.github.sbt/sbt-jacoco/scala_2.12/sbt_1.0/`

### How to use library in sbt project
- Because we need access to local maven repository. Create file `repositories` on path `~/.sbt`
- Fill file with this content:
```
[repositories]

local
maven-local
maven-central
```
- Add/Update `plugins.sbt` file.
```
from:
    addSbtPlugin("com.github.sbt" % "sbt-jacoco" % "3.4.0")
to:
    addSbtPlugin("com.github.sbt" % "sbt-jacoco" % "3.4.1-SNAPSHOT")
```

#### If single module project
- See example project [spark-data-standardization](https://github.com/AbsaOSS/spark-data-ç).
- Add this content to the end of `build.sbt` file.
```
// JaCoCo code coverage
Test / jacocoReportSettings := JacocoReportSettings(
  title = s"<Project-name> Jacoco Report - ${scalaVersion.value}",
  formats = Seq(JacocoReportFormats.HTML, JacocoReportFormats.XML)
)
```
- Call `sbt jacoco`.
- Report should be saved on similar path `<project-path>/<project-root>>/target/scala-2.11/jacoco/report/html/`

#### If multi module project
- See example project [spark-commons](https://github.com/AbsaOSS/spark-commons).
- Add this content to the end of `build.sbt` file.
```
import com.github.sbt.jacoco.report.JacocoReportSettings

...

lazy val commonJacocoReportSettings: JacocoReportSettings = JacocoReportSettings(
  formats = Seq(JacocoReportFormats.HTML, JacocoReportFormats.XML)
)

...

"To each module definition:"

  .settings(
    jacocoReportSettings := commonJacocoReportSettings.withTitle("<module-name> Jacoco Report")
  )

```
- Call `sbt jacoco`.
- Report should be saved on similar path `<project-path>/<project-name>/<module-name>/target/spark2.4-jvm-2.11/jacoco/report/html/`
